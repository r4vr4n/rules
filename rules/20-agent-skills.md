# 20. Agent skills (SKILL.md) — authoring

**Anatomy:** skill = directory: `SKILL.md` (required, exact spelling) + optional `scripts/`, `references/`, `assets/`; no README.md inside the folder. Frontmatter: `name` kebab-case ≤64 chars matching the folder name; `description` <1024 chars, third person.

**Description discipline:** must answer BOTH _what it does_ AND _when to use_ — include trigger phrases the user would actually say, key terms, file types. Vague descriptions ("helps with documents") never fire. Add anti-triggers (when NOT to use) when confusion is likely.

**Progressive disclosure — three tiers:** metadata always in context (~100 tokens/skill) → SKILL.md body loaded on trigger (<5k tokens) → `references/` files read only when needed. Cheap to carry, deep on demand.

**Body discipline:**

- Under 500 lines; target 150-300. Past ~500 you almost always have 2-3 skills masquerading as one — split by lifecycle / role / level.
- Highest-signal first: one-line bolded summary → When to Use (3-6 concrete situations) → core concept (≤5 sentences) → minimal example → deeper patterns → anti-patterns
- Imperative instructions ("Do X"), never "consider X"; runnable examples with zero boilerplate the reader already knows
- Exclude: feature history, marketing prose, exhaustive edge cases (pick top 3), things the model already knows, long API-param tables (link official docs)
- Scannability test: a 10-second skim answers what is this / when do I use it / minimal example

**Scripts:** deterministic script > model generation wherever possible — code for reliability, instructions for judgment, references for factual lookup. Handle errors explicitly inside scripts — never "fail and let the model figure it out". No voodoo constants (justify every value); state dependencies explicitly and verify availability. MCP tools fully qualified (`ServerName:tool_name`) — bare names fail with multiple servers. Complex multi-step ops: numbered steps + copyable checklist; validation loop (draft → check → fix → repeat until pass).

**Lifecycle:** build the skill AFTER solving the task once manually — extract the winning approach. Trigger tests: fires on obvious tasks ✓, fires on paraphrases ✓, does NOT fire on unrelated topics ✗; ≥3 evals from REAL past tasks; retest across models. Version skills like code and re-test whenever referenced data/schema changes — a skill can pass every check and still be confidently wrong on stale data.

**Security:** frontmatter is injected into the system prompt — treat third-party skills as untrusted input (~26% of community skills carry security issues); review before install.