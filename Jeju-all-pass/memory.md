# Jeju All-Pass 旅遊規劃 App — 專案記錄

最後更新：2026-09-10
旅遊日期：2026-11-06（五）～ 2026-11-11（三），自駕
航班：11/6 台北 07:50 → 濟州 10:45；11/11 濟州 10:10 → 台北 11:25

## 網址與檔案

| 用途 | 位置 |
|---|---|
| 正式版（即時同步，主要使用） | https://lunar-box-480604-h7.web.app |
| GitHub Pages 版（同一份程式，需手動上傳 index.html） | https://candicechang123.github.io/jeju-allpass-map/ |
| Claude Artifact 版（僅本機儲存，無雲端同步） | https://claude.ai/code/artifact/d8d31406-0fc4-49c1-9843-93e4d2c7e006 |
| 本機專案資料夾 | `C:\Users\Candice\Desktop\Jeju-all-pass\` |
| 部署指令（在專案資料夾執行） | `firebase deploy --only hosting` |
| Firebase 專案 | `lunar-box-480604-h7`（My First Project），Realtime Database（us-central1） |
| 景點 Excel | `Jeju-All-Pass-景點資料.xlsx`（79 個 Pass 景點） |

資料夾內容：`index.html`（完整可部署版）、`public/index.html`（Firebase Hosting 用，與 index.html 相同）、`jeju-allpass-map.html`（Artifact 用，無 doctype/head）、`firebase.json`、`database.rules.json`、`.firebaserc`、`memory.md`（本檔）。

更新流程：Claude 改好 → 覆蓋 `index.html` 與 `public/index.html` → `firebase deploy --only hosting` → GitHub 另外手動上傳 `index.html`。

## 需求與完成狀態

### 資料
- [x] 抓取 Wokard Jeju All-Pass 全部景點（API `voucher-api.wokard.com/api/user/poi/list?page=N&size=30`，header `lang: ko|en|zh-CN`；共 79 筆）
  - 2026-09-10 補齊最後 3 筆（官方座標是佔位值 1,1／123,456）：#77 도치돌목장、#78 선물고팡 機場店、#79 송당승마장；座標取自 Naver 店家頁（m.place.naver.com/place/<id>）
  - 79 筆已與官方名單完全對齊；若官方新增，重跑上述 API 比對 naverMapUrl 即可
- [x] 中／韓／英三語名稱、優惠內容、營業時間、電話、Naver / Google Map 連結
- [x] 匯出 Excel（含類型色標、可點連結）
- [x] 匯入使用者 Naver 收藏清單（https://naver.me/Fet6PJvb，資料夾「jeju」131 筆）
  - 113 筆新增為「非Pass」景點；18 筆與 Pass 重複者不重建，但 Naver 備註顯示在 Pass 景點的 📝
  - 113 筆已全部改為好懂的中文名＋括號說明（例：뽈살집 本店（黑豬特殊部位烤肉））
  - 注意：Naver 清單是寫死在程式裡，收藏有新增需請 Claude 重抓

### 地圖與景點
- [x] Leaflet + OpenStreetMap 互動地圖，標記顏色：紅＝必去、綠＝免費、橘＝折扣、紫＝非Pass、灰＝機場；針內圖示表類別（見「地圖圖標」）
- [x] 篩選：全部／必去／免費／折扣／非Pass／🗑 已刪除
- [x] 搜尋（中／韓／英／地址）
- [x] 必去 ★ 可自由加入／移出；移出後自動回原本免費／折扣分類；「重設必去」還原預設
- [x] 自訂景點（非Pass）：名稱、地址、營業時間、停留分鐘、備註、Naver 連結或搜尋關鍵字、座標（地圖點選或貼座標）
- [x] 刪除任何景點（機場除外），會一併從行程移除；「🗑 已刪除」可單筆或全部還原
- [x] Naver 按鈕：一律新分頁開網頁版；手機另有「App」鈕直接開 Naver Map App（Android 用 intent，未安裝導向 Play）
  - App 鈕優先順序（2026-09-10 修正）：店家 ID（Pass 74 筆已解析 naver.me → `nid`；Naver 收藏用連結內 ID；自訂景點貼 map.naver.com/p/entry/place/<id> 連結也會抓 ID）→ 明確搜尋字串 `nvq`（5 筆地址型連結已填地址）→ 有連結但無 ID 時直接開該連結（不再用店名搜尋）→ 最後才用名稱搜尋
  - 自訂景點若貼 naver.me 短網址，瀏覽器無法解析出 ID，App 鈕會直接開短網址（Naver 頁會提示開 App）；建議貼完整 map.naver.com 連結或改用 Kakao 搜尋加入

### 行程規劃
- [x] 預設 Day 1–6（11/6–11/11），Day 1 出發 11:30、Day 6 出發 07:00，備註已填航班；可改日期／時間／備註、新增／刪除天（刪除：目前選中的 Day 分頁右上 ✕，或展開設定裡的「刪除這天」；至少保留一天）
- [x] 加入行程：卡片「＋」、popup「＋加入行程」、行程頁「輸入景點名稱直接加入」（找不到可直接建自訂景點）
- [x] 排序：拖曳（電腦）／▲▼（手機）；✕ 移除；清空當天
- [x] 整批交換兩天的行程（2026-09-10）：展開日期設定 →「⇄ 與其他天交換行程」→ 選目標天；只交換景點清單，日期／出發時間／備註留在原本的日子
- [x] 時間自動計算：出發時間 → 車程 → 抵達／離開，停留分鐘可改
- [x] 車程：優先用 OSRM 實際路網開車時間（免費、無金鑰，自動快取），未取得時用直線 ×1.3、40km/h 估算；可手動覆寫、可還原
- [x] 警示：⚠ 抵達時間不在營業時間、⏱ 兩個 Pass 景點間隔 <60 分（Pass 規則）
- [x] 地圖畫出當日路線（有實際路網時畫真實道路，否則虛線），標記帶順序編號；濟州國際機場內建可當起／終點
- [x] 日期／出發時間／備註區塊可摺疊，預設收起，選擇會記住
- [x] 地圖右上「📅 只看 Day N／🗺 顯示全部」切換（2026-09-10）：只在行程分頁出現，開啟時地圖只留當天行程的標記與路線，切天自動跟隨；按鈕文字描述「按下去會做的事」（未開：📅 只看 Day N 行程／已開：🗺 顯示全部景點，藍底）；記在 localStorage `jeju-ap-dayonly`
- [x] Pass 有效期（2026-09-10）：行程頁上方選 24/48/72h；啟用時間「自動＝第一個 Pass 景點抵達時」或手動指定；每個 Pass 景點標示剩餘時數／⏰ 已到期；當日狀態列：到期時間、超時景點數、行程結束時剩多少小時（隔天可否續用）
- [x] 多張 Pass：「＋加購一張 Pass」，第 N 張自動從前一張到期後的第一個 Pass 景點啟用（也可手動），景點標示屬於 Pass 1／Pass 2；存於 `state.passes[]`
- [x] Pass 列可摺疊（2026-09-10）：預設收起只顯示一行摘要（張數／時數／到期時間）＋精簡狀態列，點擊展開設定；選擇記在 localStorage jeju-ap-passopen
- [x] 重複排入提示（2026-09-10）：加入時若景點已在其他天／同一天會跳確認（可仍加入）；行程項目標「🔁 也排在 Day N」「🔁 今天重複 N 次」；當日設定區顯示重複景點數；機場與圖示類別為「住宿」的景點不列入重複判斷

### 共同編輯與同步
- [x] Firebase Realtime Database 即時同步：網址 `#t=行程代碼`，同一連結所有裝置／朋友看到同一份，幾秒內同步
- [x] 「分享 / 匯入」：複製共同編輯連結；開新的空白行程；離線備份用分享碼（匯出／匯入）
- [x] 同步燈號：綠已同步／黃同步中／灰本機儲存
- [x] 資料庫規則：只允許讀寫 `trips/<代碼>`，其他路徑拒絕；知道連結即可編輯（不需登入的取捨）
- [x] 本機 localStorage 備援；舊版必去清單自動轉移

