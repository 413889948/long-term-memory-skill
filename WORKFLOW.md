# Long-Term Memory Workflow

## Write Example

### User input

`Remember: in OpenCode projects I prefer using explore subagents first for code structure scanning.`

### Expected action

1. Create one entry file under `memory/items/YYYY/mem-*.md`.
2. Include at least these keywords: `opencode`, `explore`, `preference`.
3. Update both `memory/index.json` and `memory/overview.md`.
4. Return useful recall keywords to the user.

## Recall Example

### User input

`Recall my previous preference about OpenCode.`

### Expected action

1. Extract keywords such as `opencode`, `preference`.
2. Read `memory/index.json` to locate candidate entries.
3. Open only relevant entry files, not all files.
4. Return matched content with keywords and entry titles.

## Conflict Handling

- If new memory conflicts with an old memory:
  - Keep the old entry (do not delete directly).
  - Create a new entry and add `supersedes: <old_id>` in `Details`.
  - In `index.json`, downgrade old entry `confidence` to `low` or mark it as superseded in summary.
