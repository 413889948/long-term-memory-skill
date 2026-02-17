---
name: long-term-memory
description: Provides durable long-term memory for conversations. Create memory files when users ask to remember/save something, and recall by keyword with progressive disclosure (load only relevant files).
---

# Long-Term Memory Skill

## Purpose

Store user-requested long-term information inside this skill's memory store and recall it later by keyword.

References:

- `WORKFLOW.md`: Write/recall examples and conflict handling.
- `memory/templates/memory-entry.md`: Entry template.

## Hard Scope

- Only read/write long-term memory files inside this skill directory.
- The memory root is fixed to `memory/`.
- Never write long-term memory to other repository paths.

## Trigger Rules

Enable this skill when either intent appears:

- **Write intent**: User asks to remember, save, archive, or record something.
- **Recall intent**: User asks to recall previous info, or references a previously stored keyword/topic.

## Storage Layout

`
memory/
  index.json
  overview.md
  items/
    YYYY/
      mem-YYYYMMDD-HHMMSS-<slug>.md
  templates/
    memory-entry.md
`

## Data Contract

### 1) `memory/index.json`

- Global index for fast keyword-to-entry lookup.
- Each `entries[]` item contains: `id`, `title`, `summary`, `keywords`, `file`, `created_at`, `updated_at`, `confidence`.
- `keyword_map` is an inverted index from keyword to `id[]`.

### 2) `memory/items/**/*.md`

- One long-term memory per file.
- Use YAML frontmatter, and append details/context below.

### 3) `memory/overview.md`

- Keep only high-level summaries and topic navigation.
- Do not place full details here to avoid prompt bloat.

## Progressive Disclosure Retrieval

During recall, always use layered retrieval and do not load all memories at once:

1. Extract 1-5 keywords from the user request.
2. Read only `memory/index.json` and use `keyword_map` to find candidate `id` values.
3. Rank candidates using `summary` and choose the top 1-5.
4. Open only those memory files for details.
5. Return the answer with matched keywords and entry titles.

If there is no exact match:

- Fallback to keyword search under `memory/items/`.
- Still load only the most relevant small subset of files.

## Write Workflow

When the user explicitly asks to remember something:

1. Distill the fact(s) to remember, then create a concise `title` and `summary`.
2. Create an entry file at `memory/items/YYYY/mem-YYYYMMDD-HHMMSS-<slug>.md`.
3. Update `memory/index.json`:
   - append/update `entries[]`
   - update `keyword_map`
   - update timestamp
4. Update topic-level summary in `memory/overview.md`.
5. Confirm what was remembered and provide useful recall keywords.

## Read Workflow

When the user asks to recall or mentions a stored keyword:

1. Extract keywords and query `memory/index.json`.
2. Read only matched entry files.
3. In the response, include:
   - matched keywords
   - source entry title(s)
   - `updated_at` when available

## Description Growth Policy

Do not keep expanding this file's `description` with memory content.

- Keep `description` focused on capability and trigger conditions.
- Put growing memory summaries in `memory/overview.md`.
- Keep full details in individual files under `memory/items/`.

This keeps the skill maintainable and enables true progressive disclosure.
