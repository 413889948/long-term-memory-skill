# Long-Term Memory Skill for OpenCode

一个可持续增长的长期记忆 Skill，支持“写入记忆 + 关键词回忆”，并采用渐进式披露，避免每次加载全部历史记忆。

## Features

- 用户提到“记住/记录/存档”时写入长期记忆。
- 用户提到“回忆/之前说过/关键词”时按关键词检索。
- 先查索引再按需加载相关条目，避免全量读取。
- 记忆文件全部保存在 skill 自身目录，便于迁移和版本管理。

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

1. 识别用户明确的记忆意图（例如“帮我记住这件事”）。
2. 生成单条记忆文件到 `memory/items/YYYY/`。
3. 更新 `memory/index.json`（条目元数据 + 关键词倒排索引）。
4. 更新 `memory/overview.md`（仅摘要级主题概览）。

### Recall Memory

1. 从请求提取 1-5 个关键词。
2. 只读取 `memory/index.json` 找候选条目。
3. 仅打开最相关的 1-5 个记忆文件。
4. 返回回忆内容并附命中关键词与来源条目。

## Why Not Put Everything in Skill Description

不建议把所有历史记忆都塞到 `SKILL.md` 的 `description` 里。随着记忆增长，会导致：

- 系统提示变长，成本上升。
- 噪声增多，检索精度下降。
- 维护困难，冲突难管理。

本项目采用更稳定的三层结构：

- `description`: 只放能力与触发条件。
- `memory/overview.md`: 只放主题摘要。
- `memory/items/*.md`: 放详细长期记忆。

## Usage

将该目录放入你的 OpenCode 项目后，触发相关意图即可使用：

- 写入示例：`记住：我在 opencode 项目里偏好先用 explore 子代理。`
- 回忆示例：`回忆一下我之前关于 opencode 的偏好。`

更多示例见 `WORKFLOW.md`。

## Notes

- 该 Skill 当前为文件型记忆实现，默认关键词检索。
- 需要更大规模记忆时，可在后续升级为 SQLite/FTS 或向量混合检索。
