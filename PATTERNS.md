# PATTERNS.md
Cirra Edge — Universal Engineering Patterns

## [Supabase + React] Google OAuth — Implicit Flow
Stack: Supabase Auth + React + React Router
Proven in: lift-staff-portal (Mar 2026) — 3 hours lost before finding this

Root cause: PKCE returns ?code= in query string. React Router navigates away before Supabase exchanges the code. Session created server-side but client never receives it.

Solution:
- flowType: 'implicit' in Supabase client — NEVER pkce
- redirectTo: window.location.origin + '/auth/callback'
- Dedicated /auth/callback public route listening for SIGNED_IN event
- Google Cloud: Supabase callback URL in redirect URIs ONLY
- Supabase dashboard: add /auth/callback to Redirect URLs for every environment
- Domain restriction: AuthContext checks email.endsWith() — NOT via hd= param
- Separate staffLoading from auth loading. ProtectedRoute waits for BOTH.

Failure modes:
- pkce: session lost silently, user shows as signed base but not in app
- hd= param: 403 on non-Google-Workspace domains
- OAuth in Lovable iframe: blocked by Google — always test in real browser tab
- Blank page loop: ProtectedRoute redirecting before staffLoading resolves

---

## [Supabase] RLS Role Check Function
Stack: Supabase PostgreSQL
Proven in: lift-staff-portal (Mar 2026) — 30 minutes lost

Root cause: Raw subquery on same table inside RLS policy causes infinite recursion.

NEVER:
  EXISTS (SELECT 1 FROM staff WHERE staff.id = auth.uid() AND role = 'admin')

ALWAYS:
  CREATE OR REPLACE FUNCTION get_my_role()
  RETURNS text LANGUAGE sql SECURITY DEFINER STABLE AS $$
    SELECT role FROM staff WHERE id = auth.uid()
  $$;
  Then use: get_my_role() = 'admin' in all RLS policies.

---

## [Supabase] Partner RLS — get_my_partner_id()
Stack: Supabase PostgreSQL
Proven in: lift-staff-portal (Mar 2026)

Same recursion risk as get_my_role() — never subquery staff table in RLS.

  CREATE OR REPLACE FUNCTION get_my_partner_id()
  RETURNS uuid LANGUAGEURITY DEFINER STABLE AS $$
    SELECT partner_id FROM staff WHERE id = auth.uid()
  $$;
  Then use: introduced_by = get_my_partner_id() in partner RLS policies.

---

## [Supabase] Edge Functions
Stack: Supabase Edge Functions (Deno)

- Deploy with --no-verify-jwt for frontend-called functions
- 502 no error = API key missing or service out of credits
- 401 = missing --no-verify-jwt
- Use supabase.functions.invoke() not raw fetch() — auto-passes JWT
- Set secrets: npx supabase secrets set KEY=value then redeploy

---

## [Any Stack] Environment Variables
- Never commit .env — use .env.local
- Both in .gitignore — set before first commit
- If committed: git rm --cached .env + git filter-branch to purge history
- Frontend (Vite): import.meta.env.VITE_* only
- Edge functions: Deno.env.get('KEY')

---

## [React] Auth Loading State Pattern
Problem: Blank page or redirect loop after successful auth.
Root cause: Single loading state for both session check and profile fetch.
ProtectedRoute sees loading=falsee staff profile loaded, staff=null, redirects.

Solution: Two separate loading states.
  const [loading, setLoading] = useState(true);
  const [staffLoading, setStaffLoading] = useState(true);
  ProtectedRoute waits for BOTH before evaluating redirect.

---

*PATTERNS.md | Cirra Edge | Last updated: March 2026*
*Promote from LEARNINGS.md when a pattern solves the same problem in 2+ projects.*


## [React + Supabase] Role Migration Safety

**Stack:** React + Supabase Auth
**Proven in:** lift-staff-portal (Mar 2026)
**Time saved:** unknown — silent failure, could have cost hours in production

**Problem:** After migrating role enum values (e.g. admin to superadmin), UI features disappear silently. No error thrown. App appears to work but role-gated sections are hidden.

**Root cause:** AuthContext role checks hardcoded to old string value. Migration changes the value in DB but app still checks the old string - silently returns false.

**Solution:** Before any role enum migration:

1. Grep for every role check in the codebase:
   grep -rn "role === " src/
   grep -rn "isAdmin" src/

2. Update every check to handle both old and new values during transition:
   isAdmin = role === 'admin' || role === 'superadmin'

3. After migration is stable and all users have new role - clean up to single value check.

4. Always verify AuthContext role checks match current enum values in DB.

**Failure modes:**
- Migrating role values without grepping first - silent permission failures
- Checking role in multiple places - missing one causes partial breakage
- No error thrown - developer assumes feature is working when it is not

## [Cloudflare Pages] Bun Lockfile Before First Deploy

**Stack:** Cloudflare Pages + any Vite project
**Proven in:** lift-staff-portal (Mar 2026)
**Time saved:** 45 minutes

**Problem:** First Cloudflare Pages deployment fails with bun install --frozen-lockfile error.

**Root cause:** Cloudflare Pages auto-detects bun as package manager when no lockfile exists. Runs with --frozen-lockfile flag which requires a committed lockfile.

**Solution:**
```bash
# Before first Cloudflare Pages deployment
bun install
git add bun.lock
git commit -m "chore: add bun lockfile for Cloudflare Pages"
git push
```

**Also note:** The .pages.dev subdomain is set permanently at project creation. Renaming the project later only changes the display name. Always confirm the project name before creating.