### 手機版
- [x] 地圖全螢幕＋底部抽屜（三段高度，拉橫桿或點擊切換）
- [x] 點卡片自動縮抽屜、地圖飛到景點且 popup 落在可視區
- [x] 精簡 header、篩選列橫向滑動、按鈕加大、彈窗由底部滑出
- [x] 視窗邊界修正（2026-09-10）：body 改 position:fixed 並用 visualViewport 高度即時設定（處理手機網址列／鍵盤造成的 100vh 偏差與頁面超出邊框）；手機輸入框字級 16px 避免 iOS 自動縮放

### GitHub 推送
- 本機 Git 登入帳號是 Daypet123，對 candicechang123/jeju-allpass-map 無寫入權限（403）；已請使用者在 repo Settings → Collaborators 加入 Daypet123（Write）。本機 clone 在 `%TEMP%\claude\ghrepo`，commit 已備好，權限開通後 `git push origin main` 即可

### Kakao 店名搜尋與地址定位（2026-09-09 完成）
- [x] Kakao Developers App `jeju-allpass`（ID 1571272），JavaScript 키已放在網頁；已登記網域：lunar-box-480604-h7.web.app、candicechang123.github.io、localhost:3456（新版主控台位置：平台金鑰 → JS 密鑰 → JavaScript SDK 網域）
- [x] 景點分頁搜尋框輸入 ≥2 字 → 「在 Kakao 地圖搜尋」（或按 Enter）→ 結果一鍵「＋加入景點」或直接排進 Day N；結果限濟州範圍、關鍵字自動補「제주」
- [x] 自訂景點表單「🔎 自動定位」：地址→座標（Kakao Geocoder），失敗改用關鍵字搜尋
- [x] 行程頁快速輸入找不到時可轉 Kakao 搜尋
- [x] Kakao 帶入的景點有「K Kakao」連結鈕（自訂欄位 ku / kid）

