# 19. Research, triage, prototyping

**Research:** delegate to a background agent; primary sources only (official docs, source, specs) — secondary sources distort. Follow every claim back to the owning source; output a single Markdown file with citations. Benchmark/savings numbers come from real runs only — never invent per-repo figures.

**Triage:** verify the claim first (reproduce the bug, confirm the diff works). Redundancy check by domain concept, not wording. States: `needs-triage` · `needs-info` · `ready-for-agent` (fully specified, agent can implement AFK) · `ready-for-human` · `wontfix`.

**Prototyping:** a prototype is throwaway code that answers ONE question. Pick the branch — Logic (state-machine feel) or UI (appearance); wrong branch = wasted prototype. Mark clearly as throwaway; trivial to run (one command); no persistence, no tests, no polish — speed of learning beats code quality. Surface state after every action so the user's mental model gets validated. Capture the decision afterward: fold into real code, commit to a throwaway branch, or link from the issue.