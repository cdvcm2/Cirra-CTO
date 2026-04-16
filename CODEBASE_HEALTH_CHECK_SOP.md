# CODEBASE HEALTH CHECK SOP
**Version 1.0 | Cirra Edge | April 2026**
**Origin:** LiftCore Sub-CTO | **Status:** Group standard, mandatory all projects

Run this audit on day one of any project you inherit and at the end of every session with significant code changes.

---

## The 10-Point Audit

Run from your repo root. Copy raw output. No cleaning.

### 1. File Size (where's the bloat?)

```bash
find src/ -name "*.tsx" -o -name "*.ts" | xargs wc -l | sort -rn | head -40
```

Over 300 lines: worth watching. Over 500: needs attention. Over 1,000: crisis. Over 5,000: monolith hiding in a file. The file at the top is your number one risk.

### 2. Dead Code

```bash
npx ts-prune --project tsconfig.app.json 2>/dev/null | grep -v "used in module" | head -40
```

Noisy with false positives (entry points, barrel exports). But anything it flags that isn't an app entry — investigate. If nothing imports it, delete it.

### 3. Duplicate Queries

```bash
grep -rn "\.from(" src/ | wc -l
grep -rn "as unknown as Record" src/ | wc -l
```

Same table queried from 20+ places with different select shapes = need a shared data access layer.

### 4. Type Safety

```bash
grep -rn ": any" src/ --include="*.ts" --include="*.tsx" | wc -l
grep -rn "as any" src/ --include="*.ts" --include="*.tsx" | wc -l
grep -rn "as unknown" src/ --include="*.ts" --include="*.tsx" | wc -l
grep -rn "@ts-ignore\|@ts-expect-error" src/ | wc -l
```

Target: zero. Reality: get under 20. Over 100 means types are decoration.

### 5. Console Statements

```bash
grep -rn "console\." src/ --include="*.ts" --include="*.tsx" | wc -l
grep -rn "console\." src/ --include="*.ts" --include="*.tsx" | sed 's/:.*//g' | sort | uniq -c | sort -rn | head -10
```

If 80% cluster in one file, that file is being debugged constantly because it's too complex.

### 6. Lint Errors

```bash
npx eslint src/ 2>&1 | tail -3
```

Separate errors from warnings. Fix errors first. Over 50 errors = batch fix by category.

### 7. Dependency Vulnerabilities

```bash
npm audit --audit-level=moderate
```

Moderate+ in production deps needs a fix or documented acceptance. Dev dep vulns are lower priority.

### 8. Bundle Size

```bash
npm run build 2>&1 | grep -E "dist/.*\.js|dist/.*\.css" | sort -t'|' -k2 -rn
```

Any chunk over 200KB is concerning. Over 500KB is a mobile problem. Code-split with dynamic imports.

### 9. Test Count

```bash
find src/ -name "*.test.*" -o -name "*.spec.*" | wc -l
find src/ -name "__tests__" | wc -l
```

Zero = no safety net. Every refactor is a prayer. Target: 4-6 golden path tests for core flows.

### 10. Edge Function Size

```bash
find supabase/functions/ -name "index.ts" | xargs wc -l | sort -rn
```

Same rules as frontend: over 500 lines is a problem. Keep index as a thin router.

---

## Grading

**GREEN:** Largest file under 500 lines. Under 20 type escape hatches. Under 10 console statements. Under 20 lint errors. At least 4 tests. All chunks under 200KB. No moderate+ vulns in prod deps.

**AMBER:** Largest file 500-2,000 lines. 20-50 type escapes. 10-30 console statements. 20-50 lint errors. 0-3 tests. Some chunks 200-500KB.

**RED:** Largest file over 2,000 lines. Over 50 type escapes. Over 30 console statements. Over 50 lint errors. No tests. Chunks over 500KB.

---

## What to Do With Results

Priority order:
1. Decompose the largest file. Everything else gets easier.
2. Type safety. Fix as any casts around database queries.
3. Tests for core flows. Even 4 tests give you a regression net.
4. Lint errors, console cleanup, dead code. Hygiene.
5. Bundle optimization, dependency updates. Maintenance.

Run this audit again after each phase. Numbers must trend down.

---

*CODEBASE_HEALTH_CHECK_SOP.md | Version 1.0 | Cirra Edge | April 2026*
