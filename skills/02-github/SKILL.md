---
name: ai-tools-github
description: 連接 GitHub CLI — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 GitHub」「設定 GitHub」時載入。
---

# 連接 GitHub CLI（通用版）v2.1

## 常見坑洞（先了解再安裝）

| 坑洞 | 原因 | 本流程解法 |
|------|------|-----------|
| 安裝後 `gh` 找不到 | winget 安裝後 PATH 不即時生效 | 安裝後告知使用者重開終端機再繼續 |
| 瀏覽器 OAuth 需手動輸入 8 位碼 | `gh auth login --web` 流程繁瑣 | 改用 PAT，AI 直接執行 `--with-token`，不需瀏覽器 |
| push 失敗（403） | token 權限不足，未勾選 `repo` | 引導使用者建立時明確勾選 `repo`（全選） |

---

## 步驟

### 1. AI 執行環境檢查

```bash
git --version
gh --version
```

若缺少工具，AI 直接執行安裝：

**Windows：**
```powershell
winget install --id Git.Git --accept-source-agreements --accept-package-agreements
winget install --id GitHub.cli --accept-source-agreements --accept-package-agreements
```

> ⚠️ Windows 安裝後 PATH 不即時生效。AI 告知使用者：「請完全關閉終端機並重新開啟，完成後告訴我。」
> 重開後 AI 再次執行 `git --version` 和 `gh --version` 確認。

**macOS：**
```bash
xcode-select --install
brew install gh
```

### 2. AI 先確認登入狀態

```bash
gh auth status
```

若已登入跳過步驟 3、4。

### 3. AI 引導使用者建立 Personal Access Token（PAT）

AI 告知使用者：

「請到 GitHub 建立 Personal Access Token：
1. 登入 GitHub → 右上角頭像 → **Settings**
2. 左側選單最下方 → **Developer settings**
3. **Personal access tokens** → **Tokens (classic)** → **Generate new token (classic)**
4. Note 隨意填、Expiration 建議選 **No expiration**
5. 勾選權限：**repo**（全選）、**read:org**
6. 點 **Generate token**，複製產生的 token（ghp_ 開頭），貼給我」

### 4. AI 直接執行登入（不需瀏覽器）

取得 token 後 AI 執行：

**Windows PowerShell：**
```powershell
$token = "使用者提供的TOKEN"
Write-Output $token | gh auth login --with-token
```

**macOS / Linux（bash）：**
```bash
echo "使用者提供的TOKEN" | gh auth login --with-token
```

### 5. AI 詢問並設定 git 使用者資訊

AI 問：「請提供你的 GitHub 名稱和 Email，我幫你設定。」

取得後 AI 執行：
```bash
git config --global user.name "使用者提供的名稱"
git config --global user.email "使用者提供的email"
```

### 6. AI 執行驗證

```bash
gh auth status
git config --global user.name
git config --global user.email
```

**常見問題：**

| 錯誤 | 處理方式 |
|------|---------|
| `'gh' is not recognized` | 重開終端機後再執行 |
| `gh: command not found` | 重新執行 winget 安裝後重開終端機 |
| push 失敗（403） | token 權限不足，重新建立並勾選 `repo` |
| token 過期 | 到 GitHub 重新建立，貼給 AI 重新執行步驟 4 |
| `Write-Output` 無效 | 改用 `$env:GH_TOKEN = "TOKEN"; gh auth status` 確認 |

---

## AI 操作守則

- 不使用 `git add .`，只 stage 指定檔案
- commit 前執行 `git diff --staged` 確認無敏感資料
- 不把 token、`.env`、憑證 commit 到 repo
