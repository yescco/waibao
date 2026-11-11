# AGENTS.md

本文件面向**维护本仓库的 AI 代理**。waibao 被安装运行时注入的是 SKILL.md 与 `references/` 下的提示词（含 `references/e2e/`），与本文件无关；SKILL.md 中「开发前必须阅读 AGENTS.md」指的是**被开发的目标项目**的 AGENTS.md，不是本文件。

## 项目是什么

waibao 是一个 agent 框架 skill（ZCode / dsh / opencode 通用）：Pm / Dev / Qa 三角色协作的全流程项目管理工作流。仓库内**没有可执行代码、依赖与构建**，全部产物是中文提示词文档。

## 文件地图

| 文件 | 角色 | 修改要点 |
| ---- | ---- | -------- |
| `SKILL.md` | Pm 角色设定 + 完整工作流，**运行时权威源** | frontmatter 的 name/description 是框架触发路由的唯一依据 |
| `references/dev-prompt.md` | Dev 子代理提示词，委派时全文注入子代理 | 产出物约定、迭代提交门禁、自评报告模板 |
| `references/qa-prompt.md` | Qa 子代理提示词 | 四维度评分、E2E 执行原则、核心禁令 |
| `references/e2e/index.md` | E2E 工具路由与上报：场景路由表、共通执行规范、Pm 上报处理流程 | Pm/Qa 共用的事实源；新增测试平台在此加路由行，不动角色提示词 |
| `references/e2e/<工具>.md` | 各 E2E 工具说明（playwright-cli / wdio-mcp），涉及 UI 的迭代随 Qa 提示词一并注入 | 场景要点、命令表、有头模式、可用性检测、证据落盘 |
| `agents/openai.yaml` | OpenAI 风格 agent 接口元数据 | `short_description` 须与 SKILL.md 的 description 口径一致 |
| `README.md` | 人类向：安装、使用、流程概览 | 特性与流程描述须与 SKILL.md 一致 |
| `AGENTS.md` | 本文件：仓库维护指南 | 不变量或同步关系变化时同步更新 |

## 核心不变量

修改任何提示词不得破坏以下约束：

1. **非侵入**：工作流产物只写 `~/.agents/waibao/projects/<项目名>/`，项目目录除 Dev 的源码改动外零写入；E2E 工具的安装/注册在用户级完成。
2. **角色权限**：Pm 不碰源码、不执行 git 写操作；Dev 是唯一修改源码并 commit 的角色；Qa 只读代码，只产出报告文本与截图证据。
3. **迭代提交门禁**：迭代通过 Qa 验收（及用户代码评审，若启用）之前，Dev 不得 `git add` / `git commit`；全部通过后由 Dev 一次性提交。
4. **中转通信**：Dev 与 Qa 互不直连，一切信息经 Pm 中转；子代理只返回文本，工作流文档落盘由 Pm 负责。
5. **真实平台 E2E**：被测应用是什么平台就在该平台真实运行上下文中测试（PC Web 用浏览器、Electron 用 CDP attach 应用进程、移动端用真机/模拟器），禁止跨上下文取巧；浏览器一律有头模式。
6. **降级有责**：无子代理机制 → 单角色串行模式；E2E 工具缺失 → 上报 Pm → 用户决策 → 降级模式（功能性/视觉不评分，责任转移至 UAT）；退化计数 ≥ 3 → blocked 交用户决策。
7. **全中文提示词**：面向中文用户编写；工具名、命令、文件名保留英文原文。

## 修改必须跨文件同步

同一概念散布在多个文件中，改动一处必须按下表同步全部相关位置：

