# NAMING CONVENTIONS
**Version 1.0 | Cirra Edge | April 2026**
Applies to all products. No exceptions.

---

## Database Naming

| Element | Convention | Example |
|---------|-----------|---------|
| Tables | snake_case, plural | booking_requests, vehicle_trips |
| Columns | snake_case | created_at, operator_id |
| Primary keys | Always UUID (gen_random_uuid()) | id uuid PRIMARY KEY DEFAULT gen_random_uuid() |
| Foreign keys | Referenced table singular + _id | operator_id, vehicle_id |
| RLS functions | Descriptive verb phrase | get_my_role(), stop_belongs_to_my_operators() |
| Reference generation functions | generate_[prefix]_ref() | generate_pp_ref('BKG') |
| Triggers | trg_[action]_[table] | trg_assign_ref_booking_requests |

## Reference Numbers

| Type | Start Value | Format | Example |
|------|------------|--------|---------|
| Customer-facing serials | 04011 | PREFIX-SG-YYYY-NNNNN | BKG-SG-2026-04012 |
| Internal records | 000001 | PREFIX-NNNNNN | LOG-000001 |
| Database primary keys | UUID | gen_random_uuid() | a1b2c3d4-e5f6-... |

Customer-facing serials are display only. UUID is always the true database PK. Serials are generated atomically via RPC with SELECT FOR UPDATE — never in application code, never via Postgres sequences.

## Table Prefix Segregation

When multiple portals share a single Supabase project, use prefix segregation:

| Prefix | Purpose | Example |
|--------|---------|---------|
| pp_ | Partner portal tables | pp_partner_users, pp_booking_requests |
| (none) | Staff portal / primary tables | staff, claims, organisations |

Each prefix has its own RLS policies. No cross-prefix RLS dependencies.

## Frontend Naming

| Element | Convention | Example |
|---------|-----------|---------|
| Components | PascalCase | BookingsPage, DriverCard |
| Hooks | camelCase with use prefix | useEtaCalculation, useAuth |
| Contexts | PascalCase with Context suffix | AuthContext, DriverAuthContext |
| Constants | UPPER_SNAKE_CASE | MAX_JOURNEY_MINUTES, DEFAULT_SERVICE_TIME |
| Types/Interfaces | PascalCase | BookingRequest, VehicleTrip |
| File names (components) | PascalCase.tsx | BookingsPage.tsx |
| File names (hooks) | camelCase.ts | useEtaCalculation.ts |
| File names (utils) | camelCase.ts | normalizePassengerName.ts |

## Git

| Element | Convention | Example |
|---------|-----------|---------|
| Commit messages | type: description | fix: enterprise inbound ETA shows dropoff time |
| Types | feat, fix, docs, chore, refactor, test, perf | docs: update CLAUDE.md with new tables |
| Branch names | feature/description or fix/description | feature/booking-tracker |

---

*NAMING_CONVENTIONS.md | Version 1.0 | Cirra Edge | April 2026*
