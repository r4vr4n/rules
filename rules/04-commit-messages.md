# 4. Commit messages

**Rule:** Conventional Commits, imperative, why over what.

- `<type>(<scope>): <imperative summary>` — types: feat, fix, refactor, perf, docs, test, chore, build, ci, style, revert
- ≤50 chars when possible, hard cap 72, no trailing period, match project capitalization after the colon
- Body only if needed: non-obvious why, breaking changes, migration notes, linked issues (`Closes #42`, `Refs #17`); wrap body at 72, issue refs at the end; attribution requests go in a `Co-authored-by:` trailer, never the subject
- **Always include a full body** for breaking changes, security fixes, data migrations, reverts — a subject-only message hides exactly the information future readers need.

**Boundary:** generate the message only — never run `git commit`, stage, or amend. Output a ready-to-paste code block.

**Never:** "This commit does X", "I/we", "now", "currently", AI attribution, emoji (unless convention requires), restating the scope.