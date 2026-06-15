---
name: ai-tools-notebooklm
description: 連接 NotebookLM MCP — 適用 Claude Desktop、Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 NotebookLM」「設定 NotebookLM」時載入。
---

# 連接 NotebookLM（通用版）v2.2

> ⚠️ **重要提醒**：NotebookLM 無官方 API，登入方式為 Chrome 瀏覽器自動化，需要電腦已安裝 Chrome，且 token 可能不定期失效需重新授權。

---

## 常見四大坑洞（先了解再安裝）

| 坑洞 | 原因 | 本流程解法 |
|------|------|-----------|
| `nlm mcp` 指令不存在 | MCP server 執行檔叫 `notebooklm-mcp`，不是 `nlm mcp` | 改用 `uvx --from notebooklm-mcp-cli notebooklm-mcp` |
| 掛載前未授權 → Server disconnected | Claude 啟動 MCP 時程式發現無授權直接閃退 | 先完成 `nlm login` 授權，再執行 MCP 註冊 |
| Windows 需要先裝 Playwright | `nlm login` 依賴 Playwright 開啟瀏覽器 | 安裝後才能登入 |
| Claude 桌面版 ≠ Claude Code CLI | 桌面版無法用 `claude mcp add`，需手動編輯 JSON | 依使用者類型給對應設定 |

---

## 步驟

### 1. AI 確認 uv 已安裝

```bash
uv --version
```

若未安裝：

**Windows：**
```powershell
winget install --id=astral-sh.uv -e
```
安裝後 AI 告知使用者：「請重開終端機，完成後告訴我。」

**macOS：**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

### 2. 安裝 notebooklm-mcp-cli

```bash
uv tool install notebooklm-mcp-cli
```

---

### 3. Windows 必要步驟：安裝 Playwright 瀏覽器

> macOS 可跳過此步驟

```bash
uv tool run playwright install chromium
```

若 `playwright` 找不到：
```bash
uvx --from playwright playwright install chromium
```

---

### 4. 先完成授權（必須在 MCP 掛載前完成）

AI 優先使用 `--channel chrome`，讓系統已登入的 Chrome 完成授權，免輸入帳密：

```bash
uvx --from notebooklm-mcp-cli nlm login --channel chrome
```

AI 告知使用者：「系統 Chrome 會開啟 Google 授權頁面，請選擇正確的帳號完成授權，完成後告訴我。」

若 `--channel chrome` 失敗，改用預設方式（會開啟新的瀏覽器視窗）：

```bash
uvx --from notebooklm-mcp-cli nlm login
```

> ⚠️ 此方式開啟的是**無登入狀態的新視窗**，需在視窗內手動輸入 Google 帳號密碼。請注意切換到該視窗並在 300 秒內完成授權。

授權完成後 AI 執行驗證：
```bash
uvx --from notebooklm-mcp-cli nlm doctor
```

`doctor` 應顯示：
- `Cookies: present`
- `Account: 你的Gmail帳號`

**常見問題：**

| 錯誤 | 處理方式 |
|------|---------|
| CP950 編碼錯誤（Windows） | `$env:PYTHONIOENCODING = "utf-8"` 再重跑 |
| 登入到錯誤帳號 | `uvx --from notebooklm-mcp-cli nlm logout` → 重新 login |
| 瀏覽器未開啟 | 確認已執行步驟 3 安裝 Playwright；確認系統已安裝 Chrome |
| doctor 顯示未授權 | 重新執行 login，確認授權完成後再試 |
| Login timeout（300s 超時） | 重新執行 login，**立即切換到彈出的瀏覽器視窗**完成授權 |

---

### 5. AI 依 Agent 類型執行 MCP 註冊

AI 先確認使用者用哪種 Claude：「請問你使用的是 Claude 桌面版 App，還是 Claude Code CLI？」

#### Claude 桌面版 App（最常見）

編輯 `%APPDATA%\Claude\claude_desktop_config.json`（Windows）或 `~/Library/Application Support/Claude/claude_desktop_config.json`（macOS）：

```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "uvx",
      "args": [
        "--from",
        "notebooklm-mcp-cli",
        "notebooklm-mcp"
      ]
    }
  }
}
```

> ⚠️ 注意：`args` 最後是 `"notebooklm-mcp"`，**不是** `"nlm"` 或 `"nlm mcp"`

#### Claude Code CLI

```bash
claude mcp add notebooklm -- uvx --from notebooklm-mcp-cli notebooklm-mcp
```

#### AntiGravity / Codex / OpenCode（opencode.json）

```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": "uvx",
      "args": ["--from", "notebooklm-mcp-cli", "notebooklm-mcp"],
      "enabled": true
    }
  }
}
```

#### Hermes Agent (~/.hermes/config.yaml)

```yaml
mcp_servers:
  notebooklm:
    command: uvx
    args: ["--from", "notebooklm-mcp-cli", "notebooklm-mcp"]
    enabled: true
```

---

### 6. 冷啟動 Claude

AI 告知使用者：「請完全關閉 Claude（工作列右鍵 → Quit），再重新開啟，完成後告訴我。」

重啟後 AI 列出 NotebookLM 筆記本確認連線成功：
```
列出我的 NotebookLM 筆記本
```

---

## Token 失效處理（不定期需重新登入）

若 AI 呼叫 NotebookLM 工具時回傳認證錯誤，不需要重新安裝或修改設定，只要重新登入即可：

```bash
uvx --from notebooklm-mcp-cli nlm login --channel chrome
```

重新完成 Google OAuth 後即可恢復。
