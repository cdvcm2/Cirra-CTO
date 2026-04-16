# PATTERNS.md
**Cirra Edge — Universal Engineering Patterns**
Proven solutions to recurring problems. Tagged by stack. Applied automatically by the Senior Engineer.

A pattern earns its place here by solving the same class of problem in 2+ projects, or by saving 2+ hours in a single incident.

**Repo:** cdvcm2/cirra-cto | Group CTO write access only. Sub-CTOs apply, never edit.

---

## How to Read This File

Each pattern has:
- **Stack tag** — which technologies it applies to
- **Proven in** — which projects validated it
- **The solution** — specific, actionable, copy-pasteable
- **Failure modes** — what breaks if you deviate

---

## [Any Stack] Parallel Agent Protocol

**Stack:** Any (Cursor + any repo)
**Proven in:** liftcore (April 2026)
**Time saved:** Full session — QA catches bugs before deploy instead of after

**Problem:** QA ran sequentially after each brief. Every session had a build→wait→QA→fix cycle. Bugs caught after commit instead of before.

**Solution:** Every brief runs exactly two Cursor agents simultaneously.

Agent 1 (Builder): executes the brief. Reads files. Builds. Commits. Pushes. Posts SUMMARY block.

Agent 2 (Auditor): triggered with one line the moment Agent 1 posts SUMMARY. Cursor Rules expands it into full audit automatically.

**Setup:**

Step 1 — Create Cursor rules directory:
```bash
mkdir -p .cursor/rules
```

Step 2 — Create `session-opener.mdc` (alwaysApply: true)
Copy from `cirra-cto/cursor-rules-templates/session-opener.mdc`.
Fill project-specific values: supabase ref, shared doc paths, single client path, AuthProvider path.

Step 3 — Create `qa-agent.mdc` (alwaysApply: false)
Copy from `cirra-cto/cursor-rules-templates/qa-agent.mdc`.
No project-specific changes needed — universal.

Step 4 — Add GitHub Actions QA workflow:
Copy `cirra-cto/cursor-rules-templates/qa-audit.yml` to `.github/workflows/qa-audit.yml`.
Add GitHub Actions secrets: VITE_SUPABASE_URL, VITE_SUPABASE_PUBLISHABLE_KEY.

**Agent 2 trigger line** (paste once, rules do the rest):
```
QA AUDIT — run full combined audit on the brief that just completed.
Read all files in the last git commit before starting.
```

**Agent 2 audit scope:**
- Security: service role, PII, RLS, secrets
- Architecture: single client, single AuthProvider, no any
- UI: loading/empty/error/disabled states
- Performance: N+1, Realtime scope, indexes, bundle
- State machine: no raw status writes, terminal states
- Types: tsc --noEmit, npm run build, npm audit
- Schema briefs: also migration safety + type regen

**Verdict:** PASS TO DEPLOY or BLOCKED. Nothing else. No merge without PASS TO DEPLOY.

### Failure modes
- `qa-agent.mdc` created as 0 bytes if heredoc not closed properly. Verify: `wc -c .cursor/rules/qa-agent.mdc`
- `alwaysApply: true` on qa-agent causes it to run on every task. Keep it false. Trigger manually.
- `session-opener.mdc` must be project-specific. Do not copy liftcore version without updating supabase ref and doc paths.
- Running QA after the session ends means you did it wrong. Agent 2 runs simultaneously, always.

---

## [Supabase] Sequential Reference Generation

**Stack:** Supabase PostgreSQL
**Proven in:** lift-partner-portal (April 2026)
**Time saved:** Prevents race conditions on customer-facing sequential refs

**Problem:** Multiple concurrent requests generate duplicate reference numbers. Postgres sequences are not suitable for customer-facing refs that need business logic (prefix, year, padding).

**Root cause:** Non-atomic ref generation — two requests read the same counter value simultaneously.

**Solution:** SECURITY DEFINER function with SELECT FOR UPDATE prevents race conditions:

