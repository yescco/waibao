# Appium Windows Driver 使用说明

适用场景：**Avalonia 桌面应用（Windows）**（Avalonia 官方推荐的 E2E 通道，驱动真实应用窗口）。macOS / Linux 的 Avalonia 桌面应用不在路由表内。场景路由见 `e2e/index.md`。

## 工作方式

Appium（appium-windows-driver，Windows 桌面应用驱动，经 Appium server 挂载）以**交互式会话**方式执行 E2E：创建会话（启动或附加被测应用）→ 获取无障碍树 → 操作 → 截图 → 关闭会话。测试动作经 W3C WebDriver 协议逐条命令完成——环境提供 Appium / WebDriver 类 MCP 工具时优先使用，否则经 HTTP 命令行通道直接调用。命令、capabilities 与参数以 appium-windows-driver 官方文档为准，本文件不固化任何命令与参数。

## 用法事实源

- appium-windows-driver 官方文档：`https://github.com/appium/appium-windows-driver`
- Avalonia 官方指南 UI Testing with Appium：`https://docs.avaloniaui.net/docs/testing/ui-testing-with-appium`（元素定位依据：Avalonia 将 `AutomationProperties.AutomationId`（或控件 Name）暴露给平台无障碍 API）

「先查文档、禁止凭记忆调用」规则见 `e2e/index.md` 共通规范。

## 场景要点

- 被测应用为已构建的 Windows 可执行文件，构建产物路径由 Pm 在任务上下文提供；未提供且无法从项目构建产物确定时向 Pm 上报。构建（短生命周期命令）可由 Qa 执行；应用启动由 Appium 会话承担（服务端启动）或附加已运行窗口，Qa 不直接执行应用启动命令。
- Appium server 由用户启动并保持运行，Qa 不启动常驻进程。
- Avalonia headless 平台测试与浏览器页面不是桌面应用上下文，禁止冒充。

## 操作环节

一次 E2E 按以下环节执行，全部操作在同一次会话内完成：

1. **创建会话**：向 Appium server 创建 Windows 桌面会话——启动被测应用（指定构建产物路径）或附加已运行窗口，方式与 capabilities 以官方文档为准。
2. **获取无障碍树**：获取当前窗口的结构化信息（可交互元素、无障碍树等），据此定位**真实元素**，不得凭空猜测元素。
3. **执行操作**：模拟真实用户操作（点击、输入、滚动），走完本迭代的核心用户路径，记录所有异常行为和 bug。
4. **截图落盘**：关键步骤截图并保存为 `<迭代目录绝对路径>/evidence-N.png`，至少 1 张。
5. **关闭会话**：E2E 结束后关闭会话；由会话启动的被测应用随会话退出，不留残留进程。

## 可用性检测

开始 E2E 前确认以下各项，任一不满足则向 Pm 上报：

- Appium server 已在当前环境运行且挂载 windows driver（可探测其会话端点，具体方式以官方文档为准）。
- 被测应用构建产物路径已由 Pm 提供，或可经项目构建命令产出。

## 证据落盘

截图保存为 `<迭代目录绝对路径>/evidence-N.png`，至少 1 张，报告中列出文件名；WebDriver 截图仅返回图像数据（base64 编码）时将其解码写入文件。
