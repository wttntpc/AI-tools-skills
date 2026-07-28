# 通用 AI 工具連接懶人包 v2.1

> 適用：Claude Code、AntiGravity、Codex（OpenAI）、OpenCode、Hermes Agent（**需桌面版 / CLI，不支援網頁版**）
> 更新日期：2026-07-28（v2.1）
> 語系偏好：繁體中文（Taiwan）

這份懶人包讓任何支援 MCP 或工具呼叫的 AI Agent，都能用同一份流程完成 Gemini Notebook（原 NotebookLM）、GitHub、Gemini、Obsidian、Notion、Firebase、Google Calendar、Gmail、Google Drive、Zotero、HackMD 的連接與開工收工工作流程設定。

**v2.0 重大更新**：使用者不需要自己開終端機。所有指令由 AI 直接執行。使用者只需要：
1. 提供 API Token / Key（從網頁複製後貼給 AI）
2. 完成瀏覽器 OAuth 授權（AI 會告知何時需要操作）

---

## Step -1：確認使用版本（AI 請先詢問）

AI 請先問使用者：「你目前使用的是 **Claude Code（桌面版 / CLI）** 還是 **claude.ai 網頁版**？」

| 版本 | 說明 | 是否支援 MCP |
|------|------|-------------|
| **Claude Code 桌面版 / CLI** | 安裝於電腦的應用程式或終端機指令 | ✅ 完整支援 |
| **claude.ai 網頁版** | 瀏覽器開啟 claude.ai | ❌ 不支援 MCP |

若使用者回答**網頁版**，AI 告知：「網頁版不支援 MCP 工具連接。請先安裝 Claude Code 桌面版或 CLI（https://claude.ai/download），安裝完成後重新開始。」

確認為桌面版 / CLI 後再繼續。

---

## Step 0：偵測 AI Agent 類型 + 環境檢查

### AI 請自動執行以下檢查

**Windows：**
```powershell
git --version
gh --version
uv --version
python --version
node --version
npm.cmd --version
```

**macOS / Linux：**
```bash
git --version
gh --version
uv --version
python3 --version
node --version
npm --version
```

### AI 依結果自動處理

- 若工具已安裝：繼續下一步
- 若缺少工具：AI 直接執行安裝指令（見下表），不需使用者手動操作

| 工具 | Windows（AI 執行） | macOS（AI 執行） |
|------|-------------------|-----------------|
| Git | `winget install Git.Git` | `xcode-select --install` |
| GitHub CLI | `winget install GitHub.cli` | `brew install gh` |
| uv | `powershell -c "irm https://astral.sh/uv/install.ps1 \| iex"` | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| Node.js | `winget install OpenJS.NodeJS` | `brew install node` |

### 偵測 Agent 類型

AI 請先自動判斷目前的執行環境，並告知使用者：

| Agent | 規則檔 | MCP 設定方式 |
|-------|--------|------------|
| Claude Code | `CLAUDE.md` | `claude mcp add` 指令 |
| AntiGravity | `ANTIGRAVITY.md` | `opencode.json` 或 UI |
| Codex (OpenAI) | `AGENTS.md` | `opencode.json` 或 CLI flag |
| OpenCode | `OPENCODE.md` 或 `CLAUDE.md` | `opencode.json` |
| Hermes Agent | `AGENTS.md`（或 `.hermes.md`） | `~/.hermes/config.yaml` |

> 如果無法自動偵測，請問使用者：「你現在用的是哪個 AI Agent？」

> 💡 **Hermes 規則檔載入順序**：`.hermes.md` → `AGENTS.md` → `CLAUDE.md` → `.cursorrules`（第一個找到的為準）

---

## Step 1：連接 Gemini Notebook（原 NotebookLM）

### AI 直接執行安裝

```powershell
uv tool install notebooklm-mcp-cli
nlm --version
```

### 使用者操作（唯一需要手動的部分）

AI 執行完安裝後，請告知使用者：

> 「請在終端機執行 `nlm login`，瀏覽器會自動開啟，請選擇正確的 Google 帳號完成授權。完成後告訴我。」

或由 AI 直接執行：
```bash
nlm login
```
> ⚠️ 此指令會開啟瀏覽器，使用者需在瀏覽器中點擊「允許」。AI 等待使用者回覆「完成」後再繼續。

