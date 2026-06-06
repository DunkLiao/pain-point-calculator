# 痛點計算機

一個可離線執行的單頁式流程痛點與自動化 ROI 估算工具，用來快速盤點重複性流程每年消耗的時間、人力成本，以及導入 RPA 或自動化後可能回收的效益。

## 功能

- 輸入流程名稱、每日耗時、作業人數與年執行日，估算每年可回收的總分鐘數與總小時數。
- 輸入人員時薪與 RPA 年度總擁有成本，計算節省人力成本、預估淨效益與釋放 FTE。
- 記錄 5 項最想改善或自動化的流程環節。
- 支援深色與淺色模式切換。
- 可將分析內容匯出為 `.txt` 報告。

## 使用方式

這是純前端靜態頁面，不需要安裝後端、建置工具或網路連線。

1. 直接用瀏覽器開啟 `index.html`。
2. 填寫流程參數：
   - 每天耗時：單一流程每天花費的分鐘數。
   - 作業人數：參與此流程的人數。
   - 年執行日：一年中執行此流程的天數。
3. 填寫 ROI 參數：
   - 人員單位成本：每小時人力成本，單位為新台幣。
   - RPA 總擁有成本：一年導入、維護與營運自動化方案的總成本。
4. 填寫改善項目日誌。
5. 點擊「匯出分析報告」下載 TXT 報告。

## 計算公式

```text
總分鐘數 = 每天耗時 x 作業人數 x 年執行日
總小時數 = 總分鐘數 / 60
節省人力成本 = 總小時數 x 人員單位成本
預估淨效益 = 節省人力成本 - RPA 總擁有成本
釋放全職人力 = 總小時數 / 2000
```

其中 FTE 以每人每年 2000 工時計算。

## 專案結構

```text
.
├── .assetsignore  # Cloudflare Worker 靜態資產上傳排除規則
├── .github/
│   └── workflows/
│       └── deploy-cloudflare-worker.yml # GitHub Actions 自動部署到 Cloudflare Worker
├── index.html   # 主頁面、樣式與互動邏輯
├── README.md    # 專案說明文件
└── wrangler.toml # Cloudflare Worker 部署設定
```

## 技術

- HTML
- 內嵌 CSS
- Vanilla JavaScript

## 自動部署到 Cloudflare Worker

此專案透過 GitHub Actions 在 `main` 分支有 push 時，自動將 `index.html` 部署到 Cloudflare Worker 靜態資產。

部署前請先完成以下設定：

1. 在 Cloudflare Workers & Pages 建立或保留 Worker，名稱使用 `pain-point-calculator`。
2. 在 Cloudflare 建立 API Token，權限至少需要可編輯 Workers Scripts。
3. 在 GitHub repository 的 `Settings > Secrets and variables > Actions` 新增以下 repository secrets：
   - `CLOUDFLARE_ACCOUNT_ID`：Cloudflare account ID。
   - `CLOUDFLARE_API_TOKEN`：Cloudflare API token。

完成後，推送到 `main` 分支會觸發 `.github/workflows/deploy-cloudflare-worker.yml`，並執行：

```text
wrangler deploy
```

`.assetsignore` 會限制 Worker 靜態資產只上傳 `index.html`，避免把 `.git`、GitHub Actions 設定或文件當成公開網站檔案。

## 注意事項

- 所有樣式與互動邏輯都已放在 `index.html` 內，可直接複製單一檔案到其他電腦使用。
- 匯出的報告會根據目前畫面上的輸入與計算結果產生。
- 估算結果僅作為流程盤點與自動化投資評估的初步參考，實際效益仍需依流程穩定度、錯誤率、維護成本與導入範圍進一步驗證。
