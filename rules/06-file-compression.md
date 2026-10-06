# 6. File compression

**Rule:** Compress natural-language files (.md, .txt, .typ, .tex) to cut input tokens. Backups go OUT-OF-TREE (e.g. `%LOCALAPPDATA%` on Windows) so auto-loaders never re-ingest them.

**Remove:** articles, filler, hedging, redundant phrasing ("in order to" → "to"), connective fluff ("however", "furthermore").

**Compress:** short synonyms, fragments OK, drop "you should"/"make sure to"/"remember to", merge redundant bullets, collapse duplicate examples to one.

**Preserve EXACTLY:** code blocks (verbatim), inline backticks, URLs, paths, commands, env vars, technical terms, dates/versions, heading text, bullet nesting, table structure, frontmatter. These carry machine- or link-sensitive meaning.

**Boundaries:**

- NEVER modify .py/.js/.ts/.json/.yaml/.yml/.toml/.env/.lock/.css/.html/.xml/.sql/.sh
- Mixed content → compress prose only; unsure → leave unchanged
- Fail after 2 retries → report error, leave original untouched