```bash
nlm doctor
```

### AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add notebooklm -- nlm mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**

AI 直接寫入 `opencode.json`：
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

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  notebooklm:
    command: nlm
    args: ["mcp"]
    enabled: true
```

完成後 AI 請告知使用者：「請重啟 Agent，完成後告訴我。」重啟後 AI 列出 Gemini Notebook 筆記本確認連線成功。

---

## Step 2：連接 GitHub

### AI 先檢查登入狀態

```bash
gh auth status
```

- 若已登入：跳過此步驟
- 若未登入：AI 執行以下指令，並告知使用者「瀏覽器會開啟，請完成 GitHub 授權」：

```bash
gh auth login --web --git-protocol https
```

### AI 詢問使用者 git 設定

AI 問：「請提供你的 GitHub 名稱和 Email，我幫你設定。」

取得後 AI 執行：
```bash
git config --global user.name "使用者提供的名稱"
git config --global user.email "使用者提供的email"
```

驗證：
```bash
gh auth status
git config --global user.name
git config --global user.email
```

---

## Step 3：設定 Gemini 免費 API

### 使用者操作

AI 告知使用者：

> 「請前往 https://aistudio.google.com/apikey 取得免費 API Key（不需信用卡），複製後貼給我。」

### AI 取得 Key 後直接設定環境變數

**Windows：**
```powershell
setx GEMINI_API_KEY "使用者提供的Key"
```

**macOS：**
```bash
echo 'export GEMINI_API_KEY="使用者提供的Key"' >> ~/.zshrc
source ~/.zshrc
```

**Linux：**
```bash
echo 'export GEMINI_API_KEY="使用者提供的Key"' >> ~/.bashrc
source ~/.bashrc
```

### AI 執行驗證

**Windows：**
```powershell
$env:GEMINI_API_KEY = "使用者提供的Key"
Invoke-RestMethod -Uri "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=$env:GEMINI_API_KEY" `
  -Method Post -ContentType "application/json" `
  -Body '{"contents":[{"parts":[{"text":"1+1等於多少？"}]}]}'
```

---

## Step 4：連接 Obsidian（選用）

### AI 直接安裝

**Windows：**
```powershell
npm.cmd install -g @bitbonsai/mcpvault
where.exe mcpvault
```

**macOS / Linux：**
```bash
npm install -g @bitbonsai/mcpvault
which mcpvault
```

### AI 詢問 vault 路徑

AI 問：「請提供你的 Obsidian vault 路徑（例如 `C:\Users\你\Documents\Secondbrain`），我幫你設定。」

取得後 AI 執行：

**Windows：**
```powershell
setx OBSIDIAN_VAULT_PATH "使用者提供的路徑"
```

**macOS：**
```bash
echo 'export OBSIDIAN_VAULT_PATH="使用者提供的路徑"' >> ~/.zshrc
source ~/.zshrc
```

### AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env OBSIDIAN_VAULT_PATH="使用者提供的路徑" obsidian -- mcpvault "使用者提供的路徑"
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "obsidian": {
      "type": "local",
      "command": "mcpvault",
      "args": ["${OBSIDIAN_VAULT_PATH}"],
      "environment": {
        "OBSIDIAN_VAULT_PATH": "${OBSIDIAN_VAULT_PATH}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  obsidian:
    command: mcpvault
    args: ["${OBSIDIAN_VAULT_PATH}"]
    env:
      OBSIDIAN_VAULT_PATH: "${OBSIDIAN_VAULT_PATH}"
    enabled: true
```

---

## Step 5：連接 Notion（選用）

### 使用者操作（一次性）

AI 告知使用者：

> 「請前往 https://www.notion.so/profile/integrations → **New integration** → 填入名稱（例如 `AI-MCP`）→ 選擇工作區 → Submit → 複製 **Internal Integration Token**（`ntn_` 開頭），貼給我。」

### AI 取得 Token 後直接設定

**Windows：**
```powershell
setx NOTION_API_KEY "使用者提供的token"
```

**macOS：**
```bash
echo 'export NOTION_API_KEY="使用者提供的token"' >> ~/.zshrc
source ~/.zshrc
```

