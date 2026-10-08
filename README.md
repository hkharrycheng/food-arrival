# 🍽️ 上菜 Checklist - HIS Tools

> 版本：v1.00
> 最後更新：2026-10-08

## 📖 簡介

上菜 Checklist 係一個輕量級嘅餐廳用餐記錄工具，幫你記錄每道菜嘅上菜時間，自動計算等候時長。

- 手機友善，深色主題
- 本地儲存，唔需要伺服器
- 支援 Agent 助手，影相自動認菜式

---

## 🔗 連結

- **GitHub Pages**：https://hkharrycheng.github.io/food-arrival/
- **Repository**：https://github.com/hkharrycheng/food-arrival

---

## 📁 文件結構

```
food-arrival/
├── index.html       # 主頁面（內含 CSS/JS）
├── food-data.json   # 預設數據模板
└── README.md        # 說明文件
```

---

## 🧩 功能說明

### 核心功能
1. **餐單轉清單**：手動輸入食物名稱同價錢，加入清單
2. **一鍵切換狀態**：點擊食物項目，切換「未上」/「已上」
3. **自動計時**：記錄上菜時間，自動計算等候時長
4. **即時統計**：顯示未上/已上數量同總金額
5. **枱號設定**：可設定枱號同落單時間
6. **重置功能**：清除本地數據，重新載入模板

### Agent 助手功能
用戶喺對話中輸入指令，Agent 自動處理：

| 指令 | 功能 |
|------|------|
| `J04` + 餐單相 | 自動認枱號、認菜式，更新模板並上傳 GitHub |
| `J04` + 食物相 | 認食物，標記已上，更新模板並上傳 GitHub |
| `J04: clear` | 清空模板，上傳 GitHub |

> 更新模板後，用戶按 HTML「🗑️ 重置」按鈕就會載入最新模板。

---

## ⚙️ ADMIN 使用說明

### 首次部署
1. 將 `index.html` 同 `food-data.json` 上傳到 GitHub
2. 開啟 GitHub Pages（Settings → Pages → 選擇 main branch）
3. 訪問 `https://hkharrycheng.github.io/food-arrival/`

### 更新數據模板
1. 用戶喺對話度輸入 `J04` + 圖片
2. Agent 識別圖片內容，更新 `food-data.json`
3. Agent 上傳到 GitHub
4. 用戶按 HTML「重置」按鈕載入最新模板

### 清空模板
1. 用戶輸入 `J04: clear`
2. Agent 將 `food-data.json` 重置為空清單
3. 上傳到 GitHub

### 版本更新
1. 修改本地 Onedrive 目錄入面嘅文件
2. 用戶測試滿意後講「OK」
3. Agent 上傳到 GitHub
4. 更新 README.md 嘅 Version Control 部分
5. 版本號 +0.01
6. 備份到飛書雲端

---

## 🔄 Logic Flow

```
用戶打開 HTML
    ↓
檢查 localStorage
    ├─ 有數據 → 載入 localStorage
    └─ 冇數據 → 讀取 food-data.json → 存入 localStorage
    ↓
顯示清單 + 統計
    ↓
用戶操作：
    ├─ 點擊食物 → 切換狀態 → 更新 localStorage
    ├─ 新增食物 → 加入清單 → 更新 localStorage
    ├─ 刪除食物 → 移除項目 → 更新 localStorage
    ├─ 改枱號/落單時間 → 更新 localStorage
    └─ 按重置 → 清除 localStorage → 重新讀取 food-data.json
    ↓
每秒更新：現在時間 + 已用餐時間
每30秒更新：等候時間
```

---

## 💼 Business Flow

### 場景 1：用餐開始
1. 用戶到餐廳，打開 HTML
2. 設定枱號同落單時間
3. 影餐單相，輸入 `J04`
4. Agent 認菜式，更新模板
5. 用戶按重置，載入餐單
6. 開始用餐

### 場景 2：食物送上
1. 食物送到枱
2. 用戶影食物相，輸入 `J04`
3. Agent 認食物，標記已上
4. 更新模板並上傳 GitHub
5. 用戶按重置，見到最新狀態

### 場景 3：用餐完結
1. 所有食物已上
2. 用戶可手動記錄總消費
3. 輸入 `J04: clear` 清空模板，準備下次使用

---

## 📊 JSON Structure

### food-data.json（模板數據）

```json
{
  "items": [
    {
      "id": 1,
      "name": "炙燒黑椒蠔",
      "price": 10,
      "status": "waiting",
      "servedTime": null
    }
  ],
  "tableNo": "31A",
  "orderTime": "18:28"
}
```

### 欄位說明

| 欄位 | 類型 | 說明 |
|------|------|------|
| `items` | Array | 食物項目列表 |
| `items[].id` | Number | 唯一識別碼 |
| `items[].name` | String | 食物名稱 |
| `items[].price` | Number | 價錢（HKD） |
| `items[].status` | String | 狀態：`waiting`（未上）/ `served`（已上） |
| `items[].servedTime` | String/null | 上菜時間（ISO 格式），未上為 null |
| `tableNo` | String | 枱號 |
| `orderTime` | String | 落單時間（HH:MM） |

### localStorage（用戶數據）

- **Key**：`foodArrivalData`
- **結構**：同 food-data.json 一樣
- **用途**：儲存用戶實際操作，唔會影響模板

---

## 🏗️ 數據架構

採用「JSON 模板 + localStorage」雙層架構：

| 層級 | 儲存位置 | 用途 | 多用戶影響 |
|------|---------|------|-----------|
| 模板層 | food-data.json（GitHub） | 預設數據，Agent 更新 | 只影響新用戶第一次打開 |
| 用戶層 | 瀏覽器 localStorage | 實際操作數據 | 完全隔離，每人有自己嘅數據 |

### 運作邏輯
1. 第一次打開：讀取 food-data.json → 複製到 localStorage
2. 日常操作：全部改 localStorage，唔碰 JSON 檔案
3. 重置按鈕：清除 localStorage → 重新讀取 food-data.json
4. 多用戶隔離：每人有自己嘅 localStorage，互不影響
5. file:// 本地打開：用 HTML 內置預設數據（CORS 限制）
6. http/https（GitHub Pages）：優先讀取外部 food-data.json

---

## 🔒 安全原則

- ❌ **唔會**喺 HTML 加按鈕直接改 GitHub
- ✅ 所有 GitHub 修改都經 Agent 執行
- ✅ Personal Access Token 存在飛書常數設定表，唔暴露喺前端
- ✅ 手機用戶只需影相 + 打「J04」，Agent 自動更新模板

---

## 📝 Version Control

| 版本 | 日期 | 改動 |
|------|------|------|
| v1.00 | 2026-10-08 | 初始版本：<br>- 基本清單功能（新增/刪除/切換狀態）<br>- 自動計算等候時間<br>- 枱號同落單時間設定<br>- JSON 模板 + localStorage 雙層架構<br>- Agent 助手功能（J04 + 圖片）<br>- 現在時間同已用餐時間計時器<br>- 深色主題，手機友善 |

---

## 🛠️ 技術棧

- **前端**：純 HTML + CSS + JavaScript（唔使框架）
- **數據儲存**：瀏覽器 localStorage
- **模板數據**：JSON 檔案
- **部署**：GitHub Pages
- **Agent 整合**：豆包 Agent（圖片識別 + GitHub API）

---

## 📞 聯絡

如有問題或建議，請聯絡 Harry。

---

**HIS Tools © 2026**
