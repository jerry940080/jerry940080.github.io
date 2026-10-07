# CLAUDE.md — 旅のしおり 首頁（jerry940080.github.io）

> 使用者以繁體中文溝通，回覆請一律使用繁體中文。

## 這個 repo 是什麼
`https://jerry940080.github.io/` 的首頁：把使用者所有行程網站集中在一頁（版面 B：左邊路線地圖、右邊依年份的時間軸卡片）。
每個行程仍是**各自獨立的 repo**（從 `trip-template` 開出來，用 trip-guide skill 做），這裡只放入口，不放行程內容。

## 檔案
- `index.html`：整個首頁（CSS／JS 內嵌，不用框架）。
- `land.js`：地圖海岸線 `const LAND`（Natural Earth 10m，範圍東經 117–147、北緯 20–46.5，涵蓋日本、台灣、韓國）。
  行程跑出這個範圍（例如歐洲）時，用 trip-guide skill 的 `scripts/make_geo.py --src world` 重產並放大範圍。

## 資料怎麼來（每次打開首頁時即時讀）
1. GitHub 公開 API `users/jerry940080/repos`：挑出有 GitHub Pages、或有 `trip` 標籤的 repo（排除本 repo 與 fork）。
2. 讀每個行程網站上的 `/<repo>/trip.json`（同網域，不受 API 次數限制）。讀得到就是一張卡；讀不到但有 `trip` 標籤的，顯示 repo 簡介＋「網站未上線」。
3. API 讀不到（離線、未登入每小時 60 次用完）→ 沿用 localStorage `hub-trips-v1` 快取；連快取都沒有才用程式裡的 `KNOWN` 清單直接讀 trip.json。
4. 狀態（還有 N 天／旅途中／已完成）依日本時間的今天當場算；網址加 `?today=2027-02-12` 可模擬。

## trip.json 格式（放在各行程網站的根目錄，也就是 Pages 發布的那個分支）
```json
{"v":1,"title":"熱海・伊豆・靜岡","sub":"一行副標","start":"2027-02-10","end":"2027-02-17","tentative":false,
 "route":["TPE","NRT"],"color":"#c67139","kanji":"熱海","cover":"cover.jpg",
 "places":[["成田",35.773,140.388],["品川",35.629,139.739]]}
```
- `places` 依行程順序，地圖照這個順序連線（去過畫實線、規劃中畫虛線）。
- `cover` 是相對於行程網站的路徑；空字串就用 `color` 色塊＋`kanji` 大字。`tentative:true` 加「日期暫定」標籤。

## 目前的行程（2026-10-07）
| repo | 發布分支 | 備註 |
|---|---|---|
| IzuAtami-trip | claude/read-izuatami-trip-37v8zi | 2027/2/10–17 熱海・伊豆・靜岡，封面 `cover.jpg` |
| 2025-osaka-kanazawa-miokokoen-nagano-tokyo | claude/friendly-cray-yhb83r | 2025/2/22–3/2 北陸・信州・東京（已完成） |
| Osaka_Kyoto_Kobe | claude/optimistic-meitner-x1spay | 2027/5/27–6/3（暫定日期） |
| TaiwanTrip2026 | claude/itinerary-url-template-e2g0to | 2026/9/26–10/5 台灣（已完成），封面 `assets/img/jiufen-1.jpg` |

## 注意
- **不要在這個 repo 裝 service worker**：它在網域根目錄，scope `/` 會攔到底下所有行程網站。
- 地圖：台灣的點自動放進小框（選四個角落裡沒壓到路線點的那個），其餘依所有點自動框範圍。
- 驗證：本機用一個資料夾把各行程 repo 以 symlink 掛成子目錄、`python3 -m http.server`，Playwright 用 `page.route` 假造 GitHub API 回應。
