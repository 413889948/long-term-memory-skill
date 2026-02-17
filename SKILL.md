---
name: long-term-memory
description: 为会话提供可持续的长期记忆能力。用户提到“记住/记录/存档”时创建记忆文件；用户提到“回忆/之前说过/关键词”时按关键词检索并只加载相关记忆文件（渐进式披露）。
---

# Long-Term Memory Skill

## Purpose

将用户要求长期保存的信息写入当前 skill 目录内的记忆库，并在后续按关键词精准回忆。

参考资料:

- `WORKFLOW.md`: 写入/回忆示例与冲突处理。
- `memory/templates/memory-entry.md`: 条目模板。

## Hard Scope

- 只允许在本 skill 目录内读写长期记忆文件。
- 记忆文件根目录固定为 `memory/`。
- 禁止把长期记忆写到仓库其他路径。

## Trigger Rules

当出现以下意图时启用此 skill：

- **写入意图**: 用户说“记住”“帮我记一下”“长期保存”“记录这件事”等。
- **回忆意图**: 用户说“回忆一下”“之前我说过”“你记得吗”“关于 <关键词> 之前怎么说的”等。

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

- 全局索引，支持关键词到条目的快速定位。
- `entries[]` 每项包含: `id`, `title`, `summary`, `keywords`, `file`, `created_at`, `updated_at`, `confidence`。
- `keyword_map` 为关键词到 `id[]` 的倒排索引。

### 2) `memory/items/**/*.md`

- 每条长期记忆一个文件。
- 文件头使用 YAML frontmatter，正文可追加上下文与来源。

### 3) `memory/overview.md`

- 只保留高层概览与主题导航。
- 不放全量细节，避免提示词膨胀。

## Progressive Disclosure Retrieval

回忆时严格使用分层检索，不要一次性加载所有记忆：

1. 先从用户请求提取 1-5 个关键词。
2. 仅读取 `memory/index.json`，通过 `keyword_map` 找候选 `id`。
3. 读取候选条目的 `summary` 与 `file`，按相关度选前 1-5 条。
4. 只打开这些条目的文件获取详细信息。
5. 回答后列出命中的关键词与条目标题。

若没有精确命中：

- 退化为对 `memory/items/` 的关键词搜索。
- 仍然只加载最相关少量文件，不做全库扫描式展开。

## Write Workflow

当用户明确要求记忆时：

1. 归纳要记住的事实，生成简洁 `title` 与 `summary`。
2. 生成条目文件 `memory/items/YYYY/mem-YYYYMMDD-HHMMSS-<slug>.md`。
3. 更新 `memory/index.json`:
   - 追加或更新 `entries[]`
   - 更新 `keyword_map`
   - 更新时间戳
4. 更新 `memory/overview.md` 的主题概览（仅摘要级）。
5. 返回确认信息：已记住内容 + 可用于回忆的关键词。

## Read Workflow

当用户要求回忆或提到旧关键词时：

1. 提取关键词并查 `memory/index.json`。
2. 只读取命中条目文件。
3. 输出回忆结果时标注：
   - 命中关键词
   - 来源条目标题
   - 如有时间，附 `updated_at`

## Description Growth Policy

不要把越来越多的记忆内容塞进本文件的 `description`。

- `description` 只描述能力与触发条件。
- 记忆概述增长放在 `memory/overview.md`。
- 详细信息始终放在 `memory/items/` 单条文件。

这样可以保持 skill 可维护，并实现真正的渐进式披露。
