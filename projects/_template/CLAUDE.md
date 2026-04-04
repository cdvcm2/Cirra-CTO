# NEVER-2-FAR — CLAUDE.md
Last updated: March 2026 · Playbook sweep **2026-04-03** (template text unchanged — see **lift-mobility-core** `HANDOVER.md` for sibling repo status).

---

## THE THREE-LAYER AI DEVELOPMENT SYSTEM

This is the standard operating model for this project and ALL future projects. Never deviate from this.

### Layer 1: Lovable — Structure and UI only
- Builds initial component scaffold, page layouts, design system, and basic routing
- Lovable makes ZERO architecture decisions
- Lovable does NOT design data flow, auth patterns, state management, or API integration
- After Lovable builds the skeleton, Claude audits it before any further work begins
- Lovable-inherited patterns must be questioned before building on top of them — they are often fragile
- Always paste LOVABLE PERMANENT RULES (see bottom of this file) at the start of every Lovable session

**Common Lovable anti-patterns to audit and fix before proceeding:**
- Global loading gates tied to auth state (e.g. serviceReady pattern — see known bugs)
- Multiple Supabase client instances
- Missing error boundaries
- State management that does not survive auth token refresh
- Data fetching gated on auth-derived flags instead of actual data dependencies
- Local duplicate components instead of shared components

### Layer 2: Claude — Brain, architecture, and strategy
- Designs all architecture before any code is written
- Writes all Cursor briefs — Cursor never decides what to build
- Audits the codebase before every new phase
- Documents every decision in CLAUDE.md and docs/DECISIONS_LOG.md
- Calls out bad patterns before they get built on top of
- Never executes code directly
- If a bug survives 3 fix attempts without confirmed root cause: STOP. Read source files. Add debug logging. Understand before acting.
- Time pressure is not an excuse to ship unverified fixes

### Layer 3: Cursor — Hands and legs, execution only
- Executes exactly what Claude specifies, nothing more
- Makes zero independent architecture or design decisions
- If Cursor produces something not in the brief, flag it and correct it before merging
- Every Cursor session starts by reading CLAUDE.md in full
- Every Cursor output must follow the standard block format below
- Always paste CURSOR PERMANENT RULES (see bottom of this file) at the start of every Cursor session

---

## RULES FOR EVERY SESSION

1. Always paste CLAUDE.md at the start of every Claude session before any technical work
2. Claude reads and audits the current codebase state before writing any Cursor instructions
3. Pre-flight audit before starting any new phase: run `git status`, check for uncommitted files, confirm clean baseline
4. Never start a new phase on top of unresolved bugs or uncommitted work
5. Phase-gated delivery only — small incremental phases, not monolithic builds. One feature per Cursor session maximum.
6. If a brief is getting long, split it into two phases
7. If context gets long in a Claude session, proactively suggest moving to a fresh chat with a tight summary
8. Never assume any factual number — always verify from source files or documentation
9. When a bug appears: diagnose root cause first, document it in CLAUDE.md, THEN fix
10. Do not ship speculative fix attempts — understand before acting
11. After any significant architectural decision, append to docs/DECISIONS_LOG.md immediately
12. Never commit with a failing build
13. Never apply a migration without confirming the migration list is in sync first
14. Never create local duplicate components — always check shared components first

---

## CURSOR OUTPUT FORMAT — MANDATORY

Every Cursor brief must be ONE single copyable block. Never split across multiple blocks.

### Brief format — paste this at the top of every Cursor session:

```
STRICT OUTPUT RULE: Every response must be in ONE single copyable code block in this exact format:
SUMMARY / TASK / FILES CREATED / FILES MODIFIED / FILES DELETED / CHECKS RUN / SCHEMA CHANGES / MIGRATION APPLIED / COMMITTED / PUSHED / MERGE CONFLICTS / BUILD STATUS / OPEN ISSUES
Never split code across multiple blocks. One block only, always. End with: Summary: [one line]

## TASK — [Task name here]

[Detailed instructions here. Read files before editing. Never guess at file contents.]

Run: npm run build
If build passes:
git add -A
git commit -m "[conventional commit message]"
git push origin main

Paste full output in ONE single block only.
```

### Expected Cursor output — verify every field is present:

```
SUMMARY:
- [what was built or changed in plain English]
- [why it matters or what it enables]
- [any important decisions made or issues resolved]

TASK: [technical description of what was done]
FILES CREATED: [list or — if none]
FILES MODIFIED: [list or — if none]
FILES DELETED: [list or — if none]
CHECKS RUN: [list each with PASS/FAIL or — if none]
SCHEMA CHANGES: [list any table/column additions, modifications or — if none]
MIGRATION APPLIED: yes/no — [migration filename if yes]
COMMITTED: [commit message or — if none]
PUSHED: yes/no
MERGE CONFLICTS: yes/no — [if yes, describe how resolved]
BUILD STATUS: passing/failing — [if failing, describe error]
OPEN ISSUES: [anything incomplete, deferred, or needing follow-up or — if none]
```

Never skip any field. If nothing to report for a field, write —.
This applies to migrations, builds, verifications, commits, code changes, documentation updates, and everything else.

If Cursor output is missing this format — flag it and ask Cursor to re-run with the format reminder pasted.

---

## DECISION LOGGING — MANDATORY

Every significant architectural, product, or strategic decision must be appended to `docs/DECISIONS_LOG.md` immediately after it is made. Never delete entries.

Format:
```
## [Date]
**Decision: [title]**
[What was decided and why. One paragraph maximum.]
```

---

## DEV ENVIRONMENT RULES

- Always use `npm run preview` NOT `npm run dev` for admin testing
- `npm run dev` causes Vite HMR WebSocket drops that remount the entire React tree on window switch
- After any code change: `npm run build && npm run preview`
- Always clear browser storage before testing auth changes:
  - Chrome DevTools Console: `localStorage.clear(); sessionStorage.clear();`
- Hard refresh after every rebuild: `Cmd+Shift+R`
- Confirm new build loaded by checking bundle filename hash changed in Network tab
- Check preview server is alive: `lsof -i :4173`
- If preview server dies, restart: `npm run preview`
- Never test on port 8080 or 8081 — those are dev server ports, not preview

---

## STACK

- Frontend: React + TypeScript + Vite + Tailwind
- Backend: Supabase (Postgres + Edge Functions + RLS + pg_cron)
- Payments: Stripe (webhooks via stripe-webhook edge function)
- Email: Resend (via send-booking-email edge function only)
- Hosting: Cloudflare Pages (never-2-far.pages.dev / never-2-far.com)
- Supabase project ref: hpdvghrvifjuqcdyyxxh

---

## ENV VARS

```
VITE_SUPABASE_URL
VITE_SUPABASE_ANON_KEY
RESEND_API_KEY         (Supabase secret only — never in frontend)
STRIPE_SECRET_KEY      (Supabase secret only — never in frontend)
STRIPE_WEBHOOK_SECRET  (Supabase secret only — never in frontend)
```

---

## KEY CONVENTIONS

- All state transitions via `transitionBooking()` RPC only — no exceptions
- All emails via `send-booking-email` edge function only — no exceptions
- Booking reference shown to customers: `j_reference` (not UUID)
- Booking reference prefix: `J-`
- Email deduplication: `booking_email_log` table (`booking_id + email_type` = unique)
- Minimum booking advance: 2 days. Maximum: 30 days.
- Video call link stored in: `visitation_bookings.session_join_url`
- WhatsApp support number: WHATSAPP_NUMBER_TBD (fill in before launch)
- Support email: care@never-2-far.com
- Resend sending address: care@never-2-far.com
- Never reference `comms_outbox` — deprecated, removed
- All times display in UTC+8

---

## DESIGN SYSTEM

- Sidebar: navy `#0A1628`
- Content background: white
- Font: Montserrat, font-weight 300, letter-spacing 0.08em
- Active states: LIFT blue `#1E6FFF` only
- Always match the existing design system exactly — never introduce new colours or fonts

---

## SUPABASE CLIENT RULES

- One canonical client only: `src/integrations/supabase/client.ts`
- Partner auth client: `src/lib/partnerSupabase.ts`
- `src/lib/supabase.ts` re-exports from canonical client — do not create a third client
- Never call `createClient` more than once per client type
- Never create local duplicate components — always check shared components first

Both clients must use the Navigator Lock bypass:
```typescript
auth: {
  storage: localStorage,
  persistSession: true,
  autoRefreshToken: true,
  detectSessionInUrl: true,
  lock: async (_name: string, _acquireTimeout: number, fn: () => Promise<unknown>) => {
    // Bypass Navigator Lock entirely — prevents NavigatorLockAcquireTimeoutError
    return await fn();
  },
}
```