### 景點編輯（2026-09-09 完成）
- [x] 所有景點（Pass／Naver／自訂，機場除外）都可編輯：卡片與 popup 的「✎ 編輯」、行程項目的 ✎
- [x] 可改：名稱、優惠／說明、地址、Naver 連結、營業時間、預設停留、備註、電話、座標（自動定位／地圖點選）
- [x] Pass／Naver 的修改以「覆寫」存在 `state.edits[key]`，不動原始資料；卡片顯示「已編輯」標籤；表單內「還原原始資料」可撤銷
- [x] 編輯不會影響行程（行程只存 key），並隨雲端同步

### 地圖圖標（2026-09-09 完成，選 C 圖示水滴針）
- [x] 水滴針內含類別圖示：餐廳／咖啡甜點／景點自然／購物／體驗樂園／住宿／機場／一般點；顏色仍表 Pass 類型
- [x] 類別由名稱、優惠、Naver/Kakao 分類關鍵字自動判斷；編輯表單「地圖圖示類別」可手動指定（存於 `type`）
- [x] 行程順序編號保留（右上黑色小標）
- 5 種樣式預覽檔：`icon-preview.html`

## 尚未執行／待使用者提供
- [ ] GitHub Pages 版需手動上傳最新 `index.html`（每次更新後）
- [ ] Naver 店名搜尋／底圖／Directions：需 Naver Cloud Platform 帳號，全球版只收企業、韓國版需韓國手機認證 → 台灣個人無法申請，已用 Kakao（搜尋、定位）＋ OSRM（車程）取代

## 暫緩／不做
- Claude Artifact 版無法雲端同步、無法呼叫 OSRM（Artifact 安全政策阻擋外部連線）→ 以 Firebase 網址為主
- Firestore 未啟用（改用 Realtime Database，更適合單一 JSON 行程）
- 登入／權限控管：目前不需登入，知道連結即可編輯；若之後要限制再加 Firebase Auth
- Wokard API 的優惠說明各語言內容相同，中文為人工翻譯

## 技術備忘
- 單一 HTML 檔，無 build；Leaflet 1.9.4（cdnjs）、Firebase compat 10.14.1（gstatic）、Kakao Maps SDK（dapi.kakao.com，libraries=services）、Noto Sans TC/KR；Artifact 版因 CSP 只會有 Leaflet
- 資料結構：`SPOTS`（79 Pass）、`NAVER`（113）、`AIRPORT`；狀態 `state={stars,custom,hidden,edits{key:{zh,addr,h,note,tel,bz,nv,nvq,lat,lon,stay}},days[{date,start,note,items[{key,stay,travel}]}],passes[{hours,mode:'auto'|'manual',start}]}`
- 景點 key：Pass 為數字 id；自訂 `c<timestamp>`；Naver `n<bookmarkId>`；機場 `'air'`
- 雲端路徑 `trips/<tripId> = {state, stamp, by}`；最後寫入者勝出；自己的寫入以 `by` 過濾
- OSRM 快取存 localStorage `jeju-ap-routes`；路線幾何僅存記憶體
- Naver 收藏 API：`https://pages.map.naver.com/save-pages/api/maps-bookmark/v3/shares/<shareId>/bookmarks?start=0&limit=300`（Accept-Language 決定名稱語言）
