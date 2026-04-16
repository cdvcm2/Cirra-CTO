# LEARNINGS.md
**Cirra Edge — Cross-Project Failure Log**
Append-only. Most recent first. Never delete entries.

**Repo:** cdvcm2/cirra-cto | Group CTO write access only.
Sub-CTOs surface candidates in their 3-5 day report. Group CTO promotes HIGH items here.

Format: Date | Project | Problem | Root cause | Fix | Time lost | Pattern promoted

---

## How Entries Get Here

Sub-CTOs flag `LEARNINGS CANDIDATE: yes` in their Cursor output.
They include it in their 3-5 day report to Group CTO (Section 06 of the report template).
Group CTO scores HIGH/MEDIUM/LOW and promotes HIGH items here.
Sub-CTOs never write directly to this file.

---

## 2026-04-02 — Parallel agent protocol not used until late in session

Project: liftcore | Time lost: full session running QA sequentially instead of in parallel

Problem: QA ran after each brief completed, not during the next brief. Every session had a sequential build→wait→QA→fix cycle instead of build+QA running simultaneously.

Root cause: Two-agent pattern was never established as the default. Discovered mid-session in Brief 096 and only adopted from that point onwards.

Fix: Cursor Rules files (.mdc) in .cursor/rules/ handle the session opener and QA audit automatically. Two agents run in parallel from the first brief of every session. Agent 2 trigger is one line: "QA AUDIT — run full combined audit on the brief that just completed. Read all files in the last git commit."

Never do: Start a session without both agent windows open. Agent 2 starts the moment Agent 1 posts its SUMMARY block.

Pattern promoted: yes — [Any Stack] Parallel Agent Protocol

---

## 2026-04-02 — CLAUDE.md tracking phantom CRITICAL issue

Project: lift-partner-portal | Time lost: investigation time + doc correction

Problem: CLAUDE.md tracked a CRITICAL open issue (AuthCallback setTimeout hack) that never existed in the codebase. The sub-CTO was operating under the assumption that a real bug existed when it did not.

Root cause: CRITICAL issue was written into CLAUDE.md from a verbal description without verifying against actual code. No grep or file reference was required.

Fix: grep confirmed no hack existed. CLAUDE.md corrected. Protocol established: any CRITICAL issue added to CLAUDE.md must include the file path and line number confirming it exists in code. No CRITICAL without a grep reference.

Never do: Add a CRITICAL issue to CLAUDE.md without first running grep to confirm it exists. A phantom CRITICAL is a liability — agents make wrong decisions based on it.

Pattern promoted: no — encoded as protocol in CLAUDE.md template and THE_CIRRA_WAY.md

---

## 2026-04-02 — CI failure masked by docs commits

Project: liftcore | Time lost: unknown — multiple sessions affected

Problem: Multiple docs commits were pushed on top of CI that had been failing since Brief 094b without checking CI status first. The lodash HIGH CVE was the blocker — it was present but invisible because nobody checked `gh run list` before starting work.

Root cause: No session-start CI check in doctrine. It was assumed CI was green unless something visibly broke.

Fix: Added mandatory CI check to ENGINEERING_DOCTRINE.md and THE_CIRRA_WAY.md. Run `gh run list --limit 5` before starting any session. Fix red before writing new code. Never build on red.

Never do: Start a session without verifying CI is green. A lodash CVE or TS error left unaddressed compounds across every subsequent brief.

Pattern promoted: no — encoded as Rule 6 in Parallel Agent Protocol section of THE_CIRRA_WAY.md

---

## 2026-04-02 — Supabase types drift after schema migration

Project: liftcore | Time lost: silent type inference errors across session

Problem: Types not regenerated after migrations 095a and 095c. Four new columns (depot_lat, depot_lng, default_shift_start, default_shift_end) got silent any inference in TypeScript.

Root cause: Type regen was not included as a mandatory step after schema migrations. It was treated as optional cleanup rather than a required gate.

Fix: Added as mandatory step in QA_DOCTRINE.md automatic fail conditions. Agent 2 must verify types are regenerated after any schema brief. Command: `npx supabase gen types typescript --project-id [ref] > src/integrations/supabase/types.ts` then `npx tsc --noEmit`.

