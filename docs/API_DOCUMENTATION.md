# Tad Web API 文件

本文件說明如何使用 API 或純 SQL 指令來建立 Tad Web 的子網站。

## 目錄

1. [系統架構](#系統架構)
2. [使用 cURL 建立子網站](#使用-curl-建立子網站)
3. [使用純 SQL 指令建立子網站](#使用純-sql-指令建立子網站)
4. [網站設定選項說明](#網站設定選項說明)
5. [範例](#範例)

---

## 系統架構

Tad Web 是一個多人網頁系統，主要包含以下資料表：

- **tad_web**: 儲存子網站的基本資訊
- **tad_web_config**: 儲存子網站的設定選項
- **tad_web_cate**: 儲存子網站的分類資訊
- **tad_web_plugins**: 儲存子網站啟用的外掛功能
- **tad_web_roles**: 儲存子網站的使用者角色

---

## 使用 cURL 建立子網站

### 方法一：透過後台管理介面（需管理員權限）

**端點 (Endpoint)**: `/modules/tad_web/admin/main.php`

**請求方法**: POST

**必要參數**:
- `op`: 操作類型，固定值為 `insert_tad_web`
- `WebName`: 網站名稱（例如："地中海型貧血 (Thalassemia)"）
- `WebOwnerUid`: 擁有者的使用者 UID（數字）
- `WebTitle`: 網站全銜/標題
- `CateID`: 所屬分類 ID（0 表示無分類）
- `WebSort`: 排序順序（數字）
- `year`: 學年度（例如："2026"）

**選用參數**:
- `WebEnable`: 網站狀態（"1" 啟用，"0" 關閉，預設 "1"）

### cURL 範例

```bash
# 首先需要取得登入的 Cookie（需要有管理員權限）
# 假設您已經有 XOOPS 的 session cookie

curl -X POST "https://your-domain.com/modules/tad_web/admin/main.php" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Cookie: XOOPS_SESSION_ID=your_session_id" \
  -d "op=insert_tad_web" \
  -d "WebName=地中海型貧血 (Thalassemia)" \
  -d "WebOwnerUid=2" \
  -d "WebTitle=網管測試帳號甲的專用網頁" \
  -d "CateID=0" \
  -d "WebSort=0" \
  -d "year=2026"
```

### 方法二：設定網站配置

建立網站後，需要設定網站的各項配置。

**端點**: `/modules/tad_web/config.php`

**請求方法**: POST

**必要參數**:
- `op`: 操作類型，使用 `save_config`
- `WebID`: 網站 ID（由建立網站時取得）

**配置參數範例**:
- `other_web_url`: 外部網站 URL
- `menu_font_size`: 選單字型大小（例如："100%"）
- `theme_side`: 側邊欄位置（"left" 或 "right"）
- `defalut_theme`: 預設主題（例如："for_tad_web_theme_2"）
- `use_simple_menu`: 使用簡易選單（1 或 0）
- `login_method[]`: 登入方式（陣列，例如：["auth0"]）

### 設定網站配置的 cURL 範例

```bash
curl -X POST "https://your-domain.com/modules/tad_web/config.php" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Cookie: XOOPS_SESSION_ID=your_session_id" \
  -d "op=save_config" \
  -d "WebID=1" \
  -d "WebName=地中海型貧血 (Thalassemia)" \
  -d "WebOwner=網管測試帳號甲" \
  -d "CateID=0" \
  -d "other_web_url=" \
  -d "menu_font_size=100%" \
  -d "theme_side=right" \
  -d "defalut_theme=for_tad_web_theme_2" \
  -d "use_simple_menu=1" \
  -d "login_method[]=auth0"
```

---

## 使用純 SQL 指令建立子網站

### 步驟 1: 建立子網站基本資料

在 `tad_web` 資料表中插入新記錄：

```sql
INSERT INTO `tad_web` (
    `CateID`, 
    `WebName`, 
    `WebSort`, 
    `WebEnable`, 
    `WebCounter`, 
    `WebOwner`, 
    `WebOwnerUid`, 
    `WebTitle`, 
    `CreatDate`, 
    `WebYear`, 
    `used_size`, 
    `last_accessed`
) VALUES (
    0,                                    -- CateID: 所屬分類 (0 表示無分類)
    '地中海型貧血 (Thalassemia)',        -- WebName: 網站名稱
    0,                                    -- WebSort: 排序
    '1',                                  -- WebEnable: 狀態 ('1' 啟用, '0' 關閉)
    0,                                    -- WebCounter: 人氣計數器
    '網管測試帳號甲',                     -- WebOwner: 擁有者名稱
    2,                                    -- WebOwnerUid: 擁有者 UID
    '2026 網管測試帳號甲的專用網頁',      -- WebTitle: 網站全銜
    NOW(),                                -- CreatDate: 建立日期
    2026,                                 -- WebYear: 學年度
    0,                                    -- used_size: 已使用空間
    NOW()                                 -- last_accessed: 最後訪問時間
);
```

取得剛建立的 WebID：

```sql
SELECT LAST_INSERT_ID() AS WebID;
```

假設取得的 WebID 為 1，接下來設定網站配置。

### 步驟 2: 設定網站配置

在 `tad_web_config` 資料表中插入配置資料：

```sql
-- 啟用的外掛功能
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('web_plugin_enable_arr', 'page,link,action,aboutus', 0, 0, 1);

-- 預設分類
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('default_class', '1', 0, 0, 1);

-- 外部網站 URL
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('other_web_url', '', 0, 0, 1);

-- 選單字型大小
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('menu_font_size', '100%', 0, 0, 1);

-- 側邊欄位置
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('theme_side', 'right', 0, 0, 1);

-- 預設主題
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('defalut_theme', 'for_tad_web_theme_2', 0, 0, 1);

-- 使用簡易選單
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('use_simple_menu', '1', 0, 0, 1);

-- 登入設定
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('login_config', 'auth0', 0, 0, 1);

-- 背景圖片
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('web_bg', 'th-plaid52.gif', 0, 0, 1);

-- 背景重複設定
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('bg_repeat', 'no-repeat', 0, 0, 1);

-- 背景附加設定
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('bg_attachment', '', 0, 0, 1);

-- 背景位置
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('bg_postiton', 'left top', 0, 0, 1);

-- 背景大小
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('bg_size', 'cover', 0, 0, 1);

-- 標題圖上方位置
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('head_top', '-387', 0, 0, 1);

-- 標題圖左方位置
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('head_left', '0', 0, 0, 1);

-- 標題圖片
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('web_head', '24.jpg', 0, 0, 1);

-- Logo 上方位置
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('logo_top', '47.796875', 0, 0, 1);

-- Logo 左方位置
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('logo_left', '53.796875', 0, 0, 1);

-- 區塊圖片文字顏色
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('block_pic_text_color', '#ABBF6B', 0, 0, 1);

-- 區塊圖片邊框顏色
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('block_pic_border_color', '#ffffff', 0, 0, 1);

-- 區塊圖片文字大小
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('block_pic_text_size', '18', 0, 0, 1);

-- 區塊圖片字型
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('block_pic_font', 'DroidSansFallback.ttf', 0, 0, 1);

-- 使用區塊圖片
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('use_block_pic', '0', 0, 0, 1);

-- 已使用空間設定
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('used_size', '1', 0, 0, 1);

-- 網站 Logo
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('web_logo', 'auto_logo.png', 0, 0, 1);
```

### 步驟 3: 建立預設分類（選用）

如果需要建立預設分類（例如「關於我們」），可以執行：

```sql
INSERT INTO `tad_web_cate` (
    `WebID`, 
    `CateName`, 
    `ColName`, 
    `ColSN`, 
    `CateSort`, 
    `CateEnable`, 
    `CateCounter`
) VALUES (
    1,                                    -- WebID: 所屬網站
    '2026 網管測試帳號甲的專用網頁',      -- CateName: 分類名稱
    'aboutus',                            -- ColName: 欄位名稱
    0,                                    -- ColSN: 欄位編號
    0,                                    -- CateSort: 排序
    '1',                                  -- CateEnable: 狀態
    0                                     -- CateCounter: 人氣
);
```

### 步驟 4: 啟用預設外掛（選用）

如果需要啟用特定外掛功能：

```sql
-- 啟用「頁面」外掛
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) 
VALUES ('page', '頁面', 1, '1', 1);

-- 啟用「連結」外掛
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) 
VALUES ('link', '連結', 2, '1', 1);

-- 啟用「行事曆」外掛
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) 
VALUES ('action', '行事曆', 3, '1', 1);

-- 啟用「關於我們」外掛
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) 
VALUES ('aboutus', '關於我們', 4, '1', 1);
```

---

## 網站設定選項說明

以下是 `tad_web_config` 資料表中常用的設定選項：

| ConfigName | 說明 | 範例值 | 類型 |
|------------|------|--------|------|
| web_plugin_enable_arr | 啟用的外掛列表（以逗號分隔） | "page,link,action,aboutus" | 字串 |
| default_class | 預設分類 ID | "1" | 數字 |
| other_web_url | 外部網站 URL | "https://example.com" | URL |
| menu_font_size | 選單字型大小 | "100%" | 百分比 |
| theme_side | 側邊欄位置 | "left" 或 "right" | 字串 |
| defalut_theme | 預設主題 | "for_tad_web_theme_2" | 字串 |
| use_simple_menu | 使用簡易選單 | "0" 或 "1" | 布林 |
| login_config | 登入方式設定 | "auth0" 或 "auth0;google" | 字串 |
| web_bg | 背景圖片檔名 | "th-plaid52.gif" | 檔名 |
| bg_repeat | 背景重複模式 | "repeat", "no-repeat", "repeat-x", "repeat-y" | CSS 值 |
| bg_attachment | 背景附加模式 | "scroll", "fixed" | CSS 值 |
| bg_postiton | 背景位置 | "left top", "center center" | CSS 值 |
| bg_size | 背景大小 | "cover", "contain", "auto" | CSS 值 |
| head_top | 標題圖上方位移 | "-387" | 像素 |
| head_left | 標題圖左方位移 | "0" | 像素 |
| web_head | 標題圖片檔名 | "24.jpg" | 檔名 |
| logo_top | Logo 上方位置 | "47.796875" | 像素 |
| logo_left | Logo 左方位置 | "53.796875" | 像素 |
| web_logo | Logo 圖片檔名 | "auto_logo.png" | 檔名 |
| block_pic_text_color | 區塊圖片文字顏色 | "#ABBF6B" | 十六進位顏色 |
| block_pic_border_color | 區塊圖片邊框顏色 | "#ffffff" | 十六進位顏色 |
| block_pic_text_size | 區塊圖片文字大小 | "18" | 像素 |
| block_pic_font | 區塊圖片字型 | "DroidSansFallback.ttf" | 字型檔名 |
| use_block_pic | 使用區塊圖片 | "0" 或 "1" | 布林 |
| used_size | 已使用空間 | "1" | 數字 |

---

## 範例

### 完整的子網站建立範例（使用 SQL）

以下是完整建立一個新子網站的 SQL 腳本：

```sql
-- 1. 建立子網站
INSERT INTO `tad_web` (
    `CateID`, `WebName`, `WebSort`, `WebEnable`, `WebCounter`, 
    `WebOwner`, `WebOwnerUid`, `WebTitle`, `CreatDate`, `WebYear`, 
    `used_size`, `last_accessed`
) VALUES (
    0, '地中海型貧血 (Thalassemia)', 0, '1', 44, 
    '網管測試帳號甲', 2, '網管測試帳號甲的專用網頁', 
    '2026-02-06 13:51:42', 2026, 947621, '2026-02-06 15:48:51'
);

-- 取得剛建立的 WebID（假設為 1）
SET @WebID = LAST_INSERT_ID();

-- 2. 設定網站配置
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) VALUES
('web_plugin_enable_arr', 'page,link,action,aboutus', 0, 0, @WebID),
('default_class', '1', 0, 0, @WebID),
('other_web_url', '', 0, 0, @WebID),
('menu_font_size', '100%', 0, 0, @WebID),
('theme_side', 'right', 0, 0, @WebID),
('defalut_theme', 'for_tad_web_theme_2', 0, 0, @WebID),
('use_simple_menu', '1', 0, 0, @WebID),
('login_config', 'auth0', 0, 0, @WebID),
('web_bg', 'th-plaid52.gif', 0, 0, @WebID),
('bg_repeat', 'no-repeat', 0, 0, @WebID),
('bg_attachment', '', 0, 0, @WebID),
('bg_postiton', 'left top', 0, 0, @WebID),
('bg_size', 'cover', 0, 0, @WebID),
('head_top', '-387', 0, 0, @WebID),
('head_left', '0', 0, 0, @WebID),
('web_head', '24.jpg', 0, 0, @WebID),
('logo_top', '47.796875', 0, 0, @WebID),
('logo_left', '53.796875', 0, 0, @WebID),
('block_pic_text_color', '#ABBF6B', 0, 0, @WebID),
('block_pic_border_color', '#ffffff', 0, 0, @WebID),
('block_pic_text_size', '18', 0, 0, @WebID),
('block_pic_font', 'DroidSansFallback.ttf', 0, 0, @WebID),
('use_block_pic', '0', 0, 0, @WebID),
('used_size', '1', 0, 0, @WebID),
('web_logo', 'auto_logo.png', 0, 0, @WebID);

-- 3. 建立預設分類
INSERT INTO `tad_web_cate` (
    `WebID`, `CateName`, `ColName`, `ColSN`, 
    `CateSort`, `CateEnable`, `CateCounter`
) VALUES (
    @WebID, '網管測試帳號甲的專用網頁', 'aboutus', 0, 0, '1', 0
);

-- 4. 啟用外掛
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) VALUES
('page', '頁面', 1, '1', @WebID),
('link', '連結', 2, '1', @WebID),
('action', '行事曆', 3, '1', @WebID),
('aboutus', '關於我們', 4, '1', @WebID);

-- 完成！顯示建立的 WebID
SELECT @WebID AS 'Created WebID';
```

---

## 注意事項

1. **權限要求**：
   - 使用 cURL 方式需要有效的管理員 session
   - 直接使用 SQL 需要有資料庫的寫入權限

2. **資料表前綴**：
   - 本文檔假設資料表前綴為空，實際使用時請根據您的 XOOPS 設定調整
   - 例如：如果前綴為 `xoops_`，則 `tad_web` 應改為 `xoops_tad_web`

3. **WebID 的取得**：
   - 建立網站後，需要取得 `LAST_INSERT_ID()` 作為 WebID
   - 後續的配置和設定都需要使用這個 WebID

4. **檔案系統**：
   - 建立網站後，系統會自動建立相應的檔案目錄
   - 目錄路徑通常為：`/uploads/tad_web/{WebID}/`

5. **快取清除**：
   - 建立或修改網站後，建議清除相關快取檔案
   - 快取檔案位置：`/var/tad_web/{WebID}/`

6. **必要後續處理**：
   - 建立網站後，系統通常會執行額外的初始化工作（如建立 logo）
   - 直接使用 SQL 建立時，可能需要手動執行這些初始化步驟

---

## 相關資源

- Tad Web 官方網站：https://tad0616.net/modules/tad_modules/index.php?module_sn=26
- XOOPS 官方網站：https://xoops.org/
- 資料庫結構檔：`/modules/tad_web/sql/mysql.sql`

---

## 技術支援

如有問題，請聯繫：
- 開發者：Tad (tad0616@gmail.com)
- GitHub：https://github.com/diagmindtw/tad_web
