# playwright-cli 使用说明

适用场景：**PC Web 页面**（URL 可访问的页面，含移动端风格的 H5 页面）与 **Electron 桌面应用**（CDP 附加）。场景路由见 `e2e/index.md`。

## 场景要点

### PC Web（含 H5）

- 第一步用 `playwright-cli open <url>` 建立会话并导航。目标 URL 从项目配置/AGENTS.md 获取，或请 Pm 向用户确认。
- 测 H5 页面可用 `open --device="iPhone 15"` 模拟移动视口，但这只是视口模拟，**不得以此替代真机移动端 E2E**。

### Electron（CDP 附加）

- 被测应用由 **Pm 负责以调试端口启动与关闭**（Qa 受 Bash 安全原则约束不得自行启动，需要时向 Pm 上报）。
- Pm 启动并确认端口可访问后，用 `attach --cdp=http://localhost:<port>` 连接到**应用进程本身**，之后各命令照常使用。
- **禁止以 `open` 打开渲染页面 / dev server URL 冒充 Electron E2E**——浏览器上下文没有主进程、IPC、Node 集成与原生窗口行为，结论不能代表应用真实表现。

## 常用命令

命令不存在时以 `npx playwright cli <子命令>` 兜底。每条命令执行后自动附带最新页面状态与 snapshot（YAML 落盘，输出中仅给出文件链接，不内联全文）：

| 操作 | 命令 | 说明 |
| ---- | ---- | ---- |
| 打开页面 | `playwright-cli open <url>` | 建立会话并导航；默认有头模式；`--browser=chrome/firefox` 选浏览器，`--device` 模拟设备视口 |
| 附加 Electron | `playwright-cli attach --cdp=http://localhost:9222` | 连接以调试端口启动的 Electron 应用 |
| 页面快照 | `snapshot` / `snapshot e34` / `snapshot --depth=4` | 大页面用元素 ref 局部快照或限制深度，省 token |
| 定向查找 | `find "登录"` | 在 snapshot 内按文本/正则搜索匹配节点 |
| 点击 | `click e15` | 按快照 ref 点击（也支持 CSS 选择器 / Playwright locator） |
| 输入 | `fill e5 "文本" --submit` | 填写输入框，`--submit` 顺带回车提交 |
| 按键 | `press Enter` | 键盘操作（Enter / Tab / 方向键等） |
| 滚动 | `mousewheel`（或 `press PageDown`） | 滚轮/按键滚动页面 |
| 截图 | `screenshot --filename <路径>` | 直接写入文件，证据落盘首选 |
| 查看控制台 | `console` | 按级别过滤，排查前端报错 |
| 执行脚本 | `eval "js"` | 在页面内求值 |
| 关闭会话 | `close` | 关闭浏览器会话 |

## 元素定位

按命令返回的 snapshot 取元素 ref（如 `e15`），大页面用 `find "文本"` 定向查找，不要凭空猜测元素。

## 有头模式

playwright-cli 默认即有头启动，保持默认即可，**禁止改以无头方式运行**。

## 可用性检测

开始 E2E 前执行 `playwright-cli --version`（失败再试 `npx playwright cli --version`）确认可用。Electron 场景另需确认被测应用已以调试端口运行，未运行则向 Pm 上报「需要以 `--remote-debugging-port=9222` 启动被测 Electron 应用」。

## 证据落盘

用 `screenshot --filename <迭代目录绝对路径>/evidence-N.png` 一步落盘。