Never do: Treat type regen as optional after a schema migration. Stale types cause silent failures that are hard to trace.

Pattern promoted: no — added to Agent 2 audit scope for schema briefs

---

## 2026-03-22 — Google OAuth session lost after redirect

Project: lift-staff-portal | Stack: Supabase + React + React Router | Time lost: 3 hours

Problem: After Google OAuth redirect back to localhost:8081, the React app never received the session. `onAuthStateChange` fired with `INITIAL_SESSION session: false`. The user showed as authenticated in Supabase dashboard (Last signed in updating) but app stayed on login page.

Root cause: PKCE flow was configured. PKCE returns `?code=` in the query string. React Router BrowserRouter performed a navigation that stripped the query parameter before Supabase could exchange the code for a session. Session was created server-side but client never received it.

Fix: Switched to implicit flow (`flowType: 'implicit'`). Added dedicated `/auth/callback` public route that listens for `SIGNED_IN` event before navigating to app. Added `detectSessionInUrl: true` to Supabase client config.

Never do: Use `flowType: 'pkce'` with React Router BrowserRouter. Never use `redirectTo: window.location.origin` — always point to `/auth/callback`.

Pattern promoted: yes — [Supabase + React] Google OAuth — Implicit Flow

---

## 2026-03-22 — RLS infinite recursion on staff table

Project: lift-staff-portal | Stack: Supabase PostgreSQL | Time lost: 30 minutes

Problem: 500 error on all queries to the staff table after adding an admin RLS policy. Error appeared across all roles, not just admin.

Root cause: The `admin_read_all_staff` policy used a raw subquery: `EXISTS (SELECT 1 FROM staff WHERE staff.id = auth.uid() AND role = 'admin')`. This queries the staff table from within an RLS policy on the staff table → infinite recursion → 500.

Fix: Created `get_my_role()` as a `SECURITY DEFINER` function. SECURITY DEFINER bypasses RLS for the function itself, breaking the recursion. All role-based RLS policies now call `get_my_role() = 'admin'`.

Never do: Raw subquery on the same table inside that table's RLS policy.

Pattern promoted: yes — [Supabase] RLS Role Check Function

---

## 2026-03-22 — .env committed to git history

Project: lift-staff-portal | Stack: Any | Time lost: 20 minutes

Problem: `.env` file containing Supabase keys and Anthropic API key was committed to git before `.gitignore` was set up properly.

Root cause: Project was initialised without creating `.gitignore` first. First `git add .` captured `.env`.

Fix: `git rm --cached .env`, updated `.gitignore`, then `git filter-branch` to purge from full history, force push.

Never do: Run `git add .` before `.gitignore` exists. Always commit `.gitignore` as the absolute first commit.

Pattern promoted: yes — [Any Stack] Environment Variable Safety

---

## 2026-03-22 — Auth blank page redirect loop

Project: lift-staff-portal | Stack: React + Supabase | Time lost: 45 minutes

Problem: After successful login, app showed blank page then immediately redirected back to /login. Session existed in Supabase but ProtectedRoute kept redirecting.

Root cause: Single `loading` state used for both `auth.getSession()` and `fetchOrCreateStaff()`. `loading` was set to false after session check, but `staff` was still null during async profile fetch. ProtectedRoute saw `loading: false, staff: null` → redirected.

Fix: Added separate `staffLoading` state. ProtectedRoute waits for BOTH `loading` and `staffLoading` to be false before evaluating redirect conditions.

Never do: Use a single loading state for both session check and profile fetch when ProtectedRoute checks both.

Pattern promoted: yes — [React] Auth Loading State Pattern

---

## 2026-03-22 — OAuth 403 in Lovable preview iframe

Project: lift-staff-portal | Stack: Google OAuth | Time lost: 45 minutes

Problem: Google OAuth returned 403 when triggered from Lovable's preview panel. Worked fine in standalone browser tab.

