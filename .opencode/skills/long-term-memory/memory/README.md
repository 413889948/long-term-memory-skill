# Memory Store Guide

## Goal

该目录是 `long-term-memory` skill 的唯一长期记忆存储区域。

## Files

- `index.json`: 索引与关键词倒排表。
- `overview.md`: 主题概览（摘要级）。
- `items/YYYY/*.md`: 具体记忆条目，一条一文件。
- `templates/memory-entry.md`: 条目模板。

## Rules

- 新增记忆时，必须同时更新 `items/`、`index.json`、`overview.md`。
- 回忆时，先查 `index.json`，再按命中加载少量条目文件。
- 禁止全量加载所有 `items/` 文件。

## Minimal Recall Flow

1. 抽取用户问题关键词。
2. 在 `index.json.keyword_map` 找候选 `id`。
3. 从 `entries` 定位 `file` 与 `summary`。
4. 只读最相关 1-5 个条目文件。
5. 输出回忆结果并附命中关键词。
