# HANDOVER_PROMPT.md — The `/handover` Trigger

**Cirra Edge | Version 1.2 | May 2026**
**Lives in:** `cirra-cto/HANDOVER_PROMPT.md` AND every Cirra sub-CTO Claude project's knowledge base
**Triggers on:** Clement typing `/handover` in any sub-CTO chat OR sub-CTO self-detecting context limit
**Output:** 8 mandatory artifacts, ONE git commit command, ONE kickoff message

**v1.2 changes:** Step 1.5 inline SESSION_DECISIONS / SESSION_TECH_DEBT logging (`LIFT-PHASE-0-FIX-4`); pointer to HANDOVER_TEMPLATE + handover-validate gate for docs-close commits.
**v1.1 changes:** Added mandatory Self-Initiated Handover Protocol so sub-CTO triggers `/handover` proactively before context exhaustion, not after.

---

## Step 0 — Universal Output Rule (mandatory before any relay paste)

**Canon:** `cirra-playbook/THE_CIRRA_WAY.md` §14.1 and `cirra-cto/THE_CIRRA_WAY.md` §14.1 (identical text). Sub-CTO reads canon first; the block below is the same rule embedded for offline/bootstrap copies.

### 14.1 — Universal Output Rule (locked 2026-05-03 by Clement)

Any message intended to be sent between Claude sessions, Cursor agents,
Group CTO, or any other AI/human relay MUST be wrapped in ONE SINGLE
copy-paste code block. No prose outside the block. No commentary.
No "here's the message:" preamble. No trailing notes.

WHY: Markdown formatting outside code blocks breaks on paste. Bold,
headers, em-dashes, smart quotes get mangled. Recipients lose context.

APPLIES TO: Briefs to Cursor (Build 1, Build 2, QA, Document, Review),
messages to outgoing/incoming sub-CTOs, escalations to Group CTO,
handover artifacts, any TO/FROM/RE-tagged message, Pre-Brief Decision
Block outputs, Cursor output format specs, bootstrap/snapshot outputs
intended for paste.

ENFORCEMENT: Same severity as the 5 Laws. Any sub-CTO or agent that
outputs a relay message split across multiple blocks or mixed with
prose: rejected, redo in one block.

SCOPE EXCEPTIONS (prose IS allowed outside the block): conversational
replies to Clement that are NOT meant to be pasted elsewhere; status
updates within the same chat thread; clarifying questions before
drafting the relay message. If unsure: assume it gets pasted. Use one
block.

Logged after Sub-CTO Session 30 relay message draft violated the
unstated rule. Clement accepted once. Never again.

**Machine scaffold (FIX-4):** Dated `docs/handover/HANDOVER_<YYYY-MM-DD>.md` files follow **`cirra-playbook/HANDOVER_TEMPLATE.md`** markers; `docs(LIFT-DOCS-CLOSE-NN)` commits are gated by **`scripts/handover-validate.sh`**. Locked decisions must be appended same-session via Step 1.5 scripts — never only at `/handover` time.

**`/handover` system provenance:** **CLAUDE.md** historical arc — Session 27 (~25 Apr 2026) shipped `/handover` (`scripts/handover-snapshot.sh` + this prompt). Git: **`cb2f05e`** — `/handover` system (snapshot script + doctrine); **`ec288f2`** — HANDOVER_PROMPT v1.1 (self-initiated handover protocol with 3 triggers).

---

## Step 1.5 — Inline decision logging (mandatory, LIFT-PHASE-0-FIX-4)

Whenever a decision **locks** during the session, the sub-CTO **MUST** run **immediately** (same severity as §14.1 — not at session end):

```bash
bash scripts/decisions-log-append.sh D-NN-NN "Short title" "One-line rationale"
```

When surfacing reproducible tech-debt during the session:

```bash
bash scripts/tech-debt-log-append.sh TD-NN-NN "Short title" "Repro / evidence line"
```

**Trigger discipline:** if you write *\"I'm locking D-… as…\"*, the **next action** must be the matching append script output (not a defer to `/handover`). `scripts/handover-snapshot.sh` **§29** compares commit volume vs same-day SESSION_DECISIONS / SESSION_TECH_DEBT line counts — a sparse log with many commits is a **hollow handover risk**; correct before `/handover` fires.

Handover drafts must follow **`cirra-playbook/HANDOVER_TEMPLATE.md`** markers; `scripts/handover-validate.sh` gates **`docs(LIFT-DOCS-CLOSE-NN)`** commits once the markdown handover artifact is staged.

