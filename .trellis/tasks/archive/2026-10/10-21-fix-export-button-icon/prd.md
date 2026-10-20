# Fix export button icon

## Goal

修复记录列表导出文本按钮显示为空白的问题，补齐 Shadow DOM Font Awesome 图标映射。

## Background

- 记录操作区已经渲染 `fa-solid fa-download`，按钮布局和交互事件均已存在。
- `index.js:96-128` 的 `FA_SOLID_CONTENT` 是 Shadow DOM 内部图标字形的白名单；`buildFaShimStyle()` 在 `index.js:6765-6766` 只为该白名单生成 `::before` 内容规则。
- 当前白名单包含查看全文使用的 `file-lines` 和删除使用的 `trash-can`，但不包含导出按钮使用的 `download`，因此导出按钮保留空白按钮盒而没有图标字形。
- 记录折叠态的统一隐藏规则位于 `style.css:1106-1115`；导出按钮需要与查看全文、删除等记录操作按钮遵循同一显示规则。

## Requirements

- 在现有 `FA_SOLID_CONTENT` 中补齐 `download` 图标的 Font Awesome solid 字形编码。
- 保留导出按钮现有的 `fa-solid fa-download` 标记、点击行为、导出文件内容和文件命名规则。
- 记录折叠时隐藏导出按钮；记录展开时按原有操作区样式显示导出按钮。
- 不改变查看全文、删除或其他图标的显示和行为。

## Acceptance Criteria

- [x] 记录列表中导出文本按钮显示可见的下载图标，不再出现白色空白按钮。
- [x] 记录折叠时导出按钮不可见，展开记录后与查看全文、删除按钮一起按原有操作区样式显示。
- [x] 查看全文和删除按钮图标仍可见，三类操作按钮布局不发生非预期变化。
- [x] 导出按钮点击后仍按既有逻辑生成“全文”文本，并在存在 `RESPONSE` 时额外生成 `RESPONSE` 文本。
- [x] 通过 JavaScript 语法检查，并检查变更范围只涉及图标映射、折叠态显示行为及本任务所需的任务记录。

## Completion Notes

- 已同步更新 `.trellis/spec/frontend/quality-guidelines.md` 和 `.trellis/spec/guides/code-reuse-thinking-guide.md`，记录 Shadow DOM 图标映射约定。
- 未发现需要同步的用户-facing Markdown；README 仍准确描述当前功能。
- 实际 SillyTavern 宿主中的字体加载和折叠/展开视觉效果仍需刷新扩展后验收。

## Out of Scope

- 不更换图标字体、引入新的图标依赖或重做操作区样式。
- 不修改导出内容格式、按钮交互、文件命名或其他记录功能。
- 不改变其他记录操作按钮在折叠态和展开态的既有行为。

## Risks and Deferred Validation

- 静态检查无法完全替代 SillyTavern 实际宿主中的 Shadow DOM 字体加载验证；最终仍需在宿主中刷新扩展并确认图标可见。
- 本任务是独立 bug 修复，不涉及旧实现替换或迁移，因此无需删除兼容实现。

## Notes

- Lightweight task: PRD-only is sufficient.