**Linux：**
```bash
echo 'export NOTION_API_KEY="使用者提供的token"' >> ~/.bashrc
source ~/.bashrc
```

### AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env NOTION_API_KEY="使用者提供的token" notion -- npx -y @notionhq/notion-mcp-server
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "notion": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "environment": {
        "NOTION_API_KEY": "${NOTION_API_KEY}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  notion:
    command: npx
    args: ["-y", "@notionhq/notion-mcp-server"]
    env:
      NOTION_API_KEY: "${NOTION_API_KEY}"
    enabled: true
```

完成後 AI 請告知：「請重啟 Agent，並在 Notion 頁面右上角 **Share** → 把 Integration 加入，AI 才能存取該頁面。」

---

## Step 6：連接 Firebase（選用）

### 使用者操作（唯一需要手動的部分）

AI 先執行安裝檢查：

**Windows：**
```powershell
npx.cmd -y firebase-tools@latest --version
```

**macOS / Linux：**
```bash
npx -y firebase-tools@latest --version
```

然後 AI 告知使用者：

> 「Firebase 需要在瀏覽器中登入，請告訴我當我執行登入指令時你準備好了。」

使用者回覆後 AI 執行：

**Windows：**
```powershell
npx.cmd -y firebase-tools@latest login
```

**macOS / Linux：**
```bash
npx -y firebase-tools@latest login
```

> ⚠️ 瀏覽器會自動開啟，請使用者完成 Google 授權後告知 AI。

### AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add firebase -- npx -y firebase-tools@latest mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "firebase": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "firebase-tools@latest", "mcp"],
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  firebase:
    command: npx
    args: ["-y", "firebase-tools@latest", "mcp"]
    enabled: true
```

---

## Step 7：連接 Google Calendar（選用）

> ⚠️ 資安：只授予 `calendar.readonly`，AI 無法刪除事件。

### 使用者操作（一次性，在 Google Cloud Console）

AI 引導使用者完成以下步驟：

1. 前往 https://console.cloud.google.com → 建立或選擇專案（例如 `AI-MCP`）
2. **APIs & Services → Library** → 啟用 `Google Calendar API`
3. **OAuth consent screen** → External → 填入 App name 和 Email → Scopes 只加 `calendar.readonly`
4. **Credentials → Create Credentials → OAuth client ID** → Desktop app → 下載 `client_secret_xxx.json`

> AI 問使用者：「請告訴我你把憑證檔案存在哪裡（完整路徑），我幫你完成後續設定。」

### AI 取得路徑後直接設定

**Windows：**
```powershell
setx GOOGLE_CALENDAR_CREDENTIALS "使用者提供的路徑"
```

**macOS：**
```bash
echo 'export GOOGLE_CALENDAR_CREDENTIALS="使用者提供的路徑"' >> ~/.zshrc
source ~/.zshrc
```

### AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env GOOGLE_CALENDAR_CREDENTIALS="使用者提供的路徑" google-calendar -- npx -y @modelcontextprotocol/server-google-calendar
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-calendar": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-google-calendar"],
      "environment": {
        "GOOGLE_CALENDAR_CREDENTIALS": "${GOOGLE_CALENDAR_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  google-calendar:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-google-calendar"]
    env:
      GOOGLE_CALENDAR_CREDENTIALS: "${GOOGLE_CALENDAR_CREDENTIALS}"
    enabled: true