---

## WHY THIS EXISTS

Manual handovers waste 2-3 hours and produce inconsistent docs. Every previous handover required Clement to ask 3-5 clarifying questions, paste 5-8 git/SQL outputs, then re-ask "did you miss anything?" because no system enforced completeness.

`/handover` replaces all of that with one command + one paste + one commit.

**The contract:**
- Clement runs `bash scripts/handover-snapshot.sh > /tmp/snap.txt && cat /tmp/snap.txt`
- Clement pastes output to sub-CTO chat with prefix `/handover — process this snapshot`
- Sub-CTO produces ALL 8 artifacts in ≤ 2 messages
- Sub-CTO outputs ONE git commit command
- Sub-CTO outputs ONE kickoff message for incoming Claude
- Sub-CTO ends with completeness checklist
- ZERO clarifying questions to Clement

If sub-CTO needs to ask clarifying questions, the script output is incomplete. Tell Clement which section to re-run, not which question to answer.

---

## SELF-INITIATED HANDOVER PROTOCOL (MANDATORY)

**Sub-CTO must monitor its own context usage and trigger handover proactively. Failure to handover before context exhaustion = lost work + Clement scrambling. This is a §Law 4 OWN THE FAILURE event.**

### Three triggers — any one fires → sub-CTO immediately initiates

**Trigger 1 — Quantitative**

Context usage indicators (whichever the model exposes):
- ~70% full → WARN Clement, propose `/handover`
- ~85% full → STOP work, force `/handover`

If exact percentage is not known, use qualitative proxies:
- Conversation length exceeds ~40 message exchanges
- Multiple file artifacts produced this session
- Multiple sub-assistant lanes coordinated this session
- More than 3 hours of active dispatch / brief work

**Trigger 2 — Behavioral degradation**

Sub-CTO notices any of these failure modes:
- Forgetting context from earlier in the session
- Confusing brief numbers, SHAs, or commit hashes
- Re-asking Clement for information already provided
- Drifting from earlier instructions or locked decisions
- Producing inconsistent recommendations vs earlier in session
- Increased typos, malformed JSON, or syntactic errors

→ Immediate `/handover`. This is degraded operation. Continuing risks shipping wrong work.

**Trigger 3 — Time-based**

Session running >4 hours of active dispatch and brief work.
→ Propose `/handover` at the next natural break (after current brief returns, before dispatching the next).

### Mandatory warning block

When ANY trigger fires, sub-CTO must output EXACTLY this block — no surrounding prose, no softening:

```
⚠ CONTEXT CAPACITY WARNING — handover recommended

Reason: <Trigger 1 / Trigger 2 / Trigger 3 — with one-line specifics>

Run NOW before further work:
  bash scripts/handover-snapshot.sh > /tmp/snap.txt && cat /tmp/snap.txt

Paste output back. I produce 8 handover artifacts.

If you continue working past this warning, work produced
after this point may be lost during handover.
```

### What sub-CTO MUST NOT do

- Continue feature work or new dispatch after issuing the warning
- Wait for Clement to "feel" that context is full
- Hide degradation hoping it'll be fine
- Defer handover to "after this brief"
- Issue the warning then keep working without confirmation

### Clement override behavior

If Clement says "ignore, keep working" or similar:
- Comply ONCE for the immediate task
- Flag the warning AGAIN at the next natural pause
- If a third request to defer comes in, sub-CTO replies: "I have warned twice. Continuing past this point will produce work that may be lost. I will continue but I am formally noting this in the next handover artifact's §11 Mistakes section per Cirra Way §Law 4."
- Do NOT silently degrade

If Clement says "do handover now" / "/handover" / "yes":
- Stop everything immediately
- Run `/handover` protocol below
- Produce 8 artifacts, commit command, kickoff message
- End session cleanly

### Why this matters

Cirra Way §Law 4 — OWN THE FAILURE. A sub-CTO that runs out of context mid-brief loses work and forces Clement to re-explain everything to the next CTO. That is the exact failure mode `/handover` exists to prevent. Issuing the warning early is competent operation, not weakness. Hiding degradation to "look productive" is the failure mode.

---

## WHEN `/handover` IS TRIGGERED (BY CLEMENT)

