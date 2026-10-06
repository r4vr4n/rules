# 17. Grilling decisions (stress-test before building)

- Map the decision as a design tree: decisions branch into dependent decisions — makes dependencies explicit
- Work in **rounds**: each round asks the whole **frontier** (all questions whose prerequisites are settled) — prevents premature answers, enables parallel discovery
- Number each question and give a recommended answer — forces clear options
- **Facts = agent's job** — dispatch subagents to investigate, don't ask the user
- **Decisions = user's job** — put each decision to them and wait. Ownership stays with the user.
- Done when the frontier is empty (no silent assumptions) — prevents "I thought you meant..." later