```sql
-- Reference counter table (one row per ref type)
CREATE TABLE IF NOT EXISTS pp_reference_counters (
  type text PRIMARY KEY,
  counter_value integer NOT NULL DEFAULT 0
);

-- Seed counter types
INSERT INTO pp_reference_counters (type, counter_value)
VALUES
  ('REQ', 4010),
  ('BKG', 4010),
  ('PAR', 0),
  ('PAX', 0)
ON CONFLICT (type) DO NOTHING;

-- Atomic ref generation function
CREATE OR REPLACE FUNCTION generate_pp_ref(ref_type text)
RETURNS text
LANGUAGE plpgsql
SECURITY DEFINER
AS $$
DECLARE
  next_val integer;
BEGIN
  -- SELECT FOR UPDATE locks the row — prevents race conditions
  SELECT counter_value + 1 INTO next_val
  FROM pp_reference_counters
  WHERE type = ref_type
  FOR UPDATE;

  UPDATE pp_reference_counters
  SET counter_value = next_val
  WHERE type = ref_type;

  -- Format: TYPE-SG-YYYY-NNNNN (customer-facing) or TYPE-SG-NNNNNN (internal)
  RETURN ref_type || '-SG-' || to_char(now(), 'YYYY') || '-' || lpad(next_val::text, 5, '0');
END;
$$;
```

**Usage in trigger:**
```sql
CREATE OR REPLACE FUNCTION trg_assign_ref()
RETURNS trigger
LANGUAGE plpgsql
SECURITY DEFINER
AS $$
BEGIN
  IF NEW.status = 'confirmed' AND OLD.status != 'confirmed' AND NEW.bkg_ref IS NULL THEN
    NEW.bkg_ref := generate_pp_ref('BKG');
  END IF;
  RETURN NEW;
END;
$$;
```

### Failure modes
- Never use Postgres `SEQUENCE` for customer-facing refs — no business logic, no race protection
- Without `FOR UPDATE` — concurrent requests read same counter → duplicate refs
- Without `SECURITY DEFINER` — function subject to RLS → fails for non-admin callers
- Always verify counter table seeded after migration: `SELECT * FROM pp_reference_counters;`

---

## [Supabase + React] Google OAuth — Implicit Flow

**Stack:** Supabase Auth + React + React Router BrowserRouter
**Proven in:** lift-staff-portal (Mar 2026)
**Time saved:** 3+ hours

**Problem:** After Google OAuth redirect, React app never receives the session. `onAuthStateChange` fires with `INITIAL_SESSION session: false`.

**Root cause:** PKCE flow returns `?code=` in query string. React Router BrowserRouter navigates away before Supabase can exchange the code. Session is created server-side but React never receives it.

**Solution:**

```typescript
// src/integrations/supabase/client.ts
export const supabase = createClient(SUPABASE_URL, SUPABASE_KEY, {
  auth: {
    storage: localStorage,
    persistSession: true,
    autoRefreshToken: true,
    detectSessionInUrl: true,
    flowType: 'implicit',  // ← CRITICAL: never 'pkce'
  }
});
```

```typescript
// Sign in function
await supabase.auth.signInWithOAuth({
  provider: 'google',
  options: {
    redirectTo: `${window.location.origin}/auth/callback`,
  },
});
```

```typescript
// src/pages/AuthCallback.tsx — public route, no ProtectedRoute
function AuthCallback() {
  const navigate = useNavigate();
  useEffect(() => {
    const { data: { subscription } } = supabase.auth.onAuthStateChange((event, session) => {
      if (event === 'SIGNED_IN' && session) {
        navigate('/home', { replace: true });
      }
    });
    return () => subscription.unsubscribe();
  }, [navigate]);
  return <LoadingSpinner />;
}
```

**Google Cloud Console:**
- Authorized redirect URIs: `https://[ref].supabase.co/auth/v1/callback` ONLY
- Never add app URL to redirect URIs

**Supabase Dashboard:**
- Authentication → URL Configuration → Redirect URLs
- Add `[domain]/auth/callback` for every environment (localhost, staging, production)
- Site URL = app base URL (not Supabase URL)

**userLoading pattern — mandatory:**
```typescript
// AuthContext.tsx
const [loading, setLoading] = useState(true);         // auth session loading
const [staffLoading, setStaffLoading] = useState(true); // profile fetch loading

// ProtectedRoute must wait for BOTH
if (loading || staffLoading) return <LoadingSpinner />;
if (!session) return <Navigate to="/login" />;
```

### Failure modes
- `pkce` flow: session lost — React Router navigates before code exchange. Silent failure.
- `hd=` param: 403 on non-Google-Workspace domains. Handle domain check in AuthContext instead.
- OAuth in Lovable preview: blocked by Google (iframe restriction). Always test in real browser tab.
- Blank page redirect loop: ProtectedRoute redirecting before `staffLoading` resolves. Always add separate loading state for profile fetch.
- Domain restriction via `hd=` only works for Google Workspace orgs. Never use for personal Gmail domains.

---

## [Supabase] RLS Role Check Function

**Stack:** Supabase PostgreSQL
**Proven in:** lift-staff-portal (Mar 2026)
**Time saved:** 30+ minutes

**Problem:** 500 error on staff/users table queries after adding admin RLS policy.

**Root cause:** RLS policy with raw subquery on same table causes infinite recursion.

```sql
-- ❌ NEVER — causes infinite recursion → 500 error
CREATE POLICY "admin_read_all" ON staff
  FOR SELECT USING (
    EXISTS (SELECT 1 FROM staff WHERE staff.id = auth.uid() AND role = 'admin')
  );
```

**Solution:** Create a `SECURITY DEFINER` function that bypasses RLS for the role check:

```sql
-- ✅ Create once per project
CREATE OR REPLACE FUNCTION get_my_role()
RETURNS text
LANGUAGE sql
SECURITY DEFINER
STABLE
AS $$
  SELECT role FROM staff WHERE id = auth.uid()
$$;

-- ✅ Use in all role-based RLS policies
CREATE POLICY "admin_read_all" ON staff
  FOR SELECT USING (get_my_role() = 'admin');
```

### Failure modes
- Not creating the function first → policies fail with "function does not exist"
- Using raw subquery in any role-based RLS → 500 on that table
- Forgetting `SECURITY DEFINER` → function still subject to RLS → recursion

---

## [Supabase] Edge Function Deployment

**Stack:** Supabase Edge Functions (Deno)
**Proven in:** lift-staff-portal (Mar 2026), liftcore (Apr 2026)

**Problem:** Edge function returns 401 or 502 with no useful error message. Or `--linked` flag not supported on current CLI version.

**Solution checklist:**

```bash
# Deploy with correct flags — use --project-ref not --linked
npx supabase functions deploy [name] --project-ref [ref] --no-verify-jwt

# Set secrets (must redeploy after setting)
npx supabase secrets set ANTHROPIC_API_KEY=sk-...
npx supabase secrets list  # verify

# Check logs
# Supabase dashboard → Functions → [name] → Invocations tab
```

**Diagnosis table:**
| Error | Cause | Fix |
|-------|-------|-----|
| 401 | Missing `--no-verify-jwt` | Redeploy with flag |
| 502 no body | API key missing or service out of credits | Check secrets, check provider account |
| 502 with body | Runtime error in function | Check Invocations log |
| --linked not supported | CLI version mismatch | Use --project-ref [ref] instead |

**Call from frontend:**
```typescript
// ✅ Use invoke() — auto-passes user JWT
const { data, error } = await supabase.functions.invoke('function-name', {
  body: { key: value }
});

// ❌ Never raw fetch() for authenticated functions
```

### Failure modes
- `--linked` not supported on all CLI versions — always use `--project-ref [ref]`
- Secrets set but function not redeployed — secrets only available after next deploy
- Service role key not set in Supabase secrets → invite flows silently fail

---

## [Any Stack] Environment Variable Safety

**Stack:** Any
**Proven in:** Multiple projects

**Rules:**
1. Never use `.env` — always `.env.local`
2. Both `.env` and `.env.local` in `.gitignore` — set before first commit
3. Verify: `git check-ignore -v .env.local`

**If `.env` was committed:**
```bash
git rm --cached .env
echo ".env" >> .gitignore
git commit -m "fix: remove .env from tracking"
# Purge from history
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch .env' \
  --prune-empty --tag-name-filter cat -- --all
git push origin --force --all
```

**Frontend (Vite):**
```typescript
const url = import.meta.env.VITE_SUPABASE_URL; // ✅
const url = 'https://xyz.supabase.co';          // ❌ never hardcode
```

**Edge functions (Deno):**
```typescript
const key = Deno.env.get('ANTHROPIC_API_KEY'); // ✅
```

---

## [React] Auth Loading State Pattern

**Stack:** React + any auth provider
**Proven in:** lift-staff-portal (Mar 2026)

