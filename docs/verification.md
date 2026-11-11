# 修改后自查清单

任何改动宣告完成之前，逐项自查。仓库无 lint / 测试 / 构建。

1. SKILL.md frontmatter 可解析（name / description 完整）。
2. 各 markdown 代码块闭合，表格列数与表头一致。
3. 按 AGENTS.md「修改必须跨文件同步」表逐行 grep 术语：改动后的旧术语在全仓库应零命中（如改了阶段名，搜旧名确认无残留）。
4. 提醒用户：改动不会自动生效，已安装副本位于 `~/.agents/skills/waibao`（全局）或项目内 `.agents/skills/waibao`，需重新 `npx skills add yescco/waibao` 更新。