Sub-CTO recognizes the trigger from any of:
- `/handover` — most common
- `/handover [reason]` — e.g. `/handover context full`, `/handover transferring to human`, `/handover quality concern`
- "do a detailed handover to another sub cto" — fallback verbal trigger

Sub-CTO response (template):
```
Acknowledged. /handover triggered.

Run this in your terminal:

  bash scripts/handover-snapshot.sh > /tmp/snap.txt && cat /tmp/snap.txt

Paste full output back. I produce 8 docs in next message.

If snapshot script doesn't exist yet, copy from
cirra-cto/scripts/handover-snapshot.sh manually.
```

---

## THE 8 MANDATORY ARTIFACTS

Sub-CTO produces these from snapshot output. Cirra Way §10 + SUB_CTO_HANDOVER_PROTOCOL.md.

### Artifact 1 — `HANDOVER.md` (root, OVERWRITES)
Live tactical truth. 28 sections per the locked template. The doc next session opens with.

### Artifact 2 — `CLAUDE.md` (root, HEADER REFRESH ONLY — do not delete history)
Update the top header block (HEAD SHA, current pointer, soft launch state, Aixin pilot data) and the LIFT-### index table. Preserve historical session notes below.

### Artifact 3 — `docs/handover/HANDOVER_<YYYY-MM-DD>.md` (TIMESTAMPED, never overwrite)
Comprehensive transfer artifact. ~12k-15k words. 13 parts:
1. Mission + Context
2. Clement's Operating Style
3. Current State Snapshot
4. Architecture Truth
5. 5-Agent Orchestration System
6. In-Flight Work
7. Known Debt Register
8. Production Gate Status
9. Architecture Decision Log
10. Secrets + Infrastructure Map
11. Learnings + Regression Patterns (INCLUDING honest self-assessment)
12. Immediate Next Actions
13. Appendices (SQL, file map, brief format, lessons, self-assessment)

### Artifact 4 — `docs/handover/BRIEF_TRACKER.md` (OVERWRITES)
Every brief from `git log --all --oneline | grep LIFT-`. Status, commit hash, smoke status. Grouped by lane. Including PASS / SHIPPED / DEPLOYED / DONE / APPROVED / IN-FLIGHT / QUEUED / RESEARCH / REVERTED / RESOLVED / NOT SHIPPED.

### Artifact 5 — `docs/handover/PRODUCTION_GATES_<YYYY-MM-DD>.md` (TIMESTAMPED)
Per-gate audit results. S1-S10, I1-I7, D1-D3, C1-C5, U1-U3. Each gate: MET / NOT MET / N/A + evidence + remediation effort estimate. Total score X/Y = N%.

### Artifact 6 — `docs/handover/HEALTH_CHECK_<YYYY-MM-DD>.md` (TIMESTAMPED)
Raw command outputs from CODEBASE_HEALTH_CHECK_SOP.md. 10 points. Final grade GREEN / AMBER / RED with reason.

### Artifact 7 — `docs/handover/INFRASTRUCTURE_MAP.md` (OVERWRITES)
Where every secret lives. Supabase project ref + region. Cloudflare Pages projects. Sentry DSN location. OneMap token storage. Mapbox token. 1Password vault names. Edge Function list with auth + secret deps. NEVER include actual secret values — only locations.

### Artifact 8 — `docs/handover/PENDING_SMOKES_<YYYY-MM-DD>.md` (TIMESTAMPED)
Every shipped-but-unsmoked commit. Brief / surface / smoke steps / blocker if any. Plus all NOT-SHIPPED outstanding briefs.

---

## DOC GENERATION RULES

### Rule 1 — No Fabrication
Every number, file path, brief number, commit SHA, score traces directly to a section in the snapshot. If snapshot section is empty, mark "UNVERIFIED" or "NOT AVAILABLE FROM SNAPSHOT" — never guess.

### Rule 2 — No New Questions
The snapshot is the contract. If you find yourself wanting to ask Clement a question, the snapshot is incomplete. Either:
- Mark UNVERIFIED in the doc and continue
- Tell Clement to re-run a specific snapshot section
Never block the handover on a clarifying question.

### Rule 3 — One Output Per Doc
Use `create_file` once per artifact. Do not split a doc across multiple files. Doc 3 (the comprehensive transfer) can be 15k words in one file — that is correct.

### Rule 4 — Preserve History
`CLAUDE.md` has session notes going back weeks/months. Update the header. Update the LIFT-### index. DO NOT delete the historical session notes below.

