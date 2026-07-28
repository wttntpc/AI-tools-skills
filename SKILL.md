---
name: ai-tools-lazy-packs
description: 通用 AI 工具連接懶人包 — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「AI 工具懶人包」「安裝通用設定」時載入。
---

# 通用 AI 工具懶人包 — AI Agent 自動安裝入口

當使用者給你這個 repo 網址並說要安裝時，請依以下流程執行。

## 步驟零：確認使用版本（必須先問）

AI 請先問使用者：「你目前使用的是 **Claude Code 桌面版 / CLI** 還是 **claude.ai 網頁版**？」

- **桌面版 / CLI** → 繼續步驟一
- **網頁版** → 告知：「網頁版不支援 MCP 工具連接，請先至 https://claude.ai/download 安裝桌面版，完成後再繼續。」

## 步驟一：列出所有可用懶人包

所有 skill 皆為**選用**，使用者自由選擇。

| 編號 | Skill 名稱 | 說明 |
|------|-----------|------|
| 01 | `01-notebooklm` | 連接 Gemini Notebook（原 NotebookLM）MCP |
| 02 | `02-github` | 連接 GitHub CLI |
| 03 | `03-obsidian` | 連接 Obsidian MCP (MCPVault) |
| 04 | `04-notion` | 連接 Notion MCP |
| 05 | `05-workflow` | 開工 / 收工 / 新專案初始化 |
| 06 | `06-firebase` | 連接 Firebase MCP |
| 07 | `07-google-calendar` | 連接 Google Calendar |
| 08 | `08-gmail` | 連接 Gmail |
| 09 | `09-google-drive` | 連接 Google Drive |
| 10 | `10-zotero` | 連接 Zotero 文獻庫 |
| 11 | `11-hackmd` | 連接 HackMD 筆記平台 |

## 步驟二：詢問使用者要安裝哪些

列出上表，然後問：

> 「以上全部為選用，你要安裝哪些？請輸入編號（例如 01 02 05），或輸入『全部』。」

## 步驟三：依序安裝選取的 skill

對每個選取的 skill，讀取 `skills/<名稱>/SKILL.md` 內容並依步驟執行。
每完成一個回報進度（✅/⚠️/❌），有錯誤自動診斷並修正後繼續。
已安裝的工具自動跳過。

## 步驟四：回報總表

所有選取的 skill 完成後，列出安裝結果總表。
