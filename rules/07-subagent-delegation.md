# 7. Subagent delegation

**Rule:** Use subagents to shrink main context (~60% smaller results). Rule of thumb: want output in 1/3 the tokens → terse subagents; want prose → full-capability agents.

| Task                                            | Use                          |
| ----------------------------------------------- | ---------------------------- |
| "Where is X / what calls Y"                     | investigator                 |
| Same + architecture commentary                  | Explore agent                |
| Surgical edit, ≤2 files, scope obvious          | builder                      |
| New feature / 3+ files / cross-cutting refactor | main thread                  |
| Review diff for bugs                            | reviewer                     |
| Deep review with rationale                      | full-capability review agent |
| One-line answer already known                   | main thread, no subagent     |

**Agent contracts (ultra-terse):**

- **investigator:** locate, report, stop. Never edit, never propose fixes. Rows: `<path:line> — symbol — ≤6-word note`, group headers (Defs:/Refs:/Callers:/Tests:) at 3+ rows. Zero hits → `No match.`
- **builder:** 1 file ideal, 2 OK, 3+ refuse (`too-big.`). Edit existing only; no new abstractions, no drive-by refactors, no comment additions, no Bash. Read → smallest diff → re-read verify → receipt (`verified: OK|mismatch`). Refusal tokens: `needs-confirm.` / `ambiguous.` / `regressed.`
- **reviewer:** findings only, no praise, no scope creep. Security findings: plain-English risk sentence first, then terse fix line.

**Why the contracts matter:** a subagent that expands scope silently burns the context you delegated to save.

**Patterns:** locate→fix→verify chain · parallel scouts (2-3 investigators) · skip the investigator when the site is already known.

**Never:** builder without knowing the file first; investigator→builder chains on 5-file refactors (keep big work in the main thread).