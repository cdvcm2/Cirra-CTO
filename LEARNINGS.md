# LEARNINGS.md
Cirra Edge — Cross-Project Failure Log
Append-only. Most recent first. Never delete entries.

---

## 2026-04-03 — TypeScript `include` does not limit the dependency graph
Project: lift-mobility-core | Stack: Vite + React + TypeScript | Time lost: ~1 h

Problem: Attempted to scope CI `tsc` with a short `include` list while retaining `App.tsx` as a root. Errors remained ~500 — finance, operator, legacy enterprise modules were still type-checked.

Root cause: **`include`** selects entry roots only; the compiler always follows **imports**. `exclude` does not remove imported modules.

Fix: Separate **`tsconfig.ci.json`** with roots that **omit** the monolithic shell when it imports broken subtrees; or fix all consumers. Document TODO to revert to full program after cleanup.

Never do: Promise "scoped `include`" as equivalent to scoped type safety without verifying the import graph.

Pattern promoted: yes — **dual tsconfig** (CI slice vs full app) as an explicit debt marker.

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

## 2026-03-25 — Anchor time must come from DB not frontend input

Project: lift-mobility-core | Stack: Supabase + React | Time lost: 0 — caught in design

Problem: Wave anchor time was initially accepted as a free frontend parameter passed to Edge Function.

Root cause: Free timing parameters on time-sensitive workflows allow frontend to pass arbitrary values, corrupting schedule integrity. For medical transport this is a patient safety risk. For any regulated workflow it is a data integrity risk.

Fix: Edge Function reads anchor time directly from DB (waves.scheduled_arrival_time / scheduled_departure_time). Frontend passes wave ID only.

Never do: Accept time-sensitive parameters from frontend when the correct value exists in the DB. Always read from source of truth.

Pattern promoted: no — captured as data integrity principle

---

## 2026-03-25 — Cloudflare Pages fails with frozen-lockfile when bun.lock missing

Project: lift-staff-portal | Stack: Cloudflare Pages + Vite | Time lost: 45 minutes

Problem: Cloudflare Pages deployment failed on first deploy with bun install --frozen-lockfile error.

Root cause: Cloudflare Pages auto-detects bun as package manager if no bun.lock exists. Runs bun install --frozen-lockfile which fails without a committed lockfile.

Fix: Run bun install locally before first Cloudflare Pages deployment. Commit bun.lock to repo.

Never do: Push to Cloudflare Pages for the first time without a committed bun.lock in the repo.

Pattern promoted: yes — [Cloudflare Pages] Bun Lockfile

---

## 2026-03-25 — Cloudflare Pages subdomain is permanent at project creation

Project: lift-staff-portal | Stack: Cloudflare Pages | Time lost: 15 minutes

Problem: Renamed Cloudflare Pages project but subdomain did not change. Old subdomain remained active.

Root cause: Cloudflare Pages sets the .pages.dev subdomain at project creation. Renaming the project only changes the display name — subdomain is permanent.

Fix: Nothing — cannot be changed. Prevention only.

Never do: Create a Cloudflare Pages project without confirming the project name is correct. The subdomain cannot be changed after creation.

Pattern promoted: no — prevention only, covered in Cloudflare Pages pattern

---

## 2026-03-25 — shadcn Sheet is an overlay not a side panel

Project: lift-staff-portal | Stack: React + shadcn/ui | Time lost: 3 Cursor iterations

Problem: Used shadcn Sheet component for split-panel layout. Sheet rendered over the table content instead of beside it.

Root cause: Sheet is built on Radix Dialog — it is an overlay that sits above page content. It cannot coexist with scrollable content beside it.

Fix: Replace Sheet with a fixed-position div for any layout requiring content to remain visible alongside the panel.

Never do: Use shadcn Sheet for split-panel layouts where background content must remain visible and interactive.

Pattern promoted: yes — [React + shadcn] Split Panel Layout

---

## 2026-03-25 — Brief constraints must account for cross-file implications

Project: lift-staff-portal | Stack: Cursor | Time lost: 1 session

Problem: Brief constraints stated do not modify App.tsx and do not modify MyPipelineDetail. These conflicted with URL-synced panel routing which required both files.

Root cause: CTO wrote constraints without tracing all files the task would touch. Cursor followed constraints and could not complete the task correctly.

Fix: Before writing any constraint in a brief, trace every file the task touches. Only constrain files that genuinely must not change.

Never do: Write do not modify constraints without first verifying the task can be completed without touching that file.

Pattern promoted: no — CTO discipline, covered in pre-brief stress test

---

## 2026-03-25 — Sentry auth token exposed in chat

Project: lift-staff-portal | Stack: Any | Time lost: 10 minutes + token rotation

Problem: Sentry auth token appeared in terminal output pasted into chat during Sentry setup.

Root cause: CTO asked user to paste full terminal output. Terminal output contained the auth token.

Fix: Rotate the exposed token immediately. Never ask user to paste full terminal output. Grep for key names only, never values. Instruct user to copy from terminal directly to browser.

Never do: Ask user to paste terminal output that may contain secrets. Always be specific about what to copy.

Pattern promoted: no — operational discipline

---

## 2026-04-02 — Parallel agent protocol not used until late

Project: liftcore | Time lost: full session running QA
sequentially instead of in parallel

Problem: QA ran after each brief completed, not during the
next brief. Every session had a sequential build→wait→QA→fix
cycle instead of build+QA running simultaneously.

Root cause: Two-agent pattern was never established as the
default. Discovered mid-session and only adopted from
Brief 096 onwards.

Fix: Cursor Rules files (.mdc) in .cursor/rules/ handle
the session opener and QA audit automatically. Two agents
run in parallel from the first brief of every session.

Never do: Start a session without both agent windows open.
Agent 2 starts the moment Agent 1 posts its SUMMARY block.

Pattern promoted: yes — [Any Stack] Parallel Agent Protocol
