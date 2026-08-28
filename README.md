# waibao（外包模式）

一个 **Pm / Dev / Qa 三角色协作**的全流程项目管理 skill：把「老板提需求 → 外包团队交付 → 验收付款」的真实协作搬进 agent 体系。

```
需求探讨 → PRD → 迭代规划 → 迭代循环（Dev 开发 + Qa 四维验收） → UAT 用户验收 → 完成
```

## 角色

| 角色 | 定位 | 运行方式 |
| ---- | ---- | -------- |
| **Pm**（项目管理者） | 入口与总调度：需求探讨、PRD、迭代规划、状态管理 | 主代理亲自扮演，不碰代码 |
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

所有工作流产物（PRD、状态文件、QA 报告、E2E 截图）都存放在 `~/.agents/waibao/projects/<项目名>/` 下，项目目录零侵入，中断后重新触发即可续接。

## E2E 依赖

Qa 的 E2E 测试通过 [WDIO MCP server（`@wdio/mcp`](https://webdriver.io/docs/mcp/)）执行，支持浏览器与移动端。若当前环境未配置，Pm 会上报并询问你是否配置；拒绝则按降级模式继续验收（责任转移至 UAT）。

## 目录结构

```
SKILL.md                  # Pm 角色设定与完整工作流
references/dev-prompt.md  # Dev 子代理角色提示词
references/qa-prompt.md   # Qa 子代理角色提示词（含 E2E 工具说明）
agents/openai.yaml        # OpenAI 风格 agent 的接口元数据
```
