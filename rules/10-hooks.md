### 10.4 Hooks

| ID | Rule | Tag |
| ---- | ------ | ----- |
| FE-HOOK-001 | Complex feature logic in `src/hooks/<feature>/use-*.ts` with descriptive names (`expandLineageUpstream`, not `handleExpand`); components stay thin | 👀 |
| FE-HOOK-002 | Hooks that only wrap API calls or streaming go in `src/hooks/api/` | 👀 |
| FE-HOOK-003 | Before writing a generic browser/OS hook (mouse position, screen/viewport size, OS detection, clipboard, online status, etc.), check `@teragonia/uikit`'s hook catalog, then a well-known community hook library — don't hand-roll one that already exists | 👀 |

**FE-HOOK-003 — Reuse before writing:** Check in order: 1) `@teragonia/uikit` hook catalog, 2) Mantine hooks / `usehooks-ts` / `react-use`, 3) Only then write your own.