```

AI 執行完後在 `.gitignore` 加入：`client_secret*.json`、`token*.json`

---

## Step 8：連接 Gmail（選用）

> ⚠️ 資安：AI 起草郵件後必須由使用者確認才能發送。

### 使用者操作（一次性，在 Google Cloud Console）

AI 引導使用者完成：

1. 前往 https://console.cloud.google.com → 同上專案
2. **APIs & Services → Library** → 啟用 `Gmail API`
3. **OAuth consent screen** → External → 填入 App name、Email、Test users（加入你的 Gmail）
4. **Credentials → Create Credentials → OAuth client ID** → Desktop app → 下載憑證

> AI 問使用者：「請告訴我憑證檔案的完整路徑。」

### AI 取得路徑後直接設定

**Windows：**
```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.gmail-mcp"
Copy-Item "使用者提供的路徑" "$env:USERPROFILE\.gmail-mcp\gcp-oauth.keys.json"
setx GOOGLE_GMAIL_CREDENTIALS "使用者提供的路徑"
```

**macOS / Linux：**
```bash
mkdir -p ~/.gmail-mcp
cp "使用者提供的路徑" ~/.gmail-mcp/gcp-oauth.keys.json
echo 'export GOOGLE_GMAIL_CREDENTIALS="使用者提供的路徑"' >> ~/.zshrc
source ~/.zshrc
```

### AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env GOOGLE_GMAIL_CREDENTIALS="使用者提供的路徑" gmail -- npx -y @gongrzhe/server-gmail-autoauth-mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "gmail": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@gongrzhe/server-gmail-autoauth-mcp"],
      "environment": {
        "GOOGLE_GMAIL_CREDENTIALS": "${GOOGLE_GMAIL_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  gmail:
    command: npx
    args: ["-y", "@gongrzhe/server-gmail-autoauth-mcp"]
    env:
      GOOGLE_GMAIL_CREDENTIALS: "${GOOGLE_GMAIL_CREDENTIALS}"
    enabled: true
```

---

## Step 9：連接 Google Drive（選用）

> ⚠️ 資安：只授予 `drive.readonly`，AI 只能讀取不能修改或刪除。

### 使用者操作（一次性）

AI 引導使用者：

1. 前往 https://console.cloud.google.com → 同上專案
2. **APIs & Services → Library** → 啟用 `Google Drive API`
3. **OAuth consent screen** → Scopes 只加 `drive.readonly`
4. **Credentials → Create Credentials → OAuth client ID** → Desktop app → 下載憑證

> AI 問使用者：「請告訴我憑證檔案的完整路徑。」

### AI 取得路徑後直接設定

**Windows：**
```powershell
setx GOOGLE_DRIVE_CREDENTIALS "使用者提供的路徑"
```

**macOS：**
```bash
echo 'export GOOGLE_DRIVE_CREDENTIALS="使用者提供的路徑"' >> ~/.zshrc
source ~/.zshrc
```

### AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env GOOGLE_DRIVE_CREDENTIALS="使用者提供的路徑" google-drive -- npx -y @modelcontextprotocol/server-gdrive
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-drive": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-gdrive"],
      "environment": {
        "GOOGLE_DRIVE_CREDENTIALS": "${GOOGLE_DRIVE_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  google-drive:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-gdrive"]
    env:
      GOOGLE_DRIVE_CREDENTIALS: "${GOOGLE_DRIVE_CREDENTIALS}"
    enabled: true
```

---

## Step 10：連接 Zotero（選用）

> 使用本機 API（localhost:23119），不需要 API Key，資料完全不離開本機。
> ⚠️ 已知問題：`zotero-mcp` npm 套件與 Zotero 7.x 不相容，改用直接 API 呼叫。

### 使用者操作（一次性）

AI 告知使用者：

> 「請開啟 Zotero → `Edit` → `Preferences` → **進階** → 勾選「允許此電腦上的其他應用程式與 Zotero 通訊」，完成後告訴我。」

### AI 執行驗證

```powershell
curl -s "http://localhost:23119/api/users/0/items?itemType=journalArticle&limit=3"
```

成功回傳論文清單即完成 ✅

---

## Step 11：連接 HackMD（選用）

> ⚠️ 資安：API token 只存入系統環境變數，不貼在對話中、不寫進 repo。

### 使用者操作（一次性）

AI 告知使用者：

> 「請前往 HackMD → 右上角頭像 → **Settings** → **API** → **Create API Token** → 名稱填 `AI-MCP`，勾選 ✅ Read + ✅ Write → 複製 token（只出現一次），貼給我。」

### AI 取得 Token 後直接設定

**Windows：**
```powershell
setx HACKMD_API_TOKEN "使用者提供的token"
```

**macOS：**
```bash
echo 'export HACKMD_API_TOKEN="使用者提供的token"' >> ~/.zshrc
source ~/.zshrc
```

**Linux：**
```bash
echo 'export HACKMD_API_TOKEN="使用者提供的token"' >> ~/.bashrc
source ~/.bashrc
```

### AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add -e HACKMD_API_TOKEN="使用者提供的token" hackmd -- npx -y hackmd-mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "hackmd": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "hackmd-mcp"],
      "environment": {
        "HACKMD_API_TOKEN": "${HACKMD_API_TOKEN}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  hackmd:
    command: npx  # Windows 請改為 npx.cmd
    args: ["-y", "hackmd-mcp"]
    env:
      HACKMD_API_TOKEN: "${HACKMD_API_TOKEN}"
    enabled: true
```

