# Long-Term Memory Workflow

## Write Example

### User input

`记住：我在 opencode 项目里偏好先用 explore 子代理做代码结构扫描。`

### Expected action

1. 生成条目文件到 `memory/items/YYYY/mem-*.md`。
2. `keywords` 至少包含: `opencode`, `explore`, `偏好`。
3. 更新 `memory/index.json` 与 `memory/overview.md`。
4. 回答用户可用于回忆的关键词。

## Recall Example

### User input

`回忆一下我之前关于 opencode 的偏好。`

### Expected action

1. 提取关键词: `opencode`, `偏好`。
2. 读取 `memory/index.json` 并定位候选条目。
3. 只读取相关条目文件，不全量展开。
4. 输出命中内容，并注明命中关键词与条目标题。

## Conflict Handling

- 若新记忆与旧记忆冲突：
  - 保留旧条目，不直接删除。
  - 新建条目并在 `Details` 标注“supersedes: <old_id>”。
  - 在 `index.json` 中将旧条目 `confidence` 下调为 `low` 或在摘要中标注已被替代。
