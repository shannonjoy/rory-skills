---
name: example-skill
description: "One paragraph that decides when this skill runs. Say what it owns, the phrases that should trigger it, and — just as important — what it never does and which skill owns that instead."
---

# Example Skill — one-line purpose

**Owns:** the single job this skill is responsible for.
**Never:** the adjacent jobs it must hand off (name the owning skill for each).

## Inputs (read live, every run)
- Source of truth #1 (e.g. a tracker database) — never answer from memory.
- Source of truth #2.

## Steps
1. Preflight: confirm the sources are reachable; if not, say so first and stop.
2. Do the work.
3. Verify against a real artifact before reporting anything as done.

## Autonomy line
| Inside the line — do it, report after | Outside the line — queue for the human |
|---|---|
| reads, analysis, drafts held as drafts | anything outbound (send, post, publish) |
| writes to the owner's own workspace | spending, credentials, deletions |
| reversible changes with a named rollback | legal-adjacent or irreversible actions |

## Output
- One mobile-readable result: what changed (with evidence), what's next, what needs the human (numbered, max five, each with the literal answers they can give).
- Honest zeros. Never pad a slow run.

## Scheduled-run rules
No clarifying questions; take the most reasonable reading and say which. Fail loudly with the cause named. Stamp the run so the liveness sentinel can see it.

## Lessons
- Dated one-liners added whenever something surprised the skill. Newest first.
