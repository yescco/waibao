# WDIO MCP 使用说明

适用场景：**移动端（iOS/Android 真机或模拟器，经 Appium）**——唯一支持真机/模拟器 E2E 的工具。浏览器驱动与 Appium 环境由其管理，前提是目标浏览器/设备已就绪。场景路由见 `e2e/index.md`。

## 工具表

| 操作 | 工具 | 说明 |
| ---- | ---- | ---- |
| 启动会话 | `start_session` | 启动移动端（或浏览器）会话；浏览器会话必须显式传 `headless: false` |
| 页面导航 | `navigate` | 在当前标签页加载 URL |
| 获取可交互元素 | `get_elements` | 返回元素及其 CSS 选择器 / ID / 类名 / ARIA 信息 |
| 获取可访问性树 | `get_accessibility_tree` | 按角色过滤的结构化页面树 |
| 点击元素 | `click_element` | 按选择器点击 |
| 输入文本 | `set_value` | 向输入框输入文本 |
| 滚动 | `scroll` | 按像素上下滚动 |
| 截图 | `get_screenshot` | 截取当前页面（返回图像数据） |
| 执行脚本 | `execute_script` | 在页面内执行 JavaScript |
| 关闭会话 | `close_session` | 关闭或脱离当前会话 |

移动端另有 `tap_element`、`swipe`、`switch_context` 等专用工具，需要测移动端时按平台要求准备 Appium 环境。

## 元素定位

通过 `get_elements` 或 `get_accessibility_tree` 获取真实选择器后再操作，不要凭空猜测元素。

## 有头模式

`start_session` 的 `headless` 参数默认为 `true`（无头），浏览器会话必须显式传 `headless: false`（移动端会话不涉及该参数）。

## 可用性检测

开始 E2E 前检查当前环境是否已配置 WDIO MCP（即可用工具列表中是否存在 `start_session`、`navigate` 等会话/导航类工具），未配置则向 Pm 上报。

## 证据落盘

`get_screenshot` 返回图像数据，通过 shell 解码写入 `<迭代目录绝对路径>/evidence-N.png`。
