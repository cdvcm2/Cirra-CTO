# Machine-checkable session handover (LIFT PHASE 0 FIX-4)

**Purpose:** Skeleton for `docs/handover/HANDOVER_<YYYY-MM-DD>.md`. Each section stays between HTML comment markers so `scripts/handover-validate.sh` can gate `docs(LIFT-DOCS-CLOSE-NN)` commits.

Do **not** commit with unresolved **template stubs** (`SESSION_NUMBER` still shows the template token, `AUTO_FILL_*`, or any `REPLACE_*` sentinel from this file).

---

<!-- TEMPLATE_VERSION: 1.0.0 -->
<!-- HANDOVER_HEAD_SHA: AUTO_FILL_FROM_GIT -->
<!-- HANDOVER_DATE: AUTO_FILL_TODAY -->
<!-- SESSION_NUMBER: MANUAL_FILL -->
<!-- HANDOVER_PARENT_HEAD_SHA: AUTO_OR_MANUAL — git SHA of HANDOVER_<prev>.md commit base (omit only on first-ever use). -->

<!-- ARTIFACT_1_BEGIN: immediate_read_first -->

(One paragraph max — what incoming sub-CTO does FIRST after reading pointers.)

<!-- ARTIFACT_1_END -->

<!-- ARTIFACT_2_BEGIN: session_accomplishments -->

(AUTO_POPULATED: paste `git log --oneline <HANDOVER_PARENT_HEAD_SHA>..<HANDOVER_HEAD_SHA>` verbatim below this line.)

```
REPLACE_WITH_GIT_LOG_OUTPUT
```

<!-- ARTIFACT_2_END -->

<!-- ARTIFACT_3_BEGIN: feature_state -->

(Status per flagship feature lanes — prose.)

<!-- ARTIFACT_3_END -->

<!-- ARTIFACT_4_BEGIN: known_bugs -->

<!-- AUTO_POPULATED: TECH_DEBT.md entries added this session -->

(ALLOWED_NOTE: optional context paragraphs.)

REPLACE_WITH_KNOWN_BUGS_TECH_DEBT_ENTRY_IDS

<!-- ARTIFACT_4_END -->

<!-- ARTIFACT_5_BEGIN: decisions_made -->

<!-- AUTO_POPULATED: from docs/handover/SESSION_DECISIONS_<DATE>.log for this session -->

(If append script was not run, summarise every D-ID here verbatim, one entry per paragraph.)

REPLACE_WITH_SESSION_DECISIONS_SUMMARY_LINE

<!-- ARTIFACT_5_END -->

<!-- ARTIFACT_6_BEGIN: project_state -->

(PI cadence / doc topology / CI gates.)

<!-- ARTIFACT_6_END -->

<!-- ARTIFACT_7_BEGIN: incoming_punchlist -->

(Incoming P0 / P1 / P2 bullets — Priority · ETA · Owner.)

<!-- ARTIFACT_7_END -->

<!-- ARTIFACT_8_BEGIN: docs_commit_brief -->

<!-- Document Agent briefing — summarise files touched & decision IDs. -->

(P0 rationale + escalation notes.)

<!-- ARTIFACT_8_END -->

---

*TEMPLATE_VERSION synced with cirra-playbook/HANDOVER_TEMPLATE.md — bumps require updating `scripts/handover-validate.sh` pin.*