Root cause: Lovable preview runs inside an iframe. Google blocks OAuth flows initiated from iframes (`sec-fetch-dest: iframe` header triggers Google's security policy).

Fix: Always test OAuth in a standalone browser tab. Never use Lovable preview for auth flow testing.

Never do: Test any OAuth flow inside an embedded preview tool.

Pattern promoted: no — too specific to tooling, covered by Google OAuth pattern note

---

*LEARNINGS.md | Cirra Edge | cdvcm2/cirra-cto | Append-only — never delete entries*
*Group CTO promotes from sub-CTO reports. Sub-CTOs never write here directly.*

---

## 2026-04-12 — Edge Function secrets with SUPABASE_ prefix

Project: liftcore | Time lost: investigation + potential silent failure

Problem: Edge Function secret named SUPABASE_SERVICE_ROLE_KEY. Supabase reserves the SUPABASE_ prefix for internal environment variables. The secret may be silently overridden or ignored.

Root cause: The prefix restriction is documented in Supabase but not widely known. Sub-CTO handover plan explicitly instructed setting SUPABASE_SERVICE_ROLE_KEY as the secret name.

Fix: Use SERVICE_ROLE_KEY (without SUPABASE_ prefix) for all Edge Function secrets. Update Edge Function code to read Deno.env.get("SERVICE_ROLE_KEY"). Documented in SECRETS_AND_ENV_SOP.md and SUPABASE_STANDARDS.md.

Never do: Name any Edge Function secret with the SUPABASE_ prefix. Supabase reserves this namespace.

Pattern promoted: no — encoded in SECRETS_AND_ENV_SOP.md and SUPABASE_STANDARDS.md

---

## 2026-04-12 — Unverified briefs sent without receiving output

Project: liftcore | Time lost: unknown — trust gap in handover

Problem: Briefs 217 (Enterprise ETA) and 218 (as-any cleanup) were sent to Cursor but output was never received. The sub-CTO continued sending new briefs without verifying these completed. Discovered during CTO handover.

Root cause: No enforcement mechanism for the "never stack unverified briefs" rule. The rule existed in doctrine but was violated under time pressure during a demo sprint.

Fix: Added Section 04 (Unverified Briefs) as a mandatory section in SUB_CTO_REPORT_TEMPLATE v3.0. Added escalation trigger in THE_CIRRA_WAY v5.0: "Unverified brief discovered → all new briefs halt until verified." Group CTO now checks for unverified briefs in every report intake.

Never do: Send a new brief before confirming the previous brief's output was received and verified. This is the #1 process failure mode.

Pattern promoted: no — encoded as escalation trigger in THE_CIRRA_WAY v5.0

---

## 2026-04-12 — Handover documents reference wrong infrastructure

Project: liftcore | Time lost: review and correction cycle

Problem: New sub-CTO's PDPA Execution Plan referenced Vercel (not Cloudflare Pages), Google Maps (not OneMap), and an SMS provider that doesn't exist in the stack. These errors would have propagated into compliance documents sent to NKF.

Root cause: The PDPA plan was drafted without cross-referencing the actual infrastructure. No verification step existed in the handover process.

Fix: SUB_CTO_HANDOVER_PROTOCOL.md now requires Group CTO review of all handover documents before the incoming sub-CTO is approved to execute. Every infrastructure reference must be verified against the actual stack.

Never do: Accept infrastructure claims in handover documents at face value. Verify every service, every URL, every provider name against what is actually deployed.

Pattern promoted: no — encoded in SUB_CTO_HANDOVER_PROTOCOL.md

---

## 2026-04-09 — Service time as first-class DB concept

Project: liftcore | Time lost: multiple sessions debugging ETA anomalies

Problem: ETA calculations were inconsistent because service time (time spent at each stop for pickup/dropoff) was hardcoded in frontend logic, not stored in the database. Different components used different assumptions.

Root cause: Service time was treated as a display calculation, not as data. No single source of truth.

Fix: Added service_time_minutes column to stops table with mobility-based defaults. ETA calculation now reads service time from DB. Every stop has an explicit, editable service time.

Never do: Hardcode operational parameters in frontend logic. If it affects calculations, it belongs in the database.

Pattern promoted: yes — to be added to PATTERNS.md as DB-as-source-of-truth for operational parameters