| 概念 | README.md | SKILL.md | dev-prompt.md | qa-prompt.md | references/e2e/ |
| ---- | --------- | -------- | ------------- | ------------ | ---------------- |
| 工作流阶段与流转 | 流程图 | 工作流总览、阶段一~七 | 收到任务时 | 验收流程 | — |
| 迭代提交门禁 | 特性说明 | 阶段五、非侵入与 Git 约定 | 执行开发第 4 条、被打回时第 4 条 | 核心禁令第 7 条 | — |
| 用户代码评审 | 特性说明 | 阶段一/四/五/六、state.md 格式、中断恢复 | 调度方式 | 调度方式、验收流程第 1 条 | — |
| 概要设计（hld.md） | 特性说明 | 阶段三/四/五 | 收到任务第 2 条、概要设计格式 | 调度方式、验收流程第 5 条 | — |
| E2E 工具与场景路由 | E2E 依赖 | 跨工具适配、E2E 工具上报流程、阶段五第 3 条 | — | E2E 执行原则、核心禁令第 1/2/6 条 | index.md 路由表（事实源）、各工具文件 |
| 退化计数 | — | 退化计数规则、阶段五第 4 条 | — | 退化计数规则、QA 报告格式 | — |
| state.md 字段与阶段取值 | — | 状态文件格式、各阶段更新处、中断恢复 | — | — | — |
| 数据目录结构 | 使用说明 | 目录结构（数据侧）、目录结构 | — | 截图落盘约定 | index.md 共通规范的证据落盘条目 |

注：README.md 的「目录结构」列的是**本仓库文件**，SKILL.md 的「目录结构」列的是**数据目录产物**，两者不是同一份清单。

## 提示词编写规范

- SKILL.md frontmatter：`name` 固定为 `waibao`；`description` 必须同时写清触发场景与排除场景（如「小型代码修改或单点提问不要使用」）。
- 跨工具适配以「能力」而非工具名描述依赖；具体框架的工具映射只落在 SKILL.md「跨工具适配」表，不写进角色提示词。E2E 被测工具（playwright-cli / WDIO MCP）的命令与参数细节统一维护在 `references/e2e/`，角色提示词只保留场景路由表与执行原则。
- 规则、评分、映射关系用表格呈现；产物文档（PRD、QA 报告、state.md 等）用 markdown 代码块给出完整模板。
- 角色提示词保持既有骨架：角色与性格 → 调度方式 → 核心禁令 → 流程 → 格式模板 → 被打回时。新增内容归入既有章节，不另起平行体系。
- 禁令写成可判定的硬规则（「不得」「必须」），不用建议性语气。

## 验证

仓库无 lint / 测试 / 构建。修改后自查：

1. SKILL.md frontmatter 可解析（name / description 完整）。
2. 各 markdown 代码块闭合，表格列数与表头一致。
3. 按「修改必须跨文件同步」表逐行 grep 术语：改动后的旧术语在全仓库应零命中（如改了阶段名，搜旧名确认无残留）。
4. 改动不会自动生效：已安装副本位于 `~/.agents/skills/waibao`（全局）或项目内 `.agents/skills/waibao`，完成后提醒用户重新 `npx skills add yescco/waibao` 更新。

## Git

- 不主动 commit / push，除非用户明确要求。
- commit message 遵循约定式提交（Conventional Commits），格式 `<type>(<scope>): <subject>`，subject 用中文短句、祈使语气、结尾不加句号。
  - `type` 取 `feat`（新增/变更工作流能力）、`fix`（修复工作流缺陷）、`docs`（纯文字润色与结构整理）、`refactor`、`chore`。
  - `scope` 可选，取受影响的文件或模块：`skill`、`dev-prompt`、`qa-prompt`、`e2e`、`readme`、`agents`；跨多文件时省略。
  - 破坏性变更在 type 后加 `!`（如 `feat!:`），并在 body 写明影响与迁移方式。
  - 示例：`feat(skill): 新增概要设计环节，PRD 确认后先产出 hld.md 再规划迭代`；`fix(qa-prompt): E2E 必须显式 headless: false，禁止无头模式`。
  - 历史提交中 `dev-prompt:` / `qa-prompt:` 开头的旧风格为遗留格式，不回溯改写，仅约束新提交。
