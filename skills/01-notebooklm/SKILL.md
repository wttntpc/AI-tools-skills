---
name: ai-tools-notebooklm
description: 連接 NotebookLM MCP — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 NotebookLM」「設定 NotebookLM」時載入。
---

# 連接 NotebookLM（通用版）v2.0

> ⚠️ **重要提醒**：NotebookLM 無官方 API，登入方式為 **Chrome 瀏覽器自動化（Playwright）**，需要電腦已安裝 Chrome，且登入後 token 可能不定期失效需重新授權。

## 前置需求（AI 先確認）

```bash
# 確認 uv 已安裝
uv --version

# 確認 Chrome 已安裝（Windows）
# Chrome 預設路徑：C:\Program Files\Google\Chrome\Application\chrome.exe
```

若 `uv` 未安裝，AI 先執行：
```powershell
winget install --id=astral-sh.uv -e
```
安裝後重開終端機再繼續。

---

## 步驟

### 1. AI 直接執行安裝

```bash
uv tool install notebooklm-mcp-cli
nlm --version
```

若 `nlm` 找不到（Windows PATH 未更新）：
```powershell
$env:PATH = "$env:USERPROFILE\.local\bin;$env:PATH"
nlm --version
```

### 2. 安裝 Playwright 瀏覽器（Windows 必要步驟）

`notebooklm-mcp-cli` 使用 Playwright 自動化 Chrome 完成 Google OAuth。Windows 上需手動下載瀏覽器：

```bash
playwright install chromium
```

若 `playwright` 找不到：
```bash
uv tool run playwright install chromium
```

### 3. 瀏覽器 OAuth（使用者操作）

AI 執行後告知使用者：「瀏覽器會自動開啟，請選擇正確的 Google 帳號完成授權，完成後告訴我。」

```bash
nlm login
```

授權完成後 AI 執行驗證：
```bash
nlm doctor
```

**常見問題：**

| 錯誤 | 處理方式 |
|------|---------|
| CP950 編碼錯誤 | `$env:PYTHONIOENCODING = "utf-8"` 再重跑 |
| 登入到錯誤帳號 | `nlm logout` → `nlm login` |
| 瀏覽器未開啟 | 確認 Chrome 已安裝；或改用 `nlm login --browser firefox` |
| Playwright 找不到 Chrome | `playwright install chromium` |
| Token 失效（MCP 無回應）| `nlm login` 重新授權 |

### 4. AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add notebooklm -- nlm mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": "nlm",
      "args": ["mcp"],
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  notebooklm:
    command: nlm
    args: ["mcp"]
    enabled: true
```

### 5. 驗證

AI 告知使用者：「請重啟 Agent，完成後告訴我。」重啟後 AI 列出 NotebookLM 筆記本確認連線成功。

---

## Token 失效處理（不定期需重新登入）

NotebookLM 的登入狀態不穩定，若 AI 呼叫 NotebookLM 工具時回傳認證錯誤，請執行：

```bash
nlm login
```

重新完成 Google OAuth 後即可恢復。
