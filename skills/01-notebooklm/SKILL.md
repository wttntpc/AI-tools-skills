---
name: ai-tools-notebooklm
description: 連接 NotebookLM MCP — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 NotebookLM」「設定 NotebookLM」時載入。
---

# 連接 NotebookLM（通用版）v2.1

> ⚠️ **重要提醒**：NotebookLM 無官方 API，登入方式為 Chrome 瀏覽器自動化，需要電腦已安裝 Chrome，且 token 可能不定期失效需重新授權。

---

## 常見三大坑洞（先了解再安裝）

| 坑洞 | 原因 | 本流程解法 |
|------|------|-----------|
| 絕對路徑失效 | 每台電腦使用者名稱不同，`.exe` 路徑不一樣 | 改用 `uvx --from` 動態呼叫，不寫死路徑 |
| 套件名稱 ≠ 執行檔名稱 | 套件叫 `notebooklm-mcp-cli`，執行檔叫 `nlm` | `--from notebooklm-mcp-cli nlm` 明確對應 |
| 掛載前未授權 → Server disconnected | Claude 啟動 MCP 時程式發現無授權直接閃退 | 先完成 `nlm login` 授權，再執行 MCP 註冊 |

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

### 2. 先完成授權（最重要，必須在 MCP 掛載前完成）

AI 告知使用者：「瀏覽器會自動開啟 Google 授權頁面，請選擇正確的帳號完成授權，完成後告訴我。」

```bash
uvx --from notebooklm-mcp-cli nlm login
```

> 若開啟的是無登入狀態的新視窗，請在視窗內手動輸入 Google 帳號密碼完成授權。

授權完成後 AI 執行驗證：
```bash
uvx --from notebooklm-mcp-cli nlm doctor
```

**常見問題：**

| 錯誤 | 處理方式 |
|------|---------|
| CP950 編碼錯誤（Windows） | `$env:PYTHONIOENCODING = "utf-8"` 再重跑 |
| 登入到錯誤帳號 | `uvx --from notebooklm-mcp-cli nlm logout` → 重新 login |
| 瀏覽器未開啟 | 確認系統已安裝 Chrome，再重新執行 login |
| doctor 顯示未授權 | 重新執行 login，確認授權完成後再試 |

### 3. AI 依 Agent 類型執行 MCP 註冊

**Claude Code（使用 uvx 動態呼叫，不寫死路徑）：**
```bash
claude mcp add notebooklm -- uvx --from notebooklm-mcp-cli nlm mcp
```

**Claude Desktop（claude_desktop_config.json）：**
```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "uvx",
      "args": [
        "--from",
        "notebooklm-mcp-cli",
        "nlm",
        "mcp"
      ]
    }
  }
}
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": "uvx",
      "args": ["--from", "notebooklm-mcp-cli", "nlm", "mcp"],
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  notebooklm:
    command: uvx
    args: ["--from", "notebooklm-mcp-cli", "nlm", "mcp"]
    enabled: true
```

### 4. 冷啟動 Claude

AI 告知使用者：「請完全關閉 Claude（工作列右鍵 → Quit），再重新開啟，完成後告訴我。」

重啟後 AI 列出 NotebookLM 筆記本確認連線成功：
```
列出我的 NotebookLM 筆記本
```

---

## Token 失效處理（不定期需重新登入）

若 AI 呼叫 NotebookLM 工具時回傳認證錯誤：

```bash
uvx --from notebooklm-mcp-cli nlm login --channel chrome
```

重新完成 Google OAuth 後即可恢復，不需要重新註冊 MCP。
