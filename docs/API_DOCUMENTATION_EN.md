# Tad Web API Documentation

This document explains how to create Tad Web sub-sites using either API calls or pure SQL commands.

## Table of Contents

1. [System Architecture](#system-architecture)
2. [Create Sub-site Using cURL](#create-sub-site-using-curl)
3. [Create Sub-site Using Pure SQL](#create-sub-site-using-pure-sql)
4. [Configuration Options Reference](#configuration-options-reference)
5. [Examples](#examples)

---

## System Architecture

Tad Web is a multi-user web system with the following main database tables:

- **tad_web**: Stores basic information about sub-sites
- **tad_web_config**: Stores configuration options for sub-sites
- **tad_web_cate**: Stores category information for sub-sites
- **tad_web_plugins**: Stores enabled plugins for sub-sites
- **tad_web_roles**: Stores user roles for sub-sites

---

## Create Sub-site Using cURL

### Method 1: Through Admin Backend (Requires Admin Privileges)

**Endpoint**: `/modules/tad_web/admin/main.php`

**Request Method**: POST

**Required Parameters**:
- `op`: Operation type, fixed value `insert_tad_web`
- `WebName`: Site name (e.g., "Thalassemia")
- `WebOwnerUid`: Owner's user UID (numeric)
- `WebTitle`: Site full title
- `CateID`: Category ID (0 for no category)
- `WebSort`: Sort order (numeric)
- `year`: Academic year (e.g., "2026")

**Optional Parameters**:
- `WebEnable`: Site status ("1" enabled, "0" disabled, default "1")

### cURL Example

```bash
# First, obtain login cookie (requires admin privileges)
# Assuming you have a XOOPS session cookie

curl -X POST "https://your-domain.com/modules/tad_web/admin/main.php" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Cookie: XOOPS_SESSION_ID=your_session_id" \
  -d "op=insert_tad_web" \
  -d "WebName=Thalassemia Research Site" \
  -d "WebOwnerUid=2" \
  -d "WebTitle=Admin Test Account Website" \
  -d "CateID=0" \
  -d "WebSort=0" \
  -d "year=2026"
```

### Method 2: Configure Site Settings

After creating a site, configure its various settings.

**Endpoint**: `/modules/tad_web/config.php`

**Request Method**: POST

**Required Parameters**:
- `op`: Operation type, use `save_config`
- `WebID`: Site ID (obtained from site creation)

**Configuration Parameters Example**:
- `other_web_url`: External website URL
- `menu_font_size`: Menu font size (e.g., "100%")
- `theme_side`: Sidebar position ("left" or "right")
- `defalut_theme`: Default theme (e.g., "for_tad_web_theme_2")
- `use_simple_menu`: Use simple menu (1 or 0)
- `login_method[]`: Login method (array, e.g., ["auth0"])

### cURL Example for Site Configuration

```bash
curl -X POST "https://your-domain.com/modules/tad_web/config.php" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Cookie: XOOPS_SESSION_ID=your_session_id" \
  -d "op=save_config" \
  -d "WebID=1" \
  -d "WebName=Thalassemia Research Site" \
  -d "WebOwner=Admin Test Account" \
  -d "CateID=0" \
  -d "other_web_url=" \
  -d "menu_font_size=100%" \
  -d "theme_side=right" \
  -d "defalut_theme=for_tad_web_theme_2" \
  -d "use_simple_menu=1" \
  -d "login_method[]=auth0"
```

---

## Create Sub-site Using Pure SQL

### Step 1: Create Basic Site Data

Insert a new record into the `tad_web` table:

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
    0,                                    -- CateID: Category (0 = no category)
    'Thalassemia Research Site',         -- WebName: Site name
    0,                                    -- WebSort: Sort order
    '1',                                  -- WebEnable: Status ('1' = enabled, '0' = disabled)
    0,                                    -- WebCounter: Page view counter
    'Admin Test Account',                 -- WebOwner: Owner name
    2,                                    -- WebOwnerUid: Owner UID
    '2026 Admin Test Account Website',   -- WebTitle: Site full title
    NOW(),                                -- CreatDate: Creation date
    2026,                                 -- WebYear: Academic year
    0,                                    -- used_size: Used storage space
    NOW()                                 -- last_accessed: Last accessed time
);
```

Retrieve the newly created WebID:

```sql
SELECT LAST_INSERT_ID() AS WebID;
```

Assuming the retrieved WebID is 1, proceed with site configuration.

### Step 2: Configure Site Settings

Insert configuration data into the `tad_web_config` table:

```sql
-- Enabled plugins
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('web_plugin_enable_arr', 'page,link,action,aboutus', 0, 0, 1);

-- Default category
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('default_class', '1', 0, 0, 1);

-- External website URL
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('other_web_url', '', 0, 0, 1);

-- Menu font size
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('menu_font_size', '100%', 0, 0, 1);

-- Sidebar position
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('theme_side', 'right', 0, 0, 1);

-- Default theme
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('defalut_theme', 'for_tad_web_theme_2', 0, 0, 1);

-- Use simple menu
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('use_simple_menu', '1', 0, 0, 1);

-- Login configuration
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('login_config', 'auth0', 0, 0, 1);

-- Background image
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('web_bg', 'th-plaid52.gif', 0, 0, 1);

-- Background repeat setting
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('bg_repeat', 'no-repeat', 0, 0, 1);

-- Background attachment
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('bg_attachment', '', 0, 0, 1);

-- Background position (Note: field name is 'postiton' in original system)
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('bg_postiton', 'left top', 0, 0, 1);

-- Background size
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('bg_size', 'cover', 0, 0, 1);

-- Header image top position
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('head_top', '-387', 0, 0, 1);

-- Header image left position
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('head_left', '0', 0, 0, 1);

-- Header image
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('web_head', '24.jpg', 0, 0, 1);

-- Logo top position
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('logo_top', '47.796875', 0, 0, 1);

-- Logo left position
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('logo_left', '53.796875', 0, 0, 1);

-- Block picture text color
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('block_pic_text_color', '#ABBF6B', 0, 0, 1);

-- Block picture border color
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('block_pic_border_color', '#ffffff', 0, 0, 1);

-- Block picture text size
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('block_pic_text_size', '18', 0, 0, 1);

-- Block picture font
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('block_pic_font', 'DroidSansFallback.ttf', 0, 0, 1);

-- Use block picture
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('use_block_pic', '0', 0, 0, 1);

-- Used size setting
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('used_size', '1', 0, 0, 1);

-- Site logo
INSERT INTO `tad_web_config` (`ConfigName`, `ConfigValue`, `ConfigSort`, `CateID`, `WebID`) 
VALUES ('web_logo', 'auto_logo.png', 0, 0, 1);
```

### Step 3: Create Default Category (Optional)

If you need to create a default category (e.g., "About Us"), execute:

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
    1,                                    -- WebID: Owner site
    'Admin Test Account Website',         -- CateName: Category name
    'aboutus',                            -- ColName: Column name
    0,                                    -- ColSN: Column number
    0,                                    -- CateSort: Sort order
    '1',                                  -- CateEnable: Status
    0                                     -- CateCounter: Page views
);
```

### Step 4: Enable Default Plugins (Optional)

If you need to enable specific plugin features:

```sql
-- Enable 'page' plugin
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) 
VALUES ('page', 'Page', 1, '1', 1);

-- Enable 'link' plugin
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) 
VALUES ('link', 'Link', 2, '1', 1);

-- Enable 'action' plugin
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) 
VALUES ('action', 'Calendar', 3, '1', 1);

-- Enable 'aboutus' plugin
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) 
VALUES ('aboutus', 'About Us', 4, '1', 1);
```

---

## Configuration Options Reference

Below are commonly used configuration options in the `tad_web_config` table:

| ConfigName | Description | Example Value | Type |
|------------|-------------|---------------|------|
| web_plugin_enable_arr | Enabled plugins list (comma-separated) | "page,link,action,aboutus" | String |
| default_class | Default category ID | "1" | Number |
| other_web_url | External website URL | "https://example.com" | URL |
| menu_font_size | Menu font size | "100%" | Percentage |
| theme_side | Sidebar position | "left" or "right" | String |
| defalut_theme | Default theme | "for_tad_web_theme_2" | String |
| use_simple_menu | Use simple menu | "0" or "1" | Boolean |
| login_config | Login method configuration | "auth0" or "auth0;google" | String |
| web_bg | Background image filename | "th-plaid52.gif" | Filename |
| bg_repeat | Background repeat mode | "repeat", "no-repeat", "repeat-x", "repeat-y" | CSS value |
| bg_attachment | Background attachment mode | "scroll", "fixed" | CSS value |
| bg_postiton | Background position (Note: field name is 'postiton' in original system) | "left top", "center center" | CSS value |
| bg_size | Background size | "cover", "contain", "auto" | CSS value |
| head_top | Header image top offset | "-387" | Pixels |
| head_left | Header image left offset | "0" | Pixels |
| web_head | Header image filename | "24.jpg" | Filename |
| logo_top | Logo top position | "47.796875" | Pixels |
| logo_left | Logo left position | "53.796875" | Pixels |
| web_logo | Logo image filename | "auto_logo.png" | Filename |
| block_pic_text_color | Block picture text color | "#ABBF6B" | Hex color |
| block_pic_border_color | Block picture border color | "#ffffff" | Hex color |
| block_pic_text_size | Block picture text size | "18" | Pixels |
| block_pic_font | Block picture font | "DroidSansFallback.ttf" | Font filename |
| use_block_pic | Use block picture | "0" or "1" | Boolean |
| used_size | Used storage space | "1" | Number |

---

## Examples

### Complete Sub-site Creation Example (Using SQL)

Below is a complete SQL script to create a new sub-site:

```sql
-- 1. Create sub-site
INSERT INTO `tad_web` (
    `CateID`, `WebName`, `WebSort`, `WebEnable`, `WebCounter`, 
    `WebOwner`, `WebOwnerUid`, `WebTitle`, `CreatDate`, `WebYear`, 
    `used_size`, `last_accessed`
) VALUES (
    0, 'Thalassemia Research Site', 0, '1', 44, 
    'Admin Test Account', 2, 'Admin Test Account Website', 
    '2026-02-06 13:51:42', 2026, 947621, '2026-02-06 15:48:51'
);

-- Get the newly created WebID (assuming it's 1)
SET @WebID = LAST_INSERT_ID();

-- 2. Configure site settings
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

-- 3. Create default category
INSERT INTO `tad_web_cate` (
    `WebID`, `CateName`, `ColName`, `ColSN`, 
    `CateSort`, `CateEnable`, `CateCounter`
) VALUES (
    @WebID, 'Admin Test Account Website', 'aboutus', 0, 0, '1', 0
);

-- 4. Enable plugins
INSERT INTO `tad_web_plugins` (`PluginDirname`, `PluginTitle`, `PluginSort`, `PluginEnable`, `WebID`) VALUES
('page', 'Page', 1, '1', @WebID),
('link', 'Link', 2, '1', @WebID),
('action', 'Calendar', 3, '1', @WebID),
('aboutus', 'About Us', 4, '1', @WebID);

-- Done! Display the created WebID
SELECT @WebID AS 'Created WebID';
```

---

## Important Notes

1. **Permission Requirements**:
   - Using cURL method requires a valid admin session
   - Using SQL directly requires database write permissions

2. **Table Prefix**:
   - This documentation assumes an empty table prefix; adjust according to your XOOPS configuration
   - For example: if prefix is `xoops_`, then `tad_web` should be `xoops_tad_web`

3. **Obtaining WebID**:
   - After creating a site, retrieve `LAST_INSERT_ID()` as the WebID
   - Subsequent configurations and settings all require this WebID

4. **File System**:
   - After creating a site, the system automatically creates corresponding file directories
   - Directory path is typically: `/uploads/tad_web/{WebID}/`

5. **Cache Clearing**:
   - After creating or modifying a site, it's recommended to clear related cache files
   - Cache file location: `/var/tad_web/{WebID}/`

6. **Required Post-Processing**:
   - After site creation, the system typically performs additional initialization work (such as creating logos)
   - When creating directly with SQL, you may need to manually perform these initialization steps

7. **Field Spelling**:
   - Note that the `bg_postiton` field name is indeed spelled "postiton" (not "position") in the original system
   - This is part of the original system design; use the spelling as documented

---

## Related Resources

- Tad Web Official Website: https://tad0616.net/modules/tad_modules/index.php?module_sn=26
- XOOPS Official Website: https://xoops.org/
- Database Structure File: `/modules/tad_web/sql/mysql.sql`

---

## Technical Support

For questions, please contact:
- Developer: Tad (tad0616@gmail.com)
- GitHub: https://github.com/diagmindtw/tad_web