### Rule 5 — Honest Self-Assessment
The comprehensive transfer artifact (Doc 3) MUST include §11 with mistakes from this session. Cirra Way §Law 4 ("OWN THE FAILURE"). The next CTO needs to know what failure modes happened, not just what shipped clean.

### Rule 6 — Cite The Snapshot
When a number is in a doc, reference the snapshot section it came from at first use:
```
**Type escapes:** 107 (snapshot §7)
**Console statements:** 55 (snapshot §8)
```

### Rule 7 — File Path Convention
All files Sub-CTO creates go to `/mnt/user-data/outputs/` for `present_files`. The user moves them to repo locations. Filenames in the user-facing message must EXACTLY match repo target paths so Clement can construct one `cp` command per file.

---

## ONE-COMMIT REQUIREMENT

Sub-CTO ends with ONE copy-pasteable terminal block that commits all 8 artifacts:

```
cd /Users/clement/Code/lift-mobility-core && \
  cp ~/Downloads/HANDOVER.md HANDOVER.md && \
  cp ~/Downloads/CLAUDE.md CLAUDE.md && \
  mkdir -p docs/handover && \
  cp ~/Downloads/HANDOVER_<DATE>.md docs/handover/ && \
  cp ~/Downloads/BRIEF_TRACKER.md docs/handover/ && \
  cp ~/Downloads/PRODUCTION_GATES_<DATE>.md docs/handover/ && \
  cp ~/Downloads/HEALTH_CHECK_<DATE>.md docs/handover/ && \
  cp ~/Downloads/INFRASTRUCTURE_MAP.md docs/handover/ && \
  cp ~/Downloads/PENDING_SMOKES_<DATE>.md docs/handover/ && \
  git add HANDOVER.md CLAUDE.md docs/handover/ && \
  git status && \
  git commit -m "docs: handover snapshot <DATE> — sub-CTO transfer (SHA <X>)" && \
  git push origin main
```

`<DATE>` substituted with snapshot date. `<X>` substituted with locked SHA from snapshot §1.

---

## INCOMING-CTO KICKOFF MESSAGE

Sub-CTO ends with a copy-pasteable kickoff block for Clement to paste into the new Claude project:

```
=== KICKOFF — INCOMING SUB-CTO (paste this into new Claude project) ===

You are the new Sub-CTO for LIFT Mobility / Cirra Edge.

Step 1 — Read these files in order, fully (~2 hours total):
  1. /HANDOVER.md
  2. /CLAUDE.md
  3. /docs/handover/HANDOVER_<DATE>.md ← comprehensive transfer artifact
  4. /STATE_MACHINES.md
  5. /TECH_DEBT.md
  6. cirra-cto/THE_CIRRA_WAY.md
  7. cirra-cto/HANDOVER_PROMPT.md ← /handover system + self-trigger protocol
  8. cirra-cto/PATTERNS.md (apply patterns tagged for this stack)
  9. cirra-cto/LEARNINGS.md (latest entries)

Step 2 — Verify state independently. Run:
  cd /Users/clement/Code/lift-mobility-core && bash scripts/handover-snapshot.sh

Compare your snapshot to docs/handover/HANDOVER_<DATE>.md §3
(Current State Snapshot). Discrepancies = red flag — investigate
before any action.

Step 3 — Produce Takeover Assessment per cirra-cto/SUB_CTO_HANDOVER_PROTOCOL.md.
Submit to Clement for Group CTO relay.

Step 4 — Wait for FULL or CONDITIONAL approval before any briefs.

Step 5 — On approval, first action: <FIRST_ACTION_FROM_OUTGOING>

CRITICAL — Self-trigger handover before context exhaustion.
Read cirra-cto/HANDOVER_PROMPT.md §SELF-INITIATED HANDOVER PROTOCOL.
At ~70% context full OR after ~40 exchanges OR if degradation detected,
issue the warning block and propose /handover proactively. Do NOT
wait until Clement notices.

Outgoing sub-CTO closing date: <DATE>
Outgoing sub-CTO closing SHA:  <SHA>
Open critical items:           <COUNT_FROM_PENDING_SMOKES_DOC>

=== END KICKOFF ===
```

`<FIRST_ACTION_FROM_OUTGOING>` is a one-line specific action — e.g. "Smoke LIFT-855 Live Map (3 tabs render, 10 Aixin vehicles)".

---

## COMPLETENESS CHECKLIST

