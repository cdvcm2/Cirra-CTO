# DECISIONS LOG
Append-only. Never edit existing entries. Add new entries at the top.
Format: DATE | DECISION | RATIONALE | AFFECTED PROJECTS

---

2 April 2026 | Dual portal architecture approved | 
staff.liftmobility.co and partner.liftmobility.co coexist 
permanently on same Supabase project. Separate AuthContext 
files, separate identity tables (staff vs partner_users). 
Stanley stays on staff portal via module permissions. | 
LiftStaff

2 April 2026 | Claims module deprioritised | 
Aspire covers internal claims. LiftStaff pivots to 
partner-facing CRM. Claims not cancelled — frozen 
post-April review. | LiftStaff

2 April 2026 | Partner portal auth — email/password only | 
External partners use Supabase email/password with invite 
gate. No Google OAuth for external partners. 
Forgot password + reset password flows required. | LiftStaff

2 April 2026 | Naming convention locked across all products | 
Customer-facing records start at 04011. Internal records 
start at 000001. UUID as true DB primary key always. 
Cancelled records keep number, never reused. | All products

2 April 2026 | default_execution_partner_id rename approved | 
Renamed from execution_partner_id on partner_accounts table 
to avoid naming collision with jobs.execution_partner_id. | 
LiftStaff

2 April 2026 | Job dispatch flow confirmed | 
Admin assigns BKG to execution partner as offer. 
Partner accepts or rejects with reason. 
If rejected admin reassigns same BKG to next partner. 
Only one active offer per BKG at any time. 
Full history in job_offers table. | LiftStaff

2 April 2026 | Never-2-Far production gate conditions set | 
No go-live without: Group CTO sign-off, Alex Soliman 
end-to-end test passing, test coverage at 3/10 minimum, 
PROD_CRON_SETUP.md SQL run manually post-migration. | 
Never-2-Far

2 April 2026 | LiftCore Brief 075 approved | 
Full Dispatch Planner rebuild approved. Scope: full-day 
timeline, scalable vehicle lanes, draft state persistence, 
human-readable wave selector, collapsible map. 
Start after Brief 072 applied. | LiftCore

2 April 2026 | MASTER_CONTEXT.md established | 
Single source of truth for onboarding any new Group 
CTO/CPO Claude session. Lives in cdvcm2/Cirra-CTO. 
Update in same session as any group-level decision. | 
All products

---