**Failure modes:**
- No bun.lock committed = deployment fails immediately on first push
- Wrong project name at creation = subdomain cannot be changed

---

## [Any Stack] GitHub Multi-Account SSH

**Stack:** Any — affects developers with multiple GitHub accounts
**Proven in:** lift-staff-portal (Mar 2026)

**Problem:** git push fails with permission denied or pushes to wrong account when multiple GitHub accounts are configured locally.

**Root cause:** git@github.com always uses the default SSH key. When repos belong to different GitHub accounts, the wrong key is used.

**Solution:**
```bash
# ~/.ssh/config
Host github-lift
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_lift

Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_personal
```
```bash
# Set remote to use named host not github.com
git remote set-url origin git@github-lift:LIFT-Mobility/repo-name.git
```

**Failure modes:**
- Using git@github.com when multiple accounts active = wrong key used = permission denied or wrong account push

---

## [React + shadcn] Split Panel Layout

**Stack:** React + shadcn/ui
**Proven in:** lift-staff-portal (Mar 2026)
**Time saved:** 3 Cursor iterations

**Problem:** shadcn Sheet used for side panel. Sheet renders over page content — table behind it becomes invisible and non-interactive.

**Root cause:** Sheet is built on Radix Dialog. It is an overlay, not a layout component. It cannot coexist with visible background content.

**Solution:**
```tsx
// For split-panel where background content must remain visible
<div className="flex h-full">
  <div className="flex-1 overflow-auto">
    {/* Table or list */}
  </div>
  {panelOpen && (
    <div className="w-[40vw] min-w-[480px] border-l overflow-auto">
      {/* Detail panel */}
    </div>
  )}
</div>

// Never use Sheet for this pattern
// Sheet = overlay (modal behaviour)
// Fixed div = layout component (split behaviour)
```

**Failure modes:**
- Sheet blocks interaction with background content
- Sheet does not support URL-synced state naturally
- Sheet animation fights with panel open/close transitions

---

## [Supabase] Edge Function Secrets — Never Use SUPABASE_ Prefix

**Stack:** Supabase Edge Functions
**Proven in:** lift-mobility-core (Mar 2026)
**Time saved:** 30 minutes

**Problem:** Custom secret named SUPABASE_SERVICE_ROLE_KEY rejected by Supabase secrets UI with no clear error message.

**Root cause:** Supabase reserves the SUPABASE_ namespace internally. Any secret with this prefix is rejected.

**Solution:**
```bash
# Never use SUPABASE_ prefix for custom secrets
npx supabase secrets set SERVICE_ROLE_KEY=your-key   # correct
npx supabase secrets set SUPABASE_SERVICE_ROLE_KEY=your-key  # rejected

# In Edge Function
const key = Deno.env.get('SERVICE_ROLE_KEY')  # correct
```

**Failure modes:**
- Silent rejection or unclear error in Supabase dashboard
- Function deploys successfully but secret is never set

---

## [Supabase] PostgREST Nested Joins Fail on RLS-Protected Tables

**Stack:** Supabase PostgreSQL + PostgREST
**Proven in:** lift-mobility-core (Mar 2026)
**Time saved:** 45 minutes

**Problem:** PostgREST nested join returns 500 error when the joined table has RLS policies using auth.uid().

**Root cause:** auth.uid() context may not propagate correctly through PostgREST nested joins. RLS check fails silently, returning 500.

**Solution:**
```typescript
// Never nested join on RLS-protected tables
// Wrong
const { data } = await supabase
  .from('wave_occurrences')
  .select('*, waves(*)')  // waves has RLS — will 500

// Correct — fetch separately
const { data: occurrences } = await supabase
  .from('wave_occurrences')
  .select('*')

const { data: waves } = await supabase
  .from('waves')
  .select('*')
  .in('id', occurrences.map(o => o.wave_id))
```

**Failure modes:**
- 500 with no useful error message
- Only fails when RLS is enabled — passes in dev if RLS not yet applied
- Nested joins on non-RLS tables work fine — easy to miss pattern

---

## [Any Stack] Parallel Agent Protocol

Stack: Any (Cursor + any repo)
Proven in: liftcore (April 2026)
Time saved: QA catches bugs before deploy instead of after

Pattern:
Every brief runs exactly two Cursor agents simultaneously.
Agent 1 (Builder): executes the brief. Reads files. Builds.
Commits. Pushes. Posts SUMMARY block.
Agent 2 (Auditor): triggered with one line the moment Agent 1
posts SUMMARY. Cursor Rules expands it into full 20-point audit
automatically.

Setup:
1. Create .cursor/rules/session-opener.mdc (alwaysApply: true)
   Project-specific: supabase ref, shared doc paths,
   single client path, AuthProvider path, output format.
2. Create .cursor/rules/qa-agent.mdc (alwaysApply: false)
   Universal: security, architecture, UI, performance,
   state machine, types checks. Copy from cirra-cto template.
3. Add .github/workflows/qa-audit.yml
   Runs tsc, build, audit, security greps on every push.
   Copy from cirra-cto template.

Agent 2 trigger line (paste once, rules do the rest):
"QA AUDIT — run full combined audit on the brief that just
completed. Read all files in the last git commit."

Failure modes:
- qa-agent.mdc created as 0 bytes if heredoc not closed
  properly. Verify with: wc -c .cursor/rules/qa-agent.mdc
- alwaysApply: true on qa-agent causes it to run on every
  task not just audits. Keep it false, trigger manually.
- session-opener.mdc must be project-specific. Do not copy
  liftcore version without updating supabase ref and doc paths.

Verdict options: PASS TO DEPLOY or BLOCKED. Nothing else.
