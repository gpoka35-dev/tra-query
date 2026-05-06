# 台鐵時刻查詢 APP — 部署與打包說明

## 📦 檔案內容
- `index.html` — 主畫面 + 全部邏輯
- `manifest.json` — PWA 設定檔
- `sw.js` — Service Worker(離線快取)
- `icon-192.png` / `icon-512.png` / `icon-maskable-512.png` — APP 圖示

---

## 🚀 三步驟快速部署(總時間約 15 分鐘)

### 步驟 1:設定 GitHub Pages(免費寄存)

1. 註冊 GitHub 帳號(若沒有):https://github.com/signup
2. 點右上 `+` → `New repository`
3. Repository name 填:`tra-query`
4. 選 **Public**(免費版必須是 public)
5. 勾選 `Add a README file`,點 `Create repository`
6. 進入 repo → 點 `Add file` → `Upload files`
7. 把這個資料夾裡**全部 6 個檔案**拖進去上傳
8. 滑到底點 `Commit changes`
9. 點上方 `Settings` → 左側 `Pages`
10. Source 選 `Deploy from a branch` → Branch 選 `main` / `/ (root)` → 按 `Save`
11. 等約 1-2 分鐘,頁面會出現你的網址,類似:
    ```
    https://你的帳號.github.io/tra-query/
    ```
12. 用手機或電腦瀏覽器打開測試 ✓

---

### 步驟 2:設定 TDX API 金鑰

1. 開啟你部署好的網址
2. 點右上角的 ⚙ 齒輪圖示
3. 填入你的 **Client ID** 和 **Client Secret**
4. 點「儲存並測試」
5. 看到 ✓ 連線成功 → 就可以查真實班次了

> 💡 提示:金鑰只儲存在「你自己的瀏覽器/裝置」裡的 localStorage,不會上傳到任何地方。如果別人下載你的 APK,他們需要自己填入金鑰,你的金鑰不會外洩。

---

### 步驟 3:打包成 APK(用 PWABuilder 線上服務)

1. 開啟 **https://www.pwabuilder.com**
2. 在大輸入框貼上你的 GitHub Pages 網址(例:`https://你的帳號.github.io/tra-query/`)
3. 點 `Start`
4. PWABuilder 會分析你的 PWA,顯示三個項目都打勾:
   - ✓ Manifest
   - ✓ Service Worker
   - ✓ HTTPS
5. 點右上 `Package For Stores`
6. 找到 **Android** 區塊 → 點 `Generate Package`
7. 選項:
   - **Package ID**: `tw.taiwanrail.tra.query`(或自取一個獨特的 ID)
   - **App name**: `台鐵時刻查詢`
   - **Launcher name**: `台鐵查詢`
   - **Signing key**: 選 `New`(讓系統幫你產生簽署金鑰)
   - 其他保持預設
8. 點 `Download`
9. 解壓縮下載的 zip,裡面有兩個檔案:
   - `app-release-signed.apk` ← **這個就是你要的 APK**
   - `app-release-bundle.aab`(上架 Play Store 才需要)
10. 把 `app-release-signed.apk` 傳到 Android 手機 → 開啟 → 安裝
11. 桌面就會多出「台鐵查詢」圖示,雙擊打開即可

> ⚠️ 安裝時若 Android 提示「不明來源」,允許一次即可(因為 APK 沒上架 Play Store)

---

## 🔧 替代方案:不打包 APK,直接「安裝為 APP」

如果你只想在手機上用,**完全不需要打包 APK**:

1. 用 Android 手機的 **Chrome** 開啟 GitHub Pages 網址
2. 點 Chrome 選單(右上 ⋮)
3. 點 **「安裝應用程式」** 或「加到主畫面」
4. 桌面立刻出現圖示 — 雙擊即開,跟 APK 體驗 100% 一樣
5. 沒網路時也能開啟介面(只有查詢才需要網路)

**iPhone 也能用**:Safari 開啟 → 分享按鈕 → 加到主畫面。

---

## ❓ 常見問題

**Q: APK 安裝後打開卻有瀏覽器網址列?**
A: 這是 TWA 的 Digital Asset Links 沒設定。解法:把 PWABuilder 下載 zip 內的 `assetlinks.json` 上傳到你 GitHub repo 根目錄的 `.well-known/` 資料夾下。

**Q: 沒網路時可以查詢嗎?**
A: 介面可以開,但**查詢需要連網**(向 TDX 抓資料)。如果之前查過同一條路線,瀏覽器可能還快取著資料。

**Q: 我可以把 APK 給家人朋友嗎?**
A: 可以,但他們需要自己填入 TDX API 金鑰(他們也要去申請),或者你可以把金鑰直接寫在 `index.html` 裡(找 `// First-run hint` 上方加幾行 setCredentials 程式碼,但有外洩風險)。

**Q: 如何更新 APP?**
A: 改 `index.html` 後重新上傳到 GitHub。已安裝的 APK/PWA 下次開啟會自動載入新版。

**Q: TDX API 配額限制?**
A: 每秒 50 次,個人使用完全用不完。

---

## 📝 對應的 TDX API 文件
- 認證:`POST https://tdx.transportdata.tw/auth/realms/TDXConnect/protocol/openid-connect/token`
- 起訖站時刻:`GET https://tdx.transportdata.tw/api/basic/v3/Rail/TRA/DailyTrainTimetable/OD/{起站ID}/to/{訖站ID}/{YYYY-MM-DD}`
- 文件首頁:https://tdx.transportdata.tw

製作日期:2026/05/06
