# 🚀 欣的記帳 — 部署說明書

## 📁 專案檔案結構

```
xin-wallet/
├── index.html       ← 主程式（單檔包含所有功能）
├── manifest.json    ← PWA 設定
├── sw.js            ← 離線快取 Service Worker
├── icon-192.png     ← App 圖示（需自行製作）
├── icon-512.png     ← App 圖示（需自行製作）
└── README.md        ← 本說明
```

---

## 🔧 步驟一：製作 App 圖示

需要兩張 PNG 圖示（背景 #1a1a2e 深藍，💰 emoji 居中）：
- `icon-192.png`（192×192 px）
- `icon-512.png`（512×512 px）

**快速製作方式**：
1. 開 [Favicon.io](https://favicon.io/emoji-favicons/) → 搜尋 💰
2. 下載後 resize 為 192 & 512

---

## ☁️ 步驟二：部署到 GitHub Pages（免費）

### 1. 建立 GitHub 帳號
前往 https://github.com 註冊

### 2. 新建 Repository
- Repository 名稱：`xin-wallet`
- 設定為 **Public**
- 點 「Create repository」

### 3. 上傳檔案
```bash
# 如果你有裝 Git：
git init
git add .
git commit -m "init"
git remote add origin https://github.com/你的帳號/xin-wallet.git
git push -u origin main
```

或直接在 GitHub 網頁點「Add file → Upload files」上傳所有檔案。

### 4. 開啟 GitHub Pages
- 進入 Repository → Settings → Pages
- Source 選「Deploy from a branch」
- Branch 選「main」，資料夾選「/ (root)」
- 點 Save

約 1 分鐘後，你的 App 網址就是：
```
https://你的帳號.github.io/xin-wallet/
```

---

## 🤖 步驟三：設定 Claude AI（語音＆截圖辨識）

App 的語音解析和截圖辨識功能需要 Anthropic API Key。

### 取得 API Key
1. 前往 https://console.anthropic.com
2. 登入 → API Keys → Create Key
3. 複製 Key（格式：`sk-ant-api03-...`）

### 設定到 App
打開 `index.html`，找到這段：

```html
<!-- 在 HTML <head> 加入這行 -->
```

在 `<head>` 內加入（緊接在 `</style>` 後面）：

```html
<script>
window.ANTHROPIC_API_KEY = 'sk-ant-api03-你的key';
</script>
```

然後在 API 呼叫的 fetch headers 加入：
```javascript
headers: {
  'Content-Type': 'application/json',
  'x-api-key': window.ANTHROPIC_API_KEY,
  'anthropic-version': '2023-06-01',
  'anthropic-dangerous-direct-browser-iab': 'true'
}
```

> ⚠️ 安全提醒：API Key 放在前端代表任何人都看得到。
> 建議日後改成後端 proxy（免費可用 Cloudflare Worker）。

---

## 📱 步驟四：安裝到手機

### iPhone (iOS)
1. 用 **Safari** 開啟 App 網址
2. 點底部 **分享按鈕** (□↑)
3. 往下滑找「**加入主畫面**」→ 點
4. 名稱改成「欣的記帳」→ 點「**新增**」
5. 桌面就會出現 App 圖示 🎉

### Android
1. 用 **Chrome** 開啟 App 網址
2. 點右上角 **⋮ 選單**
3. 點「**安裝應用程式**」或「**新增至主畫面**」
4. 完成！

---

## 🔐 步驟五：設定你的 PIN 碼

預設 PIN 碼是 `1234`，請修改：

在 `index.html` 找到：
```javascript
const STORED_PIN = localStorage.getItem('appPin') || '1234';
```

改成你想要的碼，或在 App 第一次登入後，在 localStorage 設定：
```javascript
localStorage.setItem('appPin', '你的PIN碼');
```

---

## 📊 步驟六：Google Sheets 同步（進階）

目前資料存在手機本機（localStorage），若需要同步至 Google Sheets：

1. 前往 [Google Cloud Console](https://console.cloud.google.com)
2. 建立新專案 → 啟用「Google Sheets API」
3. 建立 OAuth 2.0 憑證（Web Application）
4. 將 `YOUR_GOOGLE_CLIENT_ID` 和 `YOUR_GOOGLE_API_KEY` 填入 `index.html` 頂部的 `GOOGLE_CONFIG`
5. 加入你的 GitHub Pages 網址到「已授權的 JavaScript 來源」

---

## 📋 功能清單

| 功能 | 狀態 |
|------|------|
| ✅ PIN 碼登入 | 完成 |
| ✅ 手動記帳（金額/項目/類別/方式/日期）| 完成 |
| ✅ 語音輸入（Web Speech API + Claude AI解析）| 完成 |
| ✅ 截圖辨識（Claude Vision API）| 完成 |
| ✅ 統計圖表（圓餅圖/月趨勢）| 完成 |
| ✅ 資產管理（現金/信用卡/LINE Pay/街口等）| 完成 |
| ✅ 股票紀錄（台股/美股/股息/拆股）| 完成 |
| ✅ 房屋費用（水電瓦斯管理費停車費）| 完成 |
| ✅ PWA 安裝（manifest + Service Worker）| 完成 |
| ✅ 離線可用（Service Worker 快取）| 完成 |
| 🔧 Google Sheets 同步 | 需設定 OAuth |

---

## 🆘 常見問題

**Q：語音輸入沒反應？**
→ 確認瀏覽器有麥克風權限。iOS 必須用 Safari，不支援 Chrome。

**Q：截圖辨識失敗？**
→ 確認已設定 Anthropic API Key（見步驟三）。

**Q：安裝後打開是空白頁？**
→ 清除瀏覽器快取後重試。確認所有檔案都上傳到 GitHub。

**Q：資料不見了？**
→ 資料存在瀏覽器 localStorage，換瀏覽器或清除資料就會消失。
→ 建議定期用 App 內的匯出功能備份（後續版本功能）。
