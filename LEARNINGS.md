# LEARNINGS.md
Cirra Edge — Cross-Project Failure Log
Append-only. Most recent first. Never delete entries.

---

## 2026-03-22 — Google OAuth session lost after redirect
Project: lift-staff-portal | Stack: Supabase + React + React Router | Time lost: 3 hours

Problem: After Google OAuth redirect, React app never received the session. onAuthStateChange fired with INITIAL_SESSION session: false. User showed as signed in on Supabase but app stayed on login page.

Root cause: PKCE flow configured. React Router BrowserRouter navigated away before Supabase could exchange the code for a session.

Fix: Switch to implicit flow. Add dedicated /auth/callback route that waits for SIGNED_IN before navigating. Point redirectTo to /auth/callback not just origin.

Never do: Use flowType: pkce with React Router BrowserRouter.

Pattern promoted: yes — Google OAuth Implicit Flow

---

## 2026-03-22 — RLS infinite recursion on staff table
Project: lift-staff- Stack: Supabase PostgreSQL | Time lost: 30 minutes

Problem: 500 error on all staff table queries after adding admin RLS policy.

Root cause: Policy used raw subquery on the same table it was protecting. Infinite recursion.

Fix: Created get_my_role() as SECURITY DEFINER function. All role checks use this function instead of raw subquery.

Never do: Raw subquery on same table inside that table's RLS policy.

Pattern promoted: yes — RLS Role Check Function

---

## 2026-03-22 — .env committed to git history
Project: lift-staff-portal | Time lost: 20 minutes

Problem: .env file with Supabase keys committed before .gitignore was set up.

Root cause: git add . ran before .gitignore existed.

Fix: git rm --cached .env, update .gitignore, git filter-branch to purge history, force push.

Never do: Run git add . before .gitignore exists. Always commit .gitignore first.

Pattern promoted: yes — Environment Variable Safety

---

## 2026-03-22 — Auth blank page redirect loop
Project: lift-staff-portal | Stack+ Supabase | Time lost: 45 minutes

Problem: After login, app showed blank page then redirected back to login. Session existed but ProtectedRoute kept redirecting.

Root cause: Single loading state for both auth.getSession() and fetchOrCreateStaff(). loading set to false before staff profile fetched. ProtectedRoute saw loading=false, staff=null, redirected.

Fix: Added separate staffLoading state. ProtectedRoute waits for BOTH.

Never do: Use single loading state for session check and profile fetch when ProtectedRoute checks both.

Pattern promoted: yes — Auth Loading State Pattern

---

## 2026-03-22 — OAuth 403 in Lovable preview iframe
Project: lift-staff-portal | Time lost: 45 minutes

Problem: Google OAuth returned 403 when triggered from Lovable preview panel.

Root cause: Lovable preview runs in iframe. Google blocks OAuth in iframes.

Fix: Always test OAuth in standalone browser tab.

Never do: Test OAuth inside embedded preview tools.

Pattern promoted: no — covered by OAuth pattern notes

---RNINGS.md | Cirra Edge | Append-only — never delete entries*

## 2026-03-23 — Role migration breaks isAdmin silently

Project: lift-staff-portal | Stack: React + Supabase | Time lost: unknown

Problem: After migrating role values from admin to superadmin, sidebar sections disappeared with no error thrown. App appeared to work but admin features were hidden.

Root cause: AuthContext isAdmin check was hardcoded as role === 'admin'. After migration, role value became 'superadmin' — check silently returned false.

Fix: isAdmin = role === 'admin' || role === 'superadmin'

Never do: Migrate role enum values without auditing every role check in AuthContext first. Always grep for the old role string before running the migration.

Pattern promoted: yes — [React + Supabase] Role Migration Safety

---

## 2026-03-23 — get_my_role() returns null in Supabase SQL editor

Project: lift-staff-portal | Stack: Supabase PostgreSQL | Time lost: unknown

Problem: get_my_role() returned null when tested in Supabase SQL editor. Caused confusion about whether the function was broken.

Root cause: SQL editor runs as postgres superuser. auth.uid() returns null — not an authenticated user context. Function works correctly in app via real JWT.

Fix: Never test auth.uid()-dependent functions in SQL editor. Test via app or Edge Function with real JWT.

Never do: Assume get_my_role() is broken because it returns null in SQL editor. Expected behaviour — not a bug.

Pattern promoted: no — covered as diagnostic note in RLS Role Check Function pattern


## 2026-03-23 — Role migration breaks isAdmin silently

Project: lift-staff-portal | Stack: React + Supabase | Time lost: unknown

Problem: After migrating role values from admin to superadmin, sidebar sections disappeared with no error thrown. App appeared to work but admin features were hidden.

Root cause: AuthContext isAdmin check was hardcoded as role === 'admin'. After migration, role value became 'superadmin' - check silently returned false.

Fix: isAdmin = role === 'admin' || role === 'superadmin'

Never do: Migrate role enum values without auditing every role check in AuthContext first. Always grep for the old role string before running the migration.

Pattern promoted: yes - [React + Supabase] Role Migration Safety

---

## 2026-03-23 — get_my_role() returns null in Supabase SQL editor

Project: lift-staff-portal | Stack: Supabase PostgreSQL | Time lost: unknown

Problem: get_my_role() returned null when tested in Supabase SQL editor. Caused confusion about whether the function was broken.

Root cause: SQL editor runs as postgres superuser. auth.uid() returns null - not an authenticated user context. Function works correctly in app via real JWT.

Fix: Never test auth.uid()-dependent functions in SQL editor. Test via app or Edge Function with real JWT.

Never do: Assume get_my_role() is broken because it returns null in SQL editor. Expected behaviour - not a bug.

Pattern promoted: no - covered as diagnostic note in RLS Role Check Function pattern
