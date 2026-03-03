# CLAUDE.md — EDCR v2.0

## RULES — ABSOLUTE. VIOLATION IS FAILURE.

**0. Session gate — HOOK ENFORCED.**
All source reads blocked until `/tmp/.blink_session_booted` exists.
Run `Skill("session-start")` NOW. Not later. NOW.

**1. Unknown file → `Skill("smart-index")` FIRST.**
Run `index_query` (1 unknown) or `index_batch` (2+). NEVER grep, find, ls, Glob, or Grep for discovery.
Skip ONLY when path is confirmed from CLAUDE.md, user message, error output, or Gemini result THIS session.
Uncertain = unknown = fire the skill.

**2. External docs → `Skill("nlm")` FIRST.**
Run `nlm ask` BEFORE any web search. Fallback: `docs/reference/` only.

**3. PROGRESS.md — milestones only.**
Update on: task complete, blocker, direction change, session start/end. ~2–5 per session.
Every entry MUST have "Known issues" (or "No known issues").

**4. File boundary is law.**
NEVER touch files outside `active-plan.md` File Boundary. New file → add to boundary FIRST.

**5. Skills are atomic.**
When a skill triggers, complete it IN FULL before returning. No interleaving. No "later."

---

## `!p` — Protocol Reset

Read `.claude/active-plan.md` → route by Status:

| Status | Action | Status | Action |
|--------|--------|--------|--------|
| empty/absent | SESSION START | `paused` | Inform user, await direction |
| `approved` | PRE-EXECUTE | `delegating` | DELEGATION |
| `in-progress` | EXECUTION | | |

---

## SESSION START
`Skill("session-start")` — complete every step.

## TASK RECEIVED
Active plan `in-progress` → ask user: pause or finish first.
**Size:** S (single file, obvious) → approval → EXECUTION. M/L → PLANNING.
**Tier override:** Check PROGRESS.md `### Tier Overrides`.
**Debug tasks are NEVER Size S.** Minimum M.

## PLANNING
1. `Skill("smart-index")` — `index_analyze`/`index_trace` to map files
2. Check `.claude/references/tiering.md` if escalation/delegation needed
3. Write plan per `Skill("active-plan")` format → `.claude/active-plan.md`
4. Present to user → on approval: Status `in-progress`

Size M → inline pre-execute (paths confirmed? git clean? correct branch?).
Size L → PRE-EXECUTE separately.

## PRE-EXECUTE
Size L only. `active-plan.md` approved, paths confirmed via index, git clean, correct branch. Fail → stash/commit first.

## EXECUTION
Per step: Mark `[>]` → Build/Edit → Verify (lint→test→build) → `Skill("commit-and-log")` → Mark `[x]`, next `[>]`.
Unknown location → `Skill("smart-index")`.
Blocked → diagnose → revert → if stuck → ESCALATION.
Unrelated issues → PROGRESS.md Task Queue. No context-switch.

## DELEGATION
`Skill("delegation")` — complete the protocol.

## ESCALATION
Before escalating:
- [ ] All work committed
- [ ] Alternative tried at current tier
- [ ] Report via `.claude/templates/escalation-report.md`
- [ ] Expected output is specific and verifiable

After: `- [area] → minimum [Tier] — escalated from [tier] on [YYYY-MM-DD], reason: [one-line]`

## SESSION END
- [ ] Stage + commit all work (even `wip:`) → push
- [ ] PROGRESS.md entry: What, Files, Next, Known issues
- [ ] `active-plan.md`: done → clear; paused → keep state
- [ ] Brief developer summary

Compact PROGRESS.md if >60 lines (see `Skill("commit-and-log")`).

## COMPACTION RECOVERY
Read `.claude/active-plan.md` → follow `!p` routing.
Full recovery: `Skill("session-start")` § Compaction Recovery.

---

## SKILL TRIGGERS

Every trigger fires. No rationalization. Uncertain whether it applies → it applies.

| Skill | Invoke | Trigger | Skip |
|-------|--------|---------|------|
| Session Start | `Skill("session-start")` | New session. `!p` with no plan. ALWAYS first. | NEVER |
| Active Plan | `Skill("active-plan")` | Creating, transitioning, or pausing ANY plan. | NEVER |
| Commit & Log | `Skill("commit-and-log")` | After ANY verified change. EVERY step boundary. Session end. | NEVER |
| Smart Index | `Skill("smart-index")` | ANY unknown file path. Architecture mapping. After compaction. | ONLY when path confirmed in context |
| NLM | `Skill("nlm")` | ANY external doc query. BEFORE any web search. | ONLY when notebook not configured |
| Delegation | `Skill("delegation")` | Spawning ANY sub-agent. Reviewing ANY agent return. | NEVER |

**Reference:** `.claude/references/tiering.md` — load only when choosing model tier.
