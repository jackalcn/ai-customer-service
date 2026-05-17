# AI 智慧客服問答系統

## 1. 專案名稱
AI 智慧客服問答系統（Python + Streamlit + FAQ + AI API）

## 2. 專案簡介
本專案是一個可直接執行、可部署到 Streamlit Community Cloud 的企業客服聊天系統。使用者在網頁輸入問題後，系統會先查詢 `faq.json`，若 FAQ 相似度不足，再改由 AI API 產生客服回答。

系統回覆會固定呈現以下資訊：
- 問題分類
- 客服回覆
- 資料來源
- 是否建議轉人工客服

## 3. 系統功能
- 聊天式介面（保留歷史對話）
- FAQ 知識庫優先回答
- FAQ 相似度低於 0.6 時，改用 AI 回覆
- API Key 未設定時，自動退回 FAQ-only 模式
- AI API 失敗時顯示友善錯誤訊息
- 側邊欄顯示客服資訊與系統說明
- 常見問題快捷按鈕（營業時間、付款方式、退換貨、保固服務、物流配送、聯絡客服）
- 清除對話紀錄按鈕
- 回覆滿意度回饋（👍 / 👎）
- 問題分類功能（產品介紹、訂單查詢、付款方式、退換貨、保固服務、技術支援、聯絡客服、營業時間、發票問題、物流配送、其他問題）

## 4. 專案檔案結構
```text
.
├─ .streamlit/
│  ├─ config.toml
│  └─ secrets.toml.example
├─ .gitignore
├─ app.py
├─ faq.json
├─ requirements.txt
├─ runtime.txt
├─ .env.example
└─ README.md
```

## 5. 安裝方式
1. 建議使用 Python 3.10 以上版本。
2. 在專案資料夾開啟終端機並執行：

```bash
pip install -r requirements.txt
```

## 6. API Key 設定方式（本機）
1. 將 `.env.example` 複製成 `.env`。
2. 在 `.env` 填入您的 API Key：

```env
OPENAI_API_KEY=你的 OpenAI API Key
OPENAI_MODEL=gpt-4o-mini
```

說明：
- 若未設定 `OPENAI_API_KEY`，系統不會當掉，會自動使用 FAQ 模式。
- 部署到 Streamlit Cloud 時，請改用 Secrets 設定（見第 8 節）。

## 7. 執行方式
在專案目錄執行：

```bash
streamlit run app.py
```

若系統找不到 `streamlit` 指令，請改用：

```bash
python -m streamlit run app.py
```

## 8. 部署到 Streamlit Community Cloud
### 8.1 建立 GitHub Repo 並推送程式
在專案目錄執行：

```bash
git init
git add .
git commit -m "Initial Streamlit customer service app"
git branch -M main
git remote add origin <你的 GitHub Repo URL>
git push -u origin main
```

### 8.2 在 Streamlit Community Cloud 建立 App
1. 前往 Streamlit Community Cloud。
2. 點選 New app。
3. 選擇您的 GitHub Repo、分支 `main`、主程式 `app.py`。
4. 點選 Advanced settings，貼上 Secrets：

```toml
OPENAI_API_KEY = "你的 OpenAI API Key"
OPENAI_MODEL = "gpt-4o-mini"
```

5. 點選 Deploy，等待建置完成即可上線。

### 8.3 部署重點
- `app.py` 已支援優先讀取環境變數，若無則讀取 Streamlit Secrets。
- 若未設定 API Key，系統仍可用 FAQ 模式運作。

## 9. 如何修改 faq.json
`faq.json` 每筆資料格式如下：

```json
{
  "question": "使用者常見問題",
  "answer": "客服標準回答",
  "keywords": ["關鍵字1", "關鍵字2", "關鍵字3"],
  "category": "分類"
}
```

建議：
- 問題描述越貼近真實提問，FAQ 命中率越高。
- `keywords` 請填入常見同義詞與口語詞。
- `category` 盡量沿用既有分類名稱，方便統計與維護。

## 10. 如何測試系統
可依下列情境測試：
1. FAQ 命中測試：輸入「你們營業時間是幾點？」應優先回覆 FAQ 並顯示資料來源為 FAQ。
2. AI 回覆測試：輸入 FAQ 未收錄問題（例如較複雜情境題），應改用 AI 回覆。
3. API Key 缺少測試：移除 `.env` 或 Cloud Secrets 的 API Key，系統應顯示 FAQ-only 提示，不可崩潰。
4. 回饋測試：點選 👍 / 👎，應出現對應回饋訊息。
5. 清除紀錄測試：點選「清除對話紀錄」，聊天內容應被清空。
6. 案件驗證測試：確認畫面顯示案件編號、時間戳記、客服人員資訊。
7. 匯出測試：點選「下載對話紀錄（TXT）」可下載完整對話。

## 11. 常見錯誤排除
1. 找不到 `faq.json`
- 現象：畫面顯示 FAQ 檔案不存在。
- 處理：確認 `faq.json` 位於專案根目錄，檔名大小寫正確。

2. API Key 未設定
- 現象：顯示「目前尚未設定 AI API Key，因此只能使用 FAQ 知識庫回答。」
- 處理：本機請檢查 `.env`，Cloud 請檢查 App 的 Secrets 設定後重新部署。

3. AI 回覆失敗
- 現象：顯示 AI 暫時無法回覆的友善訊息。
- 處理：確認網路連線、API Key 是否有效、帳號額度是否正常。

4. `streamlit` 指令不可用
- 現象：終端機顯示找不到 `streamlit`。
- 處理：改用 `python -m streamlit run app.py`。

5. Cloud 部署失敗（Build Error）
- 現象：Streamlit Cloud 顯示套件安裝失敗或啟動錯誤。
- 處理：確認 `requirements.txt` 與 `runtime.txt` 已推送到 GitHub，並查看部署日誌定位錯誤。

## 12. 未來可升級功能
- 導入向量資料庫（如 FAISS）提升 FAQ 檢索精準度。
- 新增管理後台，讓非工程人員可直接維護 FAQ。
- 增加問題標籤統計與滿意度分析報表。
- 新增多語系客服（繁中、英文、日文）。
- 串接工單系統，自動建立人工客服案件。

---
如果您要擴充成可上線版本，建議再加入：登入權限、提問紀錄資料庫、API 速率限制與監控告警。