- Do NOT remove the lock bypass
- Do NOT revert to `lock: undefined` — insufficient, still attempts lock acquisition

---

## CANONICAL EMAIL TYPE SLUGS

```
booking_confirmed_payment
partner_assigned
reminder_24h
visit_started
visit_completed
visit_cancelled
visit_no_show
reschedule_proposed
```

---

## CANONICAL ADMIN STATUS ENUMS

```
PENDING_REVIEW, WAITLISTED, ASSIGNED, CONFIRMED, IN_PROGRESS,
COMPLETED, CANCELLED, ARCHIVED, OVERDUE
```

## CANONICAL BOOKING STATUS ENUMS (customer-facing, lowercase)

```
pending_review, waitlisted, assigned, confirmed, in_progress,
completed, cancelled, archived
```

---

## WHAT IS LIVE ON MAIN (as of March 2026)

- Stripe payment flow end to end ✅
- booking_email_log table with deduplication ✅
- send-booking-email edge function (Resend) ✅
- booking_confirmed_payment email template seeded ✅
- stripe-webhook fires confirmation email after payment ✅
- Customer auth system (login, register, portal) ✅
- Admin portal (bookings, partners, assignment flow) ✅
- Partner portal (job detail, check-in, complete job) ✅
- Migration history fully repaired and in sync ✅
- Navigator Lock bypass in both Supabase clients ✅
- Monitoring and Gallery empty states fixed ✅
- SIGNED_OUT listener in ServiceContext ✅
- Debug logging in ServiceContext — REMOVE BEFORE PROD LAUNCH ⚠️

---

## WHAT IS NEXT TO BUILD

- Phase 2: partner_assigned email + session_join_url field in admin portal
- Phase 3: reminder_24h email + pg_cron job
- Phase 4: partner portal video call UI
- Phase 5: edge case emails (cancelled, no-show, completed) + WhatsApp buttons
- Phase 6: serviceReady architectural fix — React Query refactor (MUST do before ops team onboarding)

---

## ⚠️ KNOWN BUG — MUST FIX BEFORE OPS TEAM ONBOARDING (Phase 6)

### Window-switch / serviceReady bug

**Symptoms:** After switching to any other app (WhatsApp, Finder, Slack) and returning, admin pages show skeleton loaders and data does not reload. Some sidebar links become unclickable.

**Confirmed root cause (March 2026):**
Supabase fires `INITIAL_SESSION (null) → SIGNED_IN (null) → SIGNED_IN (user)` on every window focus event. This is Supabase's built-in visibility change handler reinitialising its auth state machine. The entire ServiceContext unmounts and remounts, resetting `hasBeenReady` ref to false. Every admin page gating data on `serviceReady` goes blank.

This is a Lovable-inherited architectural flaw. Affects ALL admin users in production.

**Attempted fixes that did NOT work — do not retry:**
- Increasing sign-out debounce timeout (300ms, 3000ms)
- SIGNED_OUT event listener in ServiceContext
- hasBeenReady ref override in context value
- autoRefreshToken: false in Supabase client
- Removing serviceReady gate from individual pages one at a time
- Vite HMR timeout increase
- RouteSettleContext same-path guard

**The proper fix (Phase 6):**
Replace `serviceReady` as a data-fetching gate across all ~20 admin pages.
Use React Query globally with `staleTime: 5 * 60 * 1000` and `refetchOnWindowFocus: false`.
Data loads once on mount, stays stable, independent of auth events entirely.
Half-day Cursor session, ~20 files, must be done on a clean branch with full testing.

DO NOT attempt per-page patches. Proven ineffective.
DO NOT onboard ops team until Phase 6 is complete.

---

## ⚠️ REMOVE BEFORE PRODUCTION LAUNCH

Debug logging active in `src/contexts/ServiceContext.tsx` (commit `94e8a93`):
- `console.log('[ServiceContext] auth event:', ...)` in onAuthStateChange
- `console.log('[ServiceContext] main effect:', ...)` in main useEffect

Remove both before production launch.

---

## ARCHITECTURE PRINCIPLES — APPLY TO ALL FUTURE PROJECTS

Learned the hard way. Apply from day one on every new platform.

