# waibao（外包模式）

一个 **Pm / Dev / Qa 三角色协作**的全流程项目管理 skill：把「老板提需求 → 外包团队交付 → 验收付款」的真实协作搬进 agent 体系。

```
需求探讨 → PRD → 概要设计 → 迭代规划 → 迭代循环（Dev 开发 + Qa 四维验收 + 用户代码评审(可选) + Dev 提交） → UAT 用户验收 → 完成
```

## 角色

| 角色 | 定位 | 运行方式 |
| ---- | ---- | -------- |
| **Pm**（项目管理者） | 入口与总调度：需求探讨、PRD、概要设计与迭代规划调度、状态管理 | 主代理亲自扮演，不碰代码 |
| **Dev**（开发者） | 唯一允许写代码、git commit 的角色 | 子代理 |
| **Qa**（测试者） | 四维度验收、E2E 测试、QA 报告 | 子代理 |

## 安装

```bash
# 全局安装（所有项目可用）→ ~/.agents/skills/waibao/
npx skills add yescco/waibao -g

# 或仅当前项目 → .agents/skills/waibao/
npx skills add yescco/waibao
```

Windows 上若符号链接创建失败，追加 `--copy`。不经过 CLI 也可以直接 clone 到 `~/.agents/skills/waibao`。

## 使用

在 ZCode 等支持的 agent 中触发 `/waibao`，或用自然语言：

> 帮我做一个 XX 功能 / 启动 waibao / 外包模式开发 XX

所有工作流产物（PRD、概要设计、状态文件、QA 报告、E2E 截图）都存放在 `~/.agents/waibao/projects/<项目名>/` 下，项目目录零侵入，中断后重新触发即可续接。

PRD 确认后，Dev 会先产出**概要设计**（`hld.md`：回答系统/模块/组件/关键类「怎么分、怎么连」，以 mermaid 图表 + 分点文字表达），经你确认后才进入迭代规划——用于总览整个功能的设计，也是后续 Qa 代码质量审查与用户代码评审的对照基准。

任务启动时会询问是否启用**用户代码评审**：启用后每个迭代在 Qa 验收通过后、Dev 提交前需要你评审本次迭代的代码变更，评审通过后 Dev 才提交；评审打回的修复会重新走 Qa 验收。无论是否启用，迭代代码在通过 Qa 验收（及评审）前都不会提交，通过后由 Dev 一次性提交。

## E2E 依赖

Qa 的 E2E 统一经 [WDIO MCP server（`@wdio/mcp`）](https://webdriver.io/docs/mcp/) 以交互式会话执行：**PC Web**（含 H5）以真实浏览器会话访问目标 URL，**Electron** 拉起真实应用进程，**移动端**（iOS/Android 真机/模拟器，经 Appium）在真机/模拟器中测试。工具用法以 [webdriver.io 官方 llms.txt 文档](https://webdriver.io/llms.txt) 为事实源，提示词不固化任何 API 与命令细节。工具不可用时 Pm 会上报并询问你是否安装/配置；拒绝则按降级模式继续验收（责任转移至 UAT）。Tauri 桌面应用暂不支持 E2E，遇到时上报并由你决策。

工具路由表与执行规范在 `references/e2e/` 下独立维护，与 Qa 角色提示词解耦——扩展新测试平台只需新增一个工具说明文件并在 `references/e2e/index.md` 路由表加一行。

## 维护

仓库文件构成、文档路由与维护约定见 [AGENTS.md](./AGENTS.md)；操作细则在 `docs/` 下按操作前必读。
