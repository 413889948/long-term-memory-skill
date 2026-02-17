# Long-Term Memory Skill for OpenCode

A scalable long-term memory skill that supports both memory writing and keyword-based recall using progressive disclosure, so you never need to load the full memory history every time.

## Features

- Write durable memory entries when users ask to remember/save/archive something.
- Recall previous memory by keywords or topic mentions.
- Query index first, then load only top relevant entries.
- Keep all memory files inside this skill directory for portability and version control.

## Project Structure

```text
.
  SKILL.md
  WORKFLOW.md
  README.md
  memory/
    README.md
    overview.md
    index.json
    items/
      .keep
    templates/
      memory-entry.md
```

## How It Works

### Write Memory

1. Detect explicit memory intent (for example: "remember this").
2. Create one entry file under `memory/items/YYYY/`.
3. Update `memory/index.json` (entry metadata + inverted keyword index).
4. Update `memory/overview.md` (summary-level topic overview only).

### Recall Memory

1. Extract 1-5 keywords from the user query.
2. Read only `memory/index.json` to locate candidate entries.
3. Open only the top 1-5 relevant memory files.
4. Return recall result with matched keywords and source entries.

## Why Not Store All Memory in Skill Description

Putting all historical memory into `SKILL.md` `description` does not scale:

- Prompt size and token cost grow quickly.
- Signal-to-noise quality drops.
- Maintenance and conflict handling become harder.

This project uses a stable 3-layer model:

- `description`: capability + trigger conditions only.
- `memory/overview.md`: topic-level summary only.
- `memory/items/*.md`: full long-term memory details.

## Usage

Place this directory into your OpenCode skills path and trigger by intent:

- Write example: `Remember that I prefer using explore subagents first for repo structure scanning.`
- Recall example: `Recall what I said before about my OpenCode preference.`

See `WORKFLOW.md` for more examples.

## Notes

- Current implementation is file-backed keyword retrieval.
- For larger scale, you can later upgrade to SQLite/FTS or hybrid vector retrieval.