### 1. Never gate data fetching on auth-derived state
Any flag derived from auth state (serviceReady, isAuthenticated, userLoaded) will wobble during token refresh and window focus events.

```typescript
// BAD — unstable, wobbles on auth events
enabled: !!selectedServiceId && serviceReady

// GOOD — stable, only depends on actual data requirement
enabled: !!selectedServiceId
```

### 2. Use React Query for all data fetching from day one
Set globally in QueryClient at project start:
```typescript
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,
      refetchOnWindowFocus: false,
      refetchOnReconnect: true,
      retry: 2,
    },
  },
});
```

### 3. Supabase Navigator Lock must always be bypassed
Use the custom lock function shown in SUPABASE CLIENT RULES. `lock: undefined` is not sufficient.

### 4. One Supabase client instance only
Module-level singleton. Never call `createClient` more than once per client type.

### 5. Auth state changes must not cause UI resets
Only `SIGNED_OUT` should reset app state. `TOKEN_REFRESHED`, `SIGNED_IN`, and `INITIAL_SESSION` are normal lifecycle events — the UI must survive all of them without any visible change.

### 6. serviceReady is an anti-pattern — never use in new projects
Lovable's choice. Caused a full day of debugging. Never again.

### 7. Phase-gated delivery only
No monolithic builds. One feature per Cursor session. Split if brief is getting long.

### 8. Document root cause before fixing
Read source files. Add debug logging if needed. Confirm root cause. Document in CLAUDE.md. Then fix.

### 9. Pre-flight audit before every phase
Before any new build phase:
- `git status` — confirm clean baseline
- Read key files that will be touched
- Confirm previous phase working in preview
- Check for open issues from previous session

### 10. The 3-attempt rule
If a bug survives 3 fix attempts without confirmed root cause, stop. Read source files. Add debug logging. Understand before acting.

### 11. Never commit with failing build
Never apply a migration without confirming migration list is in sync first.

### 12. Always include loading states and empty states
Every table and panel must have a loading state and an empty state. Never leave skeleton loaders that never resolve.

---

## SESSION HANDOFF FORMAT

When ending a session or moving to a fresh chat, always produce this summary:

```
DECISIONS MADE: [list]
COMMITS SHIPPED: [hash — message, one per line]
OPEN BUGS: [name — root cause if known]
NEXT STEP: [exact first action in next session]
CONTEXT NEEDED: [what to paste at start of next session]
```

---

## CLAUDE.MD TEMPLATE FOR NEW PROJECTS

Use this when starting any new project with the Lovable → Claude → Cursor stack.
Paste into Cursor Agent at the start of every new project:

```
Create CLAUDE.md in the project root with this template structure. Fill in what is known, leave placeholders for what is not yet defined. This file must be read before every task and updated after every significant change.

# [PROJECT NAME] — CLAUDE.md

## Purpose
[One paragraph describing what this project does and who it serves.]

## Tech Stack
- Frontend: [framework, language, styling]
- Backend: [database, auth, storage]
- Payments: [provider]
- Email: [provider]
- Deployment: [hosting, CI/CD]
- Version control: [repo URL]

## The Three-Layer AI System
- Lovable: UI structure only — no architecture decisions
- Claude: brain, architecture, all Cursor briefs
- Cursor: execution only — no independent decisions

## Build Rules — Non-Negotiable
- All state transitions via [transition function] only
- All emails via [edge function] only
- One Supabase client instance only
- Never gate data fetching on auth-derived state
- Use React Query with refetchOnWindowFocus: false globally
- Supabase Navigator Lock bypass required (see client config)
- Never create local duplicate components — use shared components only
- Never commit with failing build
- Never apply migration without confirming sync first
- Always include loading states and empty states

## Database Schema
[Summary of all tables, their purpose, and key relationships. Update whenever schema changes.]

## Roles and Permissions
[List all user roles and what they can access.]

## Transition Functions
[Location and purpose of all state transition functions.]

## Shared Components
[Location of shared component library. Rule: always use these, never duplicate locally.]

## Current Phase
[What is in progress and what is complete.]

## Remaining Phases
[Ordered list of phases not yet built.]

## Known Bugs
[List with root cause and fix status.]

## Decisions Log
[Append-only. Format: ## [Date] / **Decision: [title]** / [rationale]]

## Open Issues
[Anything incomplete, deferred, or flagged for follow-up.]
```

---

