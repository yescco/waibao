# WDIO（WebDriverIO）使用说明

适用场景：**PC Web 页面**（URL 可访问的页面，含移动端风格的 H5 页面）、**Electron 桌面应用**、**Tauri 桌面应用**。场景路由见 `e2e/index.md`。

## 工作方式

WDIO 是代码化测试框架：编写配置与 spec 后一次性运行。所有测试文件放在迭代目录下的 `e2e/` 工作区，项目目录零侵入：

```
<迭代目录绝对路径>/e2e/
  package.json
  wdio.conf.ts
  specs/*.ts
```

执行顺序：初始化工作区 → 安装依赖 → 编写 spec → `npx wdio run wdio.conf.ts` → 从输出读取结果与截图证据。runner 结束后自动回收其拉起的浏览器与应用进程，不留残留。

## 场景要点

### PC Web（含 H5）

- `capabilities` 配真实浏览器（如 `browserName: 'chrome'`，Chromedriver / Geckodriver 等驱动自动下载启动），`baseUrl` 指向目标 URL。目标 URL 从项目配置 / AGENTS.md 获取，或请 Pm 向用户确认。
- dev server 未启动时**不得自行启动**（Bash 安全原则），在报告中注明并上报 Pm。
- 测 H5 页面可在 capabilities 中设置移动视口 / 设备模拟，但这只是视口模拟，**不得以此替代真机移动端 E2E**。

### Electron

- `services` 配置 `['electron', { appEntryPoint: '<应用主进程入口绝对路径>', appArgs: [/** 启动参数 */] }]`，由 electron service 直接拉起**真实应用进程**（Chromedriver 自动匹配与管理）。
- `appEntryPoint` 依据 Pm 提供的应用入口信息（如项目 `package.json` 的 `main`）解析为绝对路径。
- **禁止改为浏览器打开渲染页面 / dev server URL 冒充 Electron E2E**——浏览器上下文没有主进程、IPC、Node 集成与原生窗口行为，结论不能代表应用真实表现。

### Tauri

- `services` 配置 `['tauri', { appBinaryPath: '<已编译应用二进制绝对路径>' }]`，由 tauri service 拉起**真实应用**（Windows WebView2 / macOS WKWebView / Linux WebKitGTK）。
- 应用二进制由 Pm 确认存在后提供路径；路径无产物时向 Pm 上报，**不得自行执行构建命令**（构建属长时命令，由 Pm 委托 Dev 或用户执行）。
- **禁止改为浏览器打开前端 dev URL 冒充 Tauri E2E**——系统 WebView 与桌面浏览器渲染环境不同，结论不能代表应用真实表现。

## 依赖安装

在工作区内执行（短生命周期命令，可直接运行）：

| 场景 | 命令 |
| ---- | ---- |
| 基础（三场景通用） | `npm install --save-dev @wdio/cli @wdio/local-runner @wdio/mocha-framework @wdio/spec-reporter tsx` |
| Electron 追加 | `npm install --save-dev @wdio/electron-service` |
| Tauri 追加 | `npm install --save-dev @wdio/tauri-service` |

## 常用 API

spec 内通过全局 `browser` 对象使用（mocha framework）：

| 操作 | API | 说明 |
| ---- | --- | ---- |
| 打开页面 | `await browser.url('<url>')` | PC Web 导航；Electron/Tauri 由 service 拉起应用后直接操作应用窗口页面 |
| 定位元素 | `await $('<选择器>')` / `await $$('<选择器>')` | 支持 CSS、文本（`=<文本>`）、ARIA、shadow DOM 穿透 |
| 点击 | `await elem.click()` | 自动等待元素可交互 |
| 输入 | `await elem.setValue('<文本>')` | 输入框赋值 |
| 按键 | `await browser.keys(['Enter'])` | 键盘操作 |
| 滚动 | `await elem.scrollIntoView()` | 滚动至元素可见 |
| 截图 | `await browser.saveScreenshot('<路径>')` | 整页截图，直接写入文件，证据落盘首选 |
| 执行脚本 | `await browser.execute(() => { /* ... */ })` | 页面内执行 JavaScript |
| 断言 | `await expect(elem).toBeExisting()` / `toHaveText('<文本>')` | 内置匹配器，断言失败即测试失败 |

## 元素定位

选择器必须对应页面真实元素（CSS / 文本 / ARIA），WDIO 自动等待元素出现与可交互，仍不得凭空猜测元素。

## 有头模式

`capabilities` 中不设置 `headless` 即为有头运行（默认），**禁止在浏览器启动参数（如 `goog:chromeOptions.args`）中加入 `headless`**。

## 可用性检测

开始 E2E 前确认以下各项，任一不满足则向 Pm 上报：

- `node --version` 与 `npm --version` 可用，且 `npx wdio --version` 可执行（依赖已安装）。
- Electron 场景：Pm 提供的应用入口路径存在。
- Tauri 场景：Pm 提供的应用二进制存在。

## 证据落盘

spec 中在关键步骤后调用 `await browser.saveScreenshot('<迭代目录绝对路径>/evidence-N.png')`，至少 1 张，报告中列出文件名。
