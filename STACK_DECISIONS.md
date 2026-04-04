# STACK_DECISIONS.md
Cirra Edge — Architecture and Stack Decision Log
Append-only. Add an entry whenever a significant architecture decision is made.

*Stamp **2026-04-03:** No new stack rows — LIFT scoped-CI decision lives in **lift-mobility-core** `docs/DECISIONS_LOG.md`.*

---

## Authentication
Decision: OAuth provider
Chosen: Google OAuth via Supabase Auth (implicit flow)
Alternatives: Email/password, Magic link, Auth0, Clerk
Why: Zero password management. Google accounts standard in professional contexts. Supabase handles token refresh and session persistence natively. Implicit flow chosen over PKCE after debugging session loss with React Router.
Date: March 2026
Projects: lift-staff-portal

## Database
Decision: Primary database
Chosen: Supabase (PostgreSQL)
Alternatives: PlanetScale, Firebase Firestore, Neon
Why: Row-Level Security at DB level enforces access control regardless of app bugs. PostgreSQL is battle-tested. Supabase adds Auth, Storage, Edge Functions in one platform.
Date: March 2026
Projects: lift-staff-portal

## Frontend
Decision: Frontend stk
Chosen: React 18 + TypeScript + Vite + Tailwind + shadcn/ui
Alternatives: Next.js, Remix, SvelteKit
Why: Vite for fast dev. shadcn/ui gives accessible components without lock-in. TypeScript strict mode catches bugs before runtime.
Date: March 2026
Projects: lift-staff-portal

## AI Integration
Decision: AI extraction
Chosen: Anthropic Claude via Supabase Edge Function
Alternatives: OpenAI GPT-4, Google Gemini
Why: Claude performs better on structured extraction. Edge Function keeps API key server-side.
Date: March 2026
Projects: lift-staff-portal (expense claim extraction)

## Error Monitoring
Decision: Error monitoring
Chosen: Sentry
Alternatives: Datadog, LogRocket, Bugsnag
Why: Industry standard. React SDK handles error boundaries. PII scrubbing built-in. Free tier generous.
Date: March 2026
Projects: All (required by Cirra Engineering standards)

*STACK_DECISIONS.md | Cirra Edge | Append-only*