## PERMANENT RULES — PASTE INTO CURSOR AT START OF EVERY SESSION

```
PERMANENT RULES — apply to every task in every project without exception.

OUTPUT FORMAT
After completing any task, always output results in this exact block:

SUMMARY:
- [what was built or changed in plain English]
- [why it matters or what it enables]
- [any important decisions made or issues resolved]

TASK: [technical description of what was done]
FILES CREATED: [list or — if none]
FILES MODIFIED: [list or — if none]
FILES DELETED: [list or — if none]
CHECKS RUN: [list each with PASS/FAIL or — if none]
SCHEMA CHANGES: [list any table/column additions, modifications or — if none]
MIGRATION APPLIED: yes/no — [migration filename if yes]
COMMITTED: [commit message or — if none]
PUSHED: yes/no
MERGE CONFLICTS: yes/no — [if yes, describe how resolved]
BUILD STATUS: passing/failing — [if failing, describe error]
OPEN ISSUES: [anything incomplete, deferred, or needing follow-up or — if none]

Never skip any field. If nothing to report write —. This applies to migrations, builds,
verifications, commits, code changes, documentation updates, and everything else without exception.

CLAUDE.MD RULE
Every project must have a CLAUDE.md file in the root directory.
Before starting any task, read CLAUDE.md in full.
After completing any task that introduces a significant decision, schema change, or architectural
change, update CLAUDE.md immediately.
Never proceed with a task if CLAUDE.md contradicts the instruction — surface the conflict first.

CLAUDE.MD must always contain:
- Project name and purpose
- Tech stack
- Build rules (non-negotiable constraints)
- Database schema summary
- Role and permission model
- Transition function locations
- Shared component locations
- Decisions log (append-only, never delete entries)
- Current phase and remaining phases
- Open issues

DECISION LOGGING
Every significant architectural, product, or strategic decision must be appended to
docs/DECISIONS_LOG.md immediately after it is made. Never delete entries. Format:

## [Date]
**Decision: [title]**
[What was decided and why. One paragraph maximum.]

QUALITY RULES
- Never assume. If unclear, surface the ambiguity before proceeding.
- Never commit with failing build.
- Never apply a migration without confirming the migration list is in sync first.
- Never create local duplicate components — always check shared components first.
- Always read CLAUDE.md before starting any task.
- Always update CLAUDE.md after any significant change.
- Always include loading states and empty states on every table and panel.
```

---

## PERMANENT RULES — PASTE INTO LOVABLE AT START OF EVERY SESSION

```
PERMANENT RULES — apply to every task without exception.

OUTPUT FORMAT
After completing any task, always output results in this exact block:

SUMMARY:
- [what was built or changed in plain English]
- [why it matters or what it enables]
- [any important decisions made or issues resolved]

TASK: [technical description of what was done]
FILES CREATED: [list or — if none]
FILES MODIFIED: [list or — if none]
FILES DELETED: [list or — if none]
CHECKS RUN: [list each with PASS/FAIL or — if none]
SCHEMA CHANGES: [list any table/column additions or — if none]
MIGRATION APPLIED: yes/no — [migration filename if yes]
COMMITTED: [commit message or — if none]
PUSHED: yes/no
MERGE CONFLICTS: yes/no — [if yes, describe how resolved]
BUILD STATUS: passing/failing — [if failing, describe error]
OPEN ISSUES: [anything incomplete, deferred, or needing follow-up or — if none]

Never skip any field. If nothing to report write —.

BUILD RULES — NON-NEGOTIABLE
- Build UI shells and new pages only. Never touch transition functions, migrations, or database logic.
- Never create local duplicate components. Always use shared components from src/shared/components/.
- Never write direct state updates. All state changes go through transition functions only.
- Always match the existing design system exactly: navy #0A1628 sidebar, white content,
  Montserrat, font-weight 300, letter-spacing 0.08em, LIFT blue #1E6FFF active states only.
- All times display UTC+8.
- Always include loading states and empty states on every table and panel.
- After building, verify and list all checks explicitly before marking complete.
- Make zero architecture decisions — surface any architectural question to Claude first.
```

---

## FULL RULES

See `.claude/CLAUDE_INSTRUCTIONS.md` for Cursor agent rules.
See `.cursor/rules/never-2-far-constitution.mdc` for project constitution.
See `docs/DECISIONS_LOG.md` for append-only decisions history.
