---
name: ai-tools-github
description: 連接 GitHub CLI — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 GitHub」「設定 GitHub」時載入。
---

# 連接 GitHub CLI（通用版）v2.0

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

> ⚠️ Windows 安裝後 PATH 不會立即生效，需重開終端機。AI 告知使用者：「請關閉並重新開啟終端機，完成後告訴我。」
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

若已登入跳過步驟 3。

### 3. 瀏覽器 OAuth（使用者操作）

AI 執行後告知使用者：「終端機會顯示一組 8 位驗證碼，同時瀏覽器會自動開啟（若未開啟請手動複製連結），請在瀏覽器輸入驗證碼完成 GitHub 授權，完成後告訴我。」

```bash
gh auth login --web --git-protocol https
```

**常見問題：**

| 錯誤 | 處理方式 |
|------|---------|
| 瀏覽器未自動開啟 | 手動複製終端機顯示的 `https://github.com/login/device` 連結 |
| 驗證碼輸入後無反應 | 確認在瀏覽器已登入正確 GitHub 帳號 |
| Windows 出現 `'gh' is not recognized` | 重開終端機後再執行 |
| 已登入但 push 失敗（403）| 執行 `gh auth refresh -s repo` 重新授權 |

### 4. AI 詢問並設定 git 使用者資訊

AI 問：「請提供你的 GitHub 名稱和 Email，我幫你設定。」

取得後 AI 執行：
```bash
git config --global user.name "使用者提供的名稱"
git config --global user.email "使用者提供的email"
```

### 5. AI 執行驗證

```bash
gh auth status
git config --global user.name
git config --global user.email
```

---

## AI 操作守則

- 不使用 `git add .`，只 stage 指定檔案
- commit 前執行 `git diff --staged` 確認無敏感資料
- 不把 token、`.env`、憑證 commit 到 repo
