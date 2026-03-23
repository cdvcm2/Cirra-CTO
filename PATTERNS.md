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
