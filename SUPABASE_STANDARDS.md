# SUPABASE STANDARDS
**Version 1.0 | Cirra Edge | April 2026**
Every Cirra product uses Supabase. These rules are universal.

---

## Security

* **RLS is mandatory on every table.** No exceptions. Enable RLS immediately after table creation.
* **SECURITY DEFINER views** for column-level access control (not column-level GRANTs).
* **get_my_role()** for all role-based RLS policies. Never raw subquery on the same table — causes infinite recursion and 500 errors.
* **verify_jwt=false** is only acceptable for PIN-based driver auth. Must be documented as an intentional architectural decision in CLAUDE.md.
* **Edge Function secrets** must NOT use the SUPABASE_ prefix. Supabase reserves this prefix internally.

## Authentication

* **Google OAuth: implicit flow only.** Never PKCE. PKCE returns ?code= in query string; React Router navigates before Supabase can exchange the code.
* **Invite-gated email/password** for partner portals (no self-registration).
* **PIN-only auth** for drivers: via dedicated DriverAuthContext, no Supabase Auth dependency.
* **Separate loading states:** loading (auth session) and staffLoading (profile fetch) must be separate. ProtectedRoute waits for BOTH.

## Schema

* **Migrations via SQL Editor only.** Never CLI migrations. Never push via CLI.
* **Every migration is idempotent:** IF NOT EXISTS, IF EXISTS, OR REPLACE.
* **Types regenerated after every schema change:** npx supabase gen types typescript --project-id [ref] > src/integrations/supabase/types.ts
* **FK ON DELETE behavior explicit** on every foreign key.
* **Reference numbers generated atomically** via RPC with SELECT FOR UPDATE. Never in application code.

## Known Gotchas

* **get_my_role() returns null in SQL Editor.** This is expected behavior — SQL Editor runs as service role, not as an authenticated user. Not a bug.
* **PostgREST nested joins fail with 500** on RLS-protected tables. Use SECURITY DEFINER wrapper functions instead.
* **Edge Functions deployed without --no-verify-jwt** return 401 for authenticated frontend calls. Always include the flag.
* **supabase.functions.invoke()** auto-passes JWT. Never use raw fetch() for authenticated Edge Functions.
* **Use --project-ref not --linked** for Edge Function deployment. --linked is not supported on all CLI versions.
* **Env vars in Vite are baked at startup.** Must restart dev server after .env.local changes. Duplicate keys: first match wins.

## Data Residency

All Cirra Supabase projects must be in Singapore (ap-southeast-1). Confirm region for every new project in Dashboard > Settings > General.

## Multi-Portal Architecture

When multiple portals share a single Supabase project:
* Use prefix segregation (pp_ for partner portal tables)
* Each prefix has independent RLS policies
* No cross-prefix RLS dependencies
* passengers/customer data in the appropriate prefix (pp_booking_requests.passengers jsonb for partner portal)
* Consent versioning: data_sharing_consent_at + data_sharing_consent_by must fire before portal_access = true

---

*SUPABASE_STANDARDS.md | Version 1.0 | Cirra Edge | April 2026*
