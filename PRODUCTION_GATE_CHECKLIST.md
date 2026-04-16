# PRODUCTION GATE CHECKLIST
**Version 1.0 | Cirra Edge | April 2026**
Every product must clear ALL applicable gates before going live with real users.

---

## How to Use

Copy this checklist into your product's CLAUDE.md or a standalone PRODUCTION_GATES.md file. Mark each gate MET or NOT MET with evidence. Submit to Group CTO for review before go-live.

---

## Security Gates

| # | Gate | Evidence Required |
|---|------|-------------------|
| S1 | No .env files in git history | Output of: git log --all --name-only \| grep -i "\.env" |
| S2 | All Supabase secrets rotated if .env was ever committed | Confirmation from Supabase dashboard |
| S3 | No service role key in any frontend file | Output of: grep -rn "service_role" src/ |
| S4 | No PII in console statements | Output of: grep -rn "console\." src/ \| grep -i "email\|phone\|password\|nric" |
| S5 | RLS enabled on every table | Screenshot or query from Supabase dashboard |
| S6 | RLS tested with real JWT (not service role) | Test result showing correct role can access, wrong role cannot |
| S7 | Rate limiting on auth endpoints | Description of rate limiting mechanism |
| S8 | Edge Function secrets do NOT use SUPABASE_ prefix | List of Edge Function secret names |
| S9 | Dev login buttons / test backdoors removed | Screenshot of production login page |
| S10 | npm audit clean (zero high/critical in prod deps) | Output of: npm audit --audit-level=high |

## Infrastructure Gates

| # | Gate | Evidence Required |
|---|------|-------------------|
| I1 | Sentry configured with source maps | Sentry dashboard screenshot showing project |
| I2 | Cloudflare Pages prod branch wired | Cloudflare dashboard screenshot or URL test |
| I3 | Custom domain DNS verified | nslookup or dig output |
| I4 | Email sending domain verified (if applicable) | Resend/SendGrid dashboard screenshot |
| I5 | pg_cron verified running (if applicable) | Supabase extension list |
| I6 | PgBouncer enabled (if applicable) | Supabase connection pooling settings |
| I7 | Uptime monitoring configured | Betterstack/UptimeRobot dashboard screenshot |

## Data Gates

| # | Gate | Evidence Required |
|---|------|-------------------|
| D1 | Supabase project confirmed in Singapore region | Dashboard > Settings > General screenshot |
| D2 | PDPA consent mechanism implemented (if collecting personal data) | Screenshot of consent flow |
| D3 | Backup restoration tested at least once | Restoration test result with time recorded |

## Code Quality Gates

| # | Gate | Evidence Required |
|---|------|-------------------|
| C1 | Build passes | Output of: npm run build |
| C2 | TypeScript passes | Output of: npx tsc --noEmit |
| C3 | No file over 2,000 lines | Output from codebase health check point 1 |
| C4 | At least 4 golden path tests exist | Output from codebase health check point 9 |
| C5 | 10-point codebase health check completed | Full audit output |

## User Acceptance Gates

| # | Gate | Evidence Required |
|---|------|-------------------|
| U1 | E2E test with named real user passed | Name of tester + test date + result |
| U2 | Core workflow tested end-to-end per role | Test results per role |
| U3 | Stripe live mode verified (if applicable) | Test transaction screenshot |

---

## Scoring

Count gates MET vs total applicable gates. Score = MET / APPLICABLE.

**100%:** Ready for production.
**80-99%:** Review remaining gaps with Group CTO. May proceed with documented risk acceptance.
**Below 80%:** NOT ready. Fix gaps before go-live.

---

*PRODUCTION_GATE_CHECKLIST.md | Version 1.0 | Cirra Edge | April 2026*