完成後 AI 請告知：「請重啟 Agent，完成後告訴我，我來驗證連線。」

---

## 附錄 A：建立 Agent 規則檔

AI 直接在專案根目錄建立對應規則檔（不需使用者手動建立）：

| Agent | 規則檔 | 備註 |
|-------|--------|------|
| Claude Code | `CLAUDE.md` | |
| AntiGravity | `ANTIGRAVITY.md` | |
| Codex | `AGENTS.md` | |
| OpenCode | `OPENCODE.md` | |
| Hermes Agent | `AGENTS.md` 或 `.hermes.md` | 優先讀取 `.hermes.md` |

### 通用規則檔範本

AI 根據使用者已安裝的工具，自動填入對應狀態：

```markdown
# <專案名稱> - AI 工作規則

## 專案資訊
專案名稱：
用途：
工作目錄：
GitHub repo：

## 連接的工具
- Gemini Notebook（NotebookLM）：已連接 / 未設定
- GitHub：已連接 / 未設定
- Gemini API：已設定 / 未設定
- Notion：已連接 / 未使用
- Firebase：已連接 / 未使用
- Obsidian vault：已連接 / 未使用
- Google Calendar：已連接 / 未使用
- Gmail：已連接 / 未使用
- Google Drive：已連接 / 未使用
- Zotero：已連接 / 未使用
- HackMD：已連接 / 未使用

## 工作規則
- 回應使用繁體中文
- 涉及檔案操作時回報完整路徑
- Windows 環境使用 PowerShell 語法
- 開工時讀本檔並回報目前 Git 狀態
- 收工時更新 Obsidian 駕駛艙、檢查 diff、只提交相關檔案

## 禁止事項
- 不 commit API key、token、密碼
- 不 commit Gemini Notebook（NotebookLM）個人清單或筆記本 ID
- 不使用無差別 git add .
- Gmail：發信前必須顯示完整草稿，等待使用者確認才發送
```

---

## 附錄 B：開工 / 收工 / 新專案初始化

### 開工（對任何 Agent 說「開工」）

AI 應執行：
1. 讀取對應規則檔（CLAUDE.md / ANTIGRAVITY.md / AGENTS.md / .hermes.md）
2. 執行 `git status` 確認目前狀態
3. 讀取最近 3 筆 commit：`git log --oneline -3`
4. 回報狀態與建議下一步

### 收工（對任何 Agent 說「收工」）

AI 應執行：
1. 確認無敏感資料（API key、token、個人清單）
2. 更新 Obsidian 專案駕駛艙（完成項目、下一步、踩坑）
3. 執行 `git status` 與 `git diff`
4. 只 stage 本次相關檔案
5. 產生 commit message 並確認後 push

---

## 完成回報格式

AI 完成安裝後請用以下格式回報：

```
## 通用懶人包設定完成

Agent 類型：Claude Code / AntiGravity / Codex / OpenCode / Hermes Agent
作業系統：Windows / macOS / Linux

- Gemini Notebook（NotebookLM）MCP：✅ 已連接 / ⚠️ 待 OAuth / ❌ 失敗
- GitHub CLI：✅ 已登入 (帳號: xxx) / ❌ 未登入
- Gemini API：✅ 已設定 / ⚠️ 待取得 Key / ❌ 未設定
- Notion MCP：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Firebase MCP：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Obsidian MCP：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Google Calendar：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Gmail：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Google Drive：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Zotero：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- HackMD：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- 規則檔：✅ 已建立 (CLAUDE.md) / ⚠️ 待建立

下一步：
```

---

## 常見問題

