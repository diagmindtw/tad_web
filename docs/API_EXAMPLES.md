# Tad Web API 實用範例 / Practical API Examples

本文件提供實際可用的 API 呼叫範例。
This document provides practical, working API call examples.

## 📋 目錄 / Table of Contents

1. [如何取得 Session Cookie](#如何取得-session-cookie)
2. [建立子網站範例](#建立子網站範例)
3. [PowerShell 範例（Windows）](#powershell-範例windows)
4. [Bash 範例（Linux/Mac）](#bash-範例linuxmac)

---

## 如何取得 Session Cookie / How to Get Session Cookie

### 步驟 / Steps:

1. **登入系統** / Login to the system
   - 使用管理員帳號登入 Tad Web 系統
   - Login with an admin account to Tad Web system

2. **開啟開發者工具** / Open Developer Tools
   - 按 F12 或右鍵選擇「檢查」
   - Press F12 or right-click and select "Inspect"

3. **切換到 Network 分頁** / Switch to Network Tab
   - 點選「Network」或「網路」分頁
   - Click on "Network" tab

4. **執行任何操作** / Perform Any Action
   - 在系統中點擊任何連結或按鈕
   - Click any link or button in the system

5. **查看請求標頭** / View Request Headers
   - 選擇任一個請求
   - Select any request
   - 在「Headers」或「標頭」區找到 Cookie
   - Find Cookie in "Headers" section
   - 複製類似這樣的內容：`xoops_session_69853b2b=0c53e5df34e6a6a3787e9043feb8b5b0`
   - Copy something like: `xoops_session_69853b2b=0c53e5df34e6a6a3787e9043feb8b5b0`

**重要提示 / Important Note:**
- Cookie 名稱格式為 `xoops_session_` 加上隨機字串
- Cookie name format is `xoops_session_` plus a random string
- 每個安裝的系統其 Cookie 名稱會不同
- Cookie name differs for each installation
- Session 會過期，需要定期更新
- Sessions expire and need periodic updates

---

## 建立子網站範例 / Create Sub-site Examples

### 方法一：使用 -F 選項（推薦）/ Method 1: Using -F Option (Recommended)

```bash
curl -X POST "https://school.diagmindtw.com/modules/tad_web/admin/main.php" \
  -H "Cookie: xoops_session_69853b2b=your_actual_session_id_here" \
  -F "op=insert_tad_web" \
  -F "WebName=API測試階段02" \
  -F "year=113 學年度" \
  -F "WebTitle=301" \
  -F "WebOwnerUid=2" \
  -F "WebEnable=1" \
  -F "WebSort=1" \
  -F "CateID=0"
```

### 方法二：使用 --data-urlencode / Method 2: Using --data-urlencode

```bash
curl -X POST "https://school.diagmindtw.com/modules/tad_web/admin/main.php" \
  -H "Cookie: xoops_session_69853b2b=your_actual_session_id_here" \
  -d "op=insert_tad_web" \
  --data-urlencode "WebName=API測試階段02" \
  --data-urlencode "year=113 學年度" \
  --data-urlencode "WebTitle=301" \
  -d "WebOwnerUid=2" \
  -d "WebEnable=1" \
  -d "WebSort=1" \
  -d "CateID=0"
```

### 查看詳細資訊（除錯用）/ View Details (for Debugging)

```bash
curl -v -X POST "https://school.diagmindtw.com/modules/tad_web/admin/main.php" \
  -H "Cookie: xoops_session_69853b2b=your_actual_session_id_here" \
  -F "op=insert_tad_web" \
  -F "WebName=測試網站" \
  -F "WebOwnerUid=2" \
  -F "WebTitle=測試標題" \
  -F "CateID=0" \
  -F "WebSort=0" \
  -F "WebEnable=1" \
  -F "year=2026"
```

使用 `-v` 選項可以看到：
Using `-v` option shows:
- 完整的請求標頭 / Complete request headers
- 伺服器回應 / Server response
- HTTP 狀態碼 / HTTP status code

---

## PowerShell 範例（Windows）

### 基本範例 / Basic Example

```powershell
# 設定變數
$domain = "school.diagmindtw.com"
$sessionCookie = "xoops_session_69853b2b=your_actual_session_id_here"

# 使用 curl.exe (Windows 10/11 內建)
curl.exe -X POST "https://$domain/modules/tad_web/admin/main.php" `
  -H "Cookie: $sessionCookie" `
  -F "op=insert_tad_web" `
  -F "WebName=API測試階段03" `
  -F "year=113 學年度" `
  -F "WebTitle=測試班級" `
  -F "WebOwnerUid=2" `
  -F "WebEnable=1" `
  -F "WebSort=1" `
  -F "CateID=0"
```

### 使用 Invoke-WebRequest / Using Invoke-WebRequest

```powershell
$uri = "https://school.diagmindtw.com/modules/tad_web/admin/main.php"
$sessionCookie = "xoops_session_69853b2b=your_actual_session_id_here"

$body = @{
    op = "insert_tad_web"
    WebName = "API測試階段04"
    year = "113 學年度"
    WebTitle = "測試班級"
    WebOwnerUid = "2"
    WebEnable = "1"
    WebSort = "1"
    CateID = "0"
}

$headers = @{
    Cookie = $sessionCookie
}

# 發送請求
Invoke-WebRequest -Uri $uri -Method Post -Headers $headers -Body $body -ContentType "application/x-www-form-urlencoded"
```

---

## Bash 範例（Linux/Mac）

### 完整腳本 / Complete Script

```bash
#!/bin/bash

# Tad Web API 建立子網站腳本
# Script to create sub-site via Tad Web API

# 設定參數 / Configure parameters
DOMAIN="school.diagmindtw.com"
SESSION_COOKIE="xoops_session_69853b2b=your_actual_session_id_here"
API_ENDPOINT="https://${DOMAIN}/modules/tad_web/admin/main.php"

# 網站資訊 / Site information
WEB_NAME="API測試網站"
WEB_TITLE="測試班級標題"
YEAR="113 學年度"
OWNER_UID="2"
CATE_ID="0"
WEB_SORT="0"
WEB_ENABLE="1"

# 執行 API 呼叫 / Execute API call
echo "Creating sub-site: ${WEB_NAME}"
echo "Endpoint: ${API_ENDPOINT}"
echo ""

curl -X POST "${API_ENDPOINT}" \
  -H "Cookie: ${SESSION_COOKIE}" \
  -F "op=insert_tad_web" \
  -F "WebName=${WEB_NAME}" \
  -F "year=${YEAR}" \
  -F "WebTitle=${WEB_TITLE}" \
  -F "WebOwnerUid=${OWNER_UID}" \
  -F "WebEnable=${WEB_ENABLE}" \
  -F "WebSort=${WEB_SORT}" \
  -F "CateID=${CATE_ID}"

echo ""
echo "Done!"
```

### 使用方法 / How to Use

1. 將上述腳本儲存為 `create_web.sh`
   Save the script as `create_web.sh`

2. 賦予執行權限 / Grant execution permission:
   ```bash
   chmod +x create_web.sh
   ```

3. 編輯腳本，更新 SESSION_COOKIE 和其他參數
   Edit the script to update SESSION_COOKIE and other parameters

4. 執行腳本 / Run the script:
   ```bash
   ./create_web.sh
   ```

---

## 參數說明 / Parameter Description

| 參數名稱 | 必填 | 說明 | 範例值 |
|---------|-----|------|-------|
| op | ✓ | 操作類型 | `insert_tad_web` |
| WebName | ✓ | 網站名稱 | `班級網站` |
| WebTitle | ✓ | 網站標題 | `301班` |
| WebOwnerUid | ✓ | 擁有者 UID | `2` |
| year |  | 學年度 | `113 學年度` |
| CateID |  | 分類 ID | `0` (無分類) |
| WebSort |  | 排序 | `0` |
| WebEnable |  | 啟用狀態 | `1` (啟用) |

| Parameter | Required | Description | Example Value |
|-----------|----------|-------------|---------------|
| op | ✓ | Operation type | `insert_tad_web` |
| WebName | ✓ | Site name | `Class Website` |
| WebTitle | ✓ | Site title | `Class 301` |
| WebOwnerUid | ✓ | Owner UID | `2` |
| year |  | Academic year | `2026` |
| CateID |  | Category ID | `0` (no category) |
| WebSort |  | Sort order | `0` |
| WebEnable |  | Enable status | `1` (enabled) |

---

## 常見問題 / Common Issues

### 1. 收到空白回應 / Receiving Empty Response

**原因 / Cause:** Session cookie 可能已過期或無效
- Session cookie may have expired or is invalid

**解決方案 / Solution:**
```bash
# 加上 -v 查看詳細資訊
# Add -v to see details
curl -v -X POST "https://..." ...
```

### 2. 權限錯誤 / Permission Error

**原因 / Cause:** 使用的帳號不是管理員
- The account used is not an admin

**解決方案 / Solution:**
- 確認登入的是管理員帳號
- Confirm you're logged in with an admin account
- 重新取得正確的 session cookie
- Obtain the correct session cookie again

### 3. 中文亂碼 / Chinese Characters Garbled

**原因 / Cause:** 編碼問題
- Encoding issue

**解決方案 / Solution:**
- 使用 `-F` 選項而非 `-d`
- Use `-F` option instead of `-d`
- 或使用 `--data-urlencode`
- Or use `--data-urlencode`

---

## 相關文件 / Related Documentation

- [API_DOCUMENTATION.md](API_DOCUMENTATION.md) - 完整 API 文件（中文）
- [API_DOCUMENTATION_EN.md](API_DOCUMENTATION_EN.md) - Complete API Documentation (English)

---

## 技術支援 / Technical Support

如有問題，請聯繫：
For questions, please contact:
- GitHub: https://github.com/diagmindtw/tad_web
- Developer: Tad (tad0616@gmail.com)
