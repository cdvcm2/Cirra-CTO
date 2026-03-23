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
