# Memory Store Guide

## Goal

This directory is the only long-term memory storage area for the `long-term-memory` skill.

## Files

- `index.json`: Global index and inverted keyword map.
- `overview.md`: Topic-level summary view.
- `items/YYYY/*.md`: One memory item per file.
- `templates/memory-entry.md`: Memory entry template.

## Rules

- Every new memory must update `items/`, `index.json`, and `overview.md` together.
- During recall, query `index.json` first, then open only matched entries.
- Never load all `items/` files at once.

## Minimal Recall Flow

1. Extract keywords from user request.
2. Find candidate `id` values in `index.json.keyword_map`.
3. Resolve `file` and `summary` from `entries`.
4. Read only top 1-5 relevant entry files.
5. Return recall with matched keywords.