Sub-CTO ends EVERY `/handover` response with this checklist. Each item ✅ or ❌. If any ❌, sub-CTO fixes before stopping.

```
HANDOVER COMPLETENESS CHECK:
[ ] Snapshot received and validated
[ ] Artifact 1 — HANDOVER.md (live tactical, 28 sections)
[ ] Artifact 2 — CLAUDE.md (header refreshed, history preserved)
[ ] Artifact 3 — HANDOVER_<DATE>.md (comprehensive, 13 parts)
[ ] Artifact 4 — BRIEF_TRACKER.md (every brief from snapshot §2)
[ ] Artifact 5 — PRODUCTION_GATES_<DATE>.md (gates from snapshot §3-15)
[ ] Artifact 6 — HEALTH_CHECK_<DATE>.md (raw output from snapshot §6-11)
[ ] Artifact 7 — INFRASTRUCTURE_MAP.md (from snapshot §19-22)
[ ] Artifact 8 — PENDING_SMOKES_<DATE>.md (from snapshot §18 + manual recall)
[ ] One-commit terminal block produced
[ ] Incoming-CTO kickoff message produced
[ ] §11 of HANDOVER_<DATE>.md includes honest self-assessment
[ ] Zero fabricated numbers — every claim traces to snapshot section
[ ] Locked SHA matches snapshot §1
```

If any unchecked → flag the gap to Clement explicitly, do not silently produce incomplete handover.

---

## SUB-CTO BEHAVIOR DURING `/handover`

### Allowed
- Producing 8 artifacts as `create_file` calls to `/mnt/user-data/outputs/`
- Pulling text directly from snapshot output sections
- Cross-referencing prior commits visible in snapshot §2 + §18
- Citing previous handover docs at `docs/handover/HANDOVER_<earlier>.md` for continuity
- Self-assessment in Doc 3 §11 (mistakes from current session)

### Forbidden
- Asking Clement clarifying questions before producing artifacts
- Skipping any of the 8 artifacts
- Inventing brief numbers, file paths, SHAs, or test counts not in snapshot
- Splitting one doc across multiple files
- Deleting historical content from CLAUDE.md
- Including actual secret values (passwords, keys, tokens) anywhere
- Producing artifacts in any format other than markdown
- Adding new "while you're at it" tasks or feature briefs

### Required Closing Move
After completeness checklist passes, sub-CTO ends with EXACTLY this:
```
HANDOVER PRODUCED. 8 artifacts ready in /mnt/user-data/outputs/.

NEXT (Clement runs):
  1. <one-commit terminal block>
  2. Spin up new Claude project
  3. Paste kickoff message above into new project
  4. Wait for Takeover Assessment from incoming CTO

Outgoing sub-CTO standing by until commit confirmed.
```

Then stop. No extra prose. No "let me know if you need anything."

---

## VERSIONING

This file lives in `cirra-cto/HANDOVER_PROMPT.md`. When updated:
- Bump version at top
- Document change in `cirra-cto/CHANGELOG.md`
- Broadcast change to all active sub-CTO projects (Clement does this manually until automation exists)

---

## ESCAPE HATCH

If `/handover` cannot be completed (e.g. snapshot incomplete + sub-CTO context exhausted), sub-CTO must:

1. Output a `PARTIAL_HANDOVER.md` artifact listing exactly what IS captured and what IS NOT
2. Tell Clement which artifact slots are EMPTY (1-8)
3. Provide the snapshot input to be re-paste-able into a fresh sub-CTO Claude project
4. End with: "PARTIAL HANDOVER — incoming CTO must complete remaining artifacts as Day 1 work per cirra-cto/HANDOVER_PROMPT.md."

Do NOT fabricate to fill artifact slots. Empty + flagged > false + "complete."

---

## CHANGELOG

**v1.2 (May 2026):**
- Step 1.5 mandatory inline SESSION_DECISIONS / SESSION_TECH_DEBT append scripts; HANDOVER_TEMPLATE + `handover-validate` pointer for docs-close commits.
**v1.1 (April 2026):**
- Added Self-Initiated Handover Protocol section with 3 mandatory triggers
- Locked warning block format
- Defined Clement override behavior
- Updated kickoff message to reference self-trigger protocol

**v1.0 (April 2026):** Initial release.

---

*HANDOVER_PROMPT.md | Version 1.2 | Cirra Edge | May 2026*
*Locked: any change requires Group CTO sign-off + version bump.*