**Problem:** Blank page or redirect loop after successful auth. App redirects to login even though session exists.

**Root cause:** Single `loading` state used for both auth session check AND user profile fetch. ProtectedRoute sees `loading: false` before profile is fetched → `staff: null` → redirects to login.

**Solution:**
```typescript
// AuthContext — two separate loading states
const [loading, setLoading] = useState(true);          // auth.getSession()
const [staffLoading, setStaffLoading] = useState(true); // fetchUserProfile()

// ProtectedRoute
function ProtectedRoute({ children }) {
  const { session, staff, loading, staffLoading } = useAuth();

  if (loading || staffLoading) {
    return <div className="loading-spinner" />;
  }

  if (!session) return <Navigate to="/login" replace />;
  if (!staff) return <Navigate to="/login" replace />;

  return children;
}
```

---

*PATTERNS.md | Cirra Edge | cdvcm2/cirra-cto | Last updated: April 2026*
*Group CTO write access only. Sub-CTOs: apply, never edit.*
*Promote from sub-CTO reports when a pattern solves the same problem in 2+ projects.*

---

## [Any Stack] Four-Agent Protocol

**Stack:** Any (Cursor + any repo)
**Proven in:** liftcore (April 2026)
**Replaces:** Parallel Agent Protocol (two-agent)

**Problem:** Two agents (build + QA) missed architecture drift, doc staleness, and cross-brief regressions. QA caught syntax but not design problems.

**Solution:** Four agents with separated concerns. See FOUR_AGENT_PROTOCOL.md for full specification.

* BUILD AGENT: writes code, one brief at a time, never stacks unverified
* QA AGENT: 16-point checklist, PASS TO DEPLOY or BLOCKED
* DOCUMENT AGENT: .md files only, session-end mandatory
* REVIEW AGENT: architecture health, triggers before major features

**Key rule:** Never stack unverified briefs. One at a time. Verified. Then next.

### Failure modes
* Skipping QA Agent on "simple" briefs — no brief is simple enough to skip QA
* Document Agent not running at session end — HANDOVER.md goes stale
* Review Agent never triggered — architecture drifts silently
* Build Agent making architecture decisions instead of escalating

---

## [Any Stack] Pre-Brief Decision Block

**Stack:** Any
**Proven in:** liftcore (April 2026)
**Time saved:** Prevents most agent failures before they happen

**Problem:** Agents fail because of bad framing, not bad typing. Briefs go to Cursor without clear problem statements, without naming affected files, without rollback plans.

**Solution:** 10 mandatory fields before every brief. See THE_CIRRA_WAY Section 02.

1. Problem (one sentence)
2. Root cause / hypothesis (CONFIRMED or HYPOTHESIS)
3. Severity (P0/P1/P2/P3)
4. Files affected (explicit list)
5. Do NOT touch (explicit list)
6. Test / evidence required
7. Rollback plan (executable in under 5 min)
8. Definition of success (observable outcome)
9. Known unknowns
10. Why now

For P2/P3: items 7 and 9 can be brief. The block still forces framing.

### Failure modes
* Skipping the block for "urgent" fixes — urgent fixes need framing most
* Filling it with generic text ("fix the bug") — each field must be specific
* Not listing files in item 4 — Build Agent touches wrong files

---

## [Supabase] Duplicate Prevention via Partial Unique Index

**Stack:** Supabase PostgreSQL
**Proven in:** liftcore (April 2026)
**Time saved:** Prevented duplicate vehicle_trips from racing assign/confirm operations

**Problem:** Concurrent assign and confirm operations created duplicate vehicle_trips for the same vehicle on the same date.

**Root cause:** Assign creates a draft without wave_id. Confirm looks for a draft WITH wave_id. Both find nothing and create new records.

**Solution:** Partial unique index that excludes terminal states:

```sql
CREATE UNIQUE INDEX idx_unique_active_vehicle_trip
ON vehicle_trips (vehicle_id, trip_date)
WHERE status NOT IN ('completed', 'cancelled');
```

This allows completed/cancelled duplicates (historical records) while preventing active duplicates.

### Failure modes
* Using a full unique index — blocks historical records
* Not including all terminal states in the WHERE clause — edge cases slip through
* Applying to tables without clear terminal states — requires a state machine first
