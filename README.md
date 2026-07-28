# AI 工具連接懶人包（通用版）v2.2

適用於 **Claude Code、AntiGravity、Codex（OpenAI）、OpenCode、Hermes Agent** 等支援 MCP 的 AI Agent。

> ⚠️ **需要桌面版 / CLI**：此懶人包依賴 MCP，**不支援 claude.ai 網頁版**。
> 尚未安裝者請先至 https://claude.ai/download 下載 Claude Code 桌面版。

把這行貼給你的 AI Agent：

```
這是 AI 工具連接懶人包 https://github.com/wttntpc/AI-tools-skills
請讀取 AI 通用懶人包.md，依步驟引導我完成設定。
```

> **v2.0 重大更新**：不需要自己開終端機！所有指令由 AI 直接執行，你只需要提供 API Token 和完成瀏覽器 OAuth。

---

## 可以連接哪些工具？

| # | 工具 | 說明 |
|---|------|------|
| 01 | Gemini Notebook（原 NotebookLM） | Google 知識管理工具，透過 MCP 讓 AI 直接讀寫筆記本 |
| 02 | GitHub | 版本控制，透過 CLI 管理 repo、push、PR |
| 03 | Obsidian | 本地知識庫，透過 MCPVault 連接 |
| 04 | Notion | 筆記與資料庫，透過官方 MCP 讓 AI 讀寫頁面 |
| 05 | Workflow | 開工 / 收工 / 新專案初始化流程 |
| 06 | Firebase | 雲端資料庫 |
| 07 | Google Calendar | 讓 AI 讀取、建立、修改行事曆事件 |
| 08 | Gmail | 讓 AI 讀取、搜尋、起草郵件 |
| 09 | Google Drive | 讓 AI 讀取雲端 PDF、Docs、Sheets |
| 10 | Zotero | 讓 AI 直接查詢、引用你的文獻庫 |
| 11 | HackMD | 讓 AI 直接讀取、建立、編輯 HackMD 筆記與共筆 |

---

## 使用者只需要做這些

| 需要使用者操作 | 說明 |
|-------------|------|
| 提供 API Token / Key | 從網頁複製後貼給 AI，AI 自動存入環境變數 |
| 完成瀏覽器 OAuth | nlm login、gh auth login、Firebase login 等 |
| Google Cloud Console 設定 | Calendar / Gmail / Drive 需建立 OAuth 憑證 |

**其他全部由 AI 執行**：安裝套件、設定環境變數、註冊 MCP、驗證連線。

---

## 支援的 AI Agent

| Agent | 規則檔 | MCP 設定 |
|-------|--------|---------|
| Claude Code | `CLAUDE.md` | `claude mcp add` |
| AntiGravity | `ANTIGRAVITY.md` | `opencode.json` |
| Codex (OpenAI) | `AGENTS.md` | `opencode.json` |
| OpenCode | `OPENCODE.md` | `opencode.json` |
| Hermes Agent | `AGENTS.md` 或 `.hermes.md` | `~/.hermes/config.yaml` |

---

## 安全原則

- API token 貼給 AI 後，由 AI 直接存入系統環境變數，不寫進任何檔案或 repo
- 所有 Google 服務登入走瀏覽器 OAuth，不複製 cookie 或 token
- Google 服務只授予需要的最小 scope（如 drive.readonly）
- 不 commit 個人 Gemini Notebook（NotebookLM）清單、筆記本 ID、研究報告
- Token 洩漏：立即到對應平台撤銷，重新建立後貼給 AI 更新環境變數

---

## 更新紀錄

| 日期 | 版本 | 內容 |
|------|------|------|
| 2026-07-28 | v2.2 | NotebookLM skill v2.4：顯示名稱更新為 Gemini Notebook（原 NotebookLM，Google 官方更名），套件/指令/MCP key 維持原名以相容既有設定 |
| 2026-07-08 | v2.1 | NotebookLM skill v2.3：移除失效的 --channel 選項；新增 SessionStart Hook 自動重連機制 |
| 2026-06-12 | v2.1 | 明確標注需桌面版 / CLI；NotebookLM 改用 --channel chrome；GitHub 改用 PAT 登入 |
| 2026-06-11 | v2.0 | 重大改版：所有終端機指令改由 AI 直接執行，使用者只需提供 token 和完成瀏覽器 OAuth |
| 2026-06-10 | v1.5 | 新增 HackMD（11） |
| 2026-06-08 | v1.4 | 新增 Hermes Agent 支援 |
| 2026-06-08 | v1.3 | 新增 Zotero（10），加強資安說明 |
| 2026-06-08 | v1.2 | 新增 Google Calendar、Gmail、Google Drive（07-09） |
| 2026-06-08 | v1.1 | 新增 Notion MCP 連接 |
| 2026-06-08 | v1.0 | 初版 |