| 問題 | 解法 |
|------|------|
| Gemini Notebook（NotebookLM）登入到錯帳號 | AI 執行 `nlm logout`，請使用者重新完成瀏覽器 OAuth |
| `nlm doctor` 未認證 | 重跑 OAuth，不要手動貼 cookie |
| Windows 顯示 CP950 編碼錯誤 | AI 執行 `$env:PYTHONIOENCODING = "utf-8"` |
| GitHub CLI 找不到 gh 指令 | AI 重新執行安裝後重啟 |
| Firebase login 在 AI 對話卡住 | AI 告知使用者：「請告訴我你準備好了，我再執行登入指令」 |
| PowerShell 擋 npx.ps1 | AI 改用 `npx.cmd` |
| MCP 設定後 Agent 沒看到工具 | 重啟 Agent |
| Notion 連線成功但讀不到頁面 | 在 Notion 頁面右上角 Share → 把 Integration 加入 |
| Notion token 格式錯誤 | 確認開頭為 `ntn_`，不要包含引號 |
| Obsidian vault 路徑找不到 | AI 執行 `find ~ -name ".obsidian" -type d` 搜尋 |
| Gmail OAuth access_denied | 到 Google Cloud Console → OAuth consent screen → 加入 Gmail 為測試使用者 |
| Gmail MCP 連線失敗 | AI 確認憑證是否已複製到 `~/.gmail-mcp/gcp-oauth.keys.json` |
| Zotero 連線失敗 | 確認 Zotero 桌面程式已開啟，且已勾選本機 API 選項 |
| Hermes MCP 工具沒出現 | 確認 `~/.hermes/config.yaml` 格式正確（YAML 縮排敏感），重啟 Hermes |
| Hermes 規則檔沒被讀取 | 優先建立 `.hermes.md`，或確認 `AGENTS.md` 在專案根目錄 |
| HackMD MCP 連線失敗 | AI 確認 `HACKMD_API_TOKEN` 環境變數已設定，告知使用者重開終端機後再試 |
| HackMD token 無效 | 到 HackMD Settings → API 重新產生 token，勾選 Read + Write |
| HackMD token 洩漏 | 立即到 HackMD Settings → API 撤銷舊 token，AI 重新執行設定 |

---

## 資安維護（每季執行）

### Google 服務
前往 👉 https://myaccount.google.com/permissions

確認「有權存取帳戶的任何 Google 服務」清單中：
- ✅ 只有你認識的應用程式
- ❌ 不認識的應用程式 → 立即點擊「刪除連結」撤銷授權

### HackMD
前往 👉 https://hackmd.io/settings#api

- 確認只有你建立的 token，刪除不認識或過期的 token
- 建議每季輪換一次 token

### GitHub
前往 👉 https://github.com/settings/tokens

- 確認 token 都有設定到期日
- 刪除不再使用的 token

### 🚨 Token 洩漏緊急處理
> 若 token 不小心貼到對話或上傳到 repo，請立即：
> 1. 到對應平台**撤銷（Revoke）** 舊 token
> 2. **重新建立**新 token，貼給 AI 讓它更新環境變數
> 3. AI 執行 `git log -p | grep -i token` 確認 repo 無殘留
> 4. 若已 push 敏感資料，聯絡平台客服並考慮使用 `git filter-branch` 清除歷史

---

## 更新紀錄

| 日期 | 版本 | 內容 |
|------|------|------|
| 2026-07-28 | v2.1 | 顯示名稱更新為 Gemini Notebook（原 NotebookLM，Google 官方更名）；套件/指令/MCP key 維持 notebooklm 舊名以相容既有設定 |
| 2026-06-11 | v2.0 | 重大改版：所有終端機指令改由 AI 直接執行，使用者只需提供 token 和完成瀏覽器 OAuth |
| 2026-06-10 | v1.6 | 修正所有 Claude Code MCP 語法；修正 OpenCode JSON 格式 |
| 2026-06-10 | v1.5 | 新增 HackMD（Step 11），含資安說明、三種 Agent 設定、常見問題 |
| 2026-06-08 | v1.4 | 新增 Hermes Agent 支援 |
| 2026-06-08 | v1.3 | 步驟重新編號，規則檔和開工收工改為附錄 |
| 2026-06-08 | v1.2 | 新增 Google Calendar、Gmail、Google Drive、Zotero |
| 2026-06-08 | v1.1 | 新增 Notion MCP 連接 |
| 2026-06-08 | v1.0 | 初版 |
