# 🔍 自動化實習職缺監控系統

每天自動爬取 104 人力銀行的實習職缺，透過 Gemini AI 產生摘要，並以 Email 通知。

---

## 📌 專案說明

求職期間手動刷新職缺很費時，這個專案使用 N8N 建立自動化工作流，每天早上 9 點自動執行以下流程：

1. 呼叫 104 人力銀行 API 抓取指定地區與職類的最新實習職缺
2. 與 Google Sheets 的已通知紀錄比對，篩選出**新職缺**
3. 使用 Google Gemini AI 對每筆職缺產生繁體中文摘要
4. 彙整成一封 Email 寄送通知
5. 將已通知的職缺 ID 寫回 Google Sheets，避免重複推送

---

## 🛠️ 使用技術

| 工具 | 用途 |
|---|---|
| [N8N](https://n8n.io/) | 自動化工作流引擎 |
| 104 人力銀行 API | 職缺資料來源（非官方公開端點） |
| Google Gemini API | AI 職缺摘要（gemini-2.5-flash-lite） |
| Google Sheets | 儲存已通知職缺 ID，實現去重邏輯 |
| Gmail | Email 通知推送 |

> 本專案所有服務均使用**免費方案**，零成本運行。

---

## ⚙️ 工作流架構

```
Schedule Trigger (每天 09:00)
    ↓
HTTP Request → 104 職缺搜尋 API
    ↓
Code Node → 解析職缺清單與 Job ID
    ↓
Google Sheets (read) → 讀取已通知的職缺 ID
    ↓
Code Node → 比對並篩選新職缺
    ↓
IF Node → 有新職缺？
    ↓ Yes
HTTP Request → Gemini API 產生 AI 摘要
    ↓
Code Node → 合併所有職缺成單封郵件
    ↓
Gmail → 寄送 Email 通知
    ↓
Google Sheets (write) → 記錄已通知的職缺 ID
```

---

## 🚀 使用方式

### 事前準備

- [N8N](https://n8n.io/) 帳號（雲端或本機自架）
- [Google AI Studio](https://aistudio.google.com/app/apikey) Gemini API Key（免費）
- Google 帳號（用於 Sheets 與 Gmail）

### 設定步驟

1. 在 N8N 匯入 `workflow.json`
2. 建立一個 Google Sheets 試算表，Sheet1 的 A1 填入 `job_id`
3. 在 Gemini HTTP Request 節點的 URL 填入你的 API Key
4. 連結 Google Sheets 與 Gmail 的 OAuth 憑證
5. 修改 104 API URL 中的 `keyword` 與 `area` 參數（依需求調整）
6. 將 Workflow 設為 **Active**

### 自訂搜尋條件

修改第一個 HTTP Request 節點的 URL 參數：

| 參數 | 說明 | 範例 |
|---|---|---|
| `keyword` | 搜尋關鍵字 | `實習`、`軟體工程師` |
| `area` | 地區代碼 | `6001006000`（新竹市） |
| `jobcat` | 職務類別代碼 | `2007001000`（資訊軟體系統類） |

---

## 📁 專案結構

```
job-monitor-n8n/
├── README.md
├── workflow.json    # N8N workflow 匯出檔
└── screenshot.png   # 工作流截圖
```

---

## 📄 License

MIT
