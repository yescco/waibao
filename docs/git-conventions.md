# Git 约定式提交规范

执行 git commit 之前，先读完本文。

- 不主动 commit / push，除非用户明确要求。
- commit message 遵循约定式提交（Conventional Commits），格式 `<type>(<scope>): <subject>`，subject 用中文短句、祈使语气、结尾不加句号。
  - `type` 取 `feat`（新增/变更工作流能力）、`fix`（修复工作流缺陷）、`docs`（纯文字润色与结构整理）、`refactor`、`chore`。
  - `scope` 可选，取受影响的文件或模块：`skill`、`dev-prompt`、`qa-prompt`、`e2e`、`readme`、`agents`、`docs`；跨多文件时省略。
  - 破坏性变更在 type 后加 `!`（如 `feat!:`），并在 body 写明影响与迁移方式。
  - 示例：`feat(skill): 新增概要设计环节，PRD 确认后先产出 hld.md 再规划迭代`；`fix(qa-prompt): E2E 必须显式 headless: false，禁止无头模式`。
  - 历史提交中 `dev-prompt:` / `qa-prompt:` 开头的旧风格为遗留格式，不回溯改写，仅约束新提交。
