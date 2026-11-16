# AGENTS.md — SOUND SHELF｜音樂收藏書架

> **給任何 AI 代理人（Claude、Codex、Cursor、Copilot…）：修改本專案前，必須先完整閱讀本文件。**
> 這是專案唯一的規則來源。即使沒有任何先前的對話紀錄，讀完本文件也應能理解並延續開發。

---

## 1. Project Overview
- **名稱**：SOUND SHELF｜音樂收藏書架　**標語**：Your music, your memories.
- **理念**：收藏喜歡的音樂，也收藏每首歌背後的回憶。
- **定位**：結合音樂收藏、心情歌單、聆聽日記、回憶卡與個人統計的互動式網站；韓系極簡＋音樂雜誌風格。
- **播放與版權界線（最重要）**：本站只允許兩種播放來源：
  1. **自有音檔**：放在 `assets/audio/` 的**原創／CC0／已取得授權**音檔（目前 12 首為程式合成的原創循環音樂），由 `audio` 欄位指向，用 HTML5 `<audio>` 播放。
  2. **官方內嵌播放器**：使用者貼上的 YouTube 或 Spotify 單曲連結（`listenUrl`），僅以官方 iframe 內嵌播放。
  **禁止**：加入未授權的音檔（含擷取、轉檔自串流平台的音檔）、提供音檔下載功能、自行抓取串流網址、嵌入上述兩個平台以外的播放來源、假裝虛構歌曲是真實歌曲。新增任何音檔都必須在 `assets/audio/README.md` 登記來源與授權。
- **預設資料**皆為虛構的歌曲與藝人，封面為原創 SVG、音檔為原創合成音樂，避免侵權。

## 2. Project Goals
1. 所有功能真實可操作，而非靜態畫面。
2. 規則清楚，讓之後的 AI 能沿用同一套 Schema、元件與流程新增歌曲與功能。
3. 資料安全：驗證輸入、處理錯誤與空狀態、localStorage 有版本與遷移機制。
4. 可直接部署到 GitHub Pages，無後端、無金鑰。

### 現有功能（不得被破壞）
| # | 功能 | 位置 |
|---|---|---|
| 1 | 收藏牆：卡片／清單切換 | `app.js` `renderSongs`、`index.html #view-shelf` |
| 2 | 分類 K-POP / R&B / Pop / Indie / Others | `data.js` `GENRES` |
| 3 | 搜尋（歌曲、歌手、專輯）＋類型篩選 | `app.js` `filterSongs` |
| 4 | 我的最愛（♡）與最愛清單 | `toggleFav`、`state.onlyFav` |
| 5 | 心情歌單 Happy/Chill/Sad/Energy/Love | `#view-mood`、`renderMood` |
| 6 | 聆聽日記（歌曲＋日期＋心情＋文字，可刪除） | `#view-diary`、`submitDiary` |
| 7 | 新增音樂（驗證、重複檢查、可刪除） | `submitSong`、`deleteSong` |
| 8 | 隨機推薦（從目前顯示的收藏中抽） | `randomPick` |
| 9 | Music Memory 回憶卡（Canvas 產圖、下載 PNG） | `memory.js` |
| 10 | 個人音樂統計 | `renderStats` |
| 11 | Light / Dark 主題（持久化） | `setTheme` |
| 12 | 站內播放：自有音檔 `<audio>`／YouTube、Spotify 官方內嵌 | `renderPlayer`、`toEmbed` |

## 3. Tech Stack
- 純 **HTML5 / CSS3 / JavaScript（ES2020）**；無框架、無 npm、無打包、無後端、無 API Key。
- 播放：HTML5 `<audio>`（自有音檔）＋官方 iframe 內嵌（YouTube 使用 `youtube-nocookie.com`、Spotify `open.spotify.com/embed`），皆無需 API Key。
- 字體：Google Fonts（Cormorant Garamond、Noto Sans TC），有系統字體 fallback。
- 儲存：`localStorage`，單一鍵 `soundshelf.state`（見 §9）。
- 回憶卡：原生 Canvas 2D 繪製，`canvas.toBlob` 下載 PNG。
- **本機執行**：在專案根目錄執行 `python3 -m http.server 8000`，開啟 http://localhost:8000。
  （請勿用 `file://` 測試回憶卡：瀏覽器會因跨來源限制改用預設封面。）

## 4. Project Structure
```
sound-shelf/
├── index.html        頁面骨架：導覽、5 個 view（shelf/mood/diary/memory/stats）、2 個 <dialog>
├── AGENTS.md         本文件
├── README.md         給人類的簡介與部署
├── css/style.css     唯一樣式檔，所有色彩/間距為 :root 變數
├── js/
│   ├── data.js       預設資料 + 常數（GENRES、MOODS、DEFAULT_SONGS）。只放資料，不放邏輯
│   ├── storage.js    使用者資料：讀寫、驗證、版本遷移、錯誤處理（SoundShelfStorage）
│   ├── memory.js     回憶卡 Canvas 繪製與下載（SoundShelfMemory）
│   └── app.js        UI 狀態、渲染、事件、路由（單一 IIFE）
├── assets/images/    封面 SVG 與 cover-default.svg
└── assets/audio/     原創合成 mp3 與 README.md（授權登記）
```
載入順序固定：`data.js → storage.js → memory.js → app.js`。調整說明：依需求拆出 storage.js/memory.js，讓資料與繪圖與 UI 邏輯分離，方便 AI 只改單一模組。

## 5. Design System
**風格**：韓系極簡、音樂雜誌、大量留白、細線、大型英文標題。非後台系統——要有層次（超大標題、封面主視覺、細線分隔、字距）。
**色彩（只用 CSS 變數，禁止硬寫新色碼）**
| 變數 | 淺色 | 深色 | 用途 |
|---|---|---|---|
| `--bg` | #f6f2ea 奶油白 | #19191a | 背景 |
| `--surface` | #fcfaf5 | #232325 | 卡片、對話框 |
| `--ink` | #262626 炭黑 | #ece8df | 文字、主按鈕 |
| `--mist` | #8b8c88 霧灰 | #a0a19c | 次要文字 |
| `--line` | #dcd7cc | #38383a | 細線 |
| `--accent` | #a89a85 | #c3b59f | 少量強調 |
警示色僅限 `--alert: #b4553f`（愛心、錯誤）。
**字體**：大標題/歌名 `--serif`；內文 `--sans`（字重 300–500）；標籤用大字距（.12–.4em）、小字級、大寫。
**間距**：只用 `--s1..--s5`。**圓角**：封面 2px；按鈕與標籤膠囊 99px。
**專輯卡片**：1:1 封面、細邊框、無厚重陰影；結構「封面（右上愛心）→ 類型·心情 → 歌名 → 歌手 · 專輯」。清單檢視為「小封面＋歌名/歌手/專輯＋類型＋愛心」一列。
**動畫**：僅淡入、上浮、hover 微上移與封面 1.03 倍、view 切換淡入、首頁黑膠緩慢旋轉。時間 .3–.8s。必須保留 `prefers-reduced-motion`。不得新增彈跳、視差、閃爍。
**RWD**：≤900px（平板：導覽縮小、2 欄）、≤560px（手機：導覽可橫向捲動、1–2 欄）。
**可用 class（新元件請先沿用）**：`.card` `.row` `.chip` `.btn` `.btn.ghost` `.link-btn` `.badge` `.panel` `.field` `.empty` `.bar` `.stat`。

## 6. Naming Conventions
- 檔名 kebab-case（`cover-default.svg`）。HTML/CSS class kebab-case；JS 變數/函式 camelCase，常數 UPPER_SNAKE_CASE。
- DOM id 以 `view-`（頁面）、`btn-`（按鈕）、`form-`、`dlg-` 為前綴。
- 資料欄位 camelCase（`listenUrl`）。id：預設歌曲 `ss-001` 流水號；使用者歌曲 `u-<Date.now()>`；日記 `d-<Date.now()>`。
- 類型與心情的**值**使用英文（`"K-POP"`、`"Chill"`），中文為顯示用標籤，放在 `MOODS` 的 `label`。

## 7. Music Data Schema
```js
{
  id: "ss-013",               // 唯一字串
  title: "歌曲名稱",           // ≤60 字，必填
  artist: "歌手",              // ≤40 字，必填
  album: "專輯名稱",           // ≤60 字，必填（單曲請填 "Single"）
  genre: "Indie",             // 只能是 GENRES：K-POP | R&B | Pop | Indie | Others
  mood: "Chill",              // 只能是 MOODS.value：Happy | Chill | Sad | Energy | Love
  cover: "assets/images/x.svg", // 相對路徑或 https 網址；無則用 cover-default.svg
  audio: "assets/audio/x.mp3", // 選填，自有（原創/CC0/授權）音檔，只能是 assets/audio/ 內相對路徑；預設歌曲專用
  listenUrl: ""               // 選填，https 連結；若為 YouTube/Spotify 單曲連結會在站內內嵌播放，其餘只顯示為外部連結
}
```
- 使用者**不能上傳音檔**，因此使用者歌曲不會有 `audio`，只能用 `listenUrl`。
- 使用者新增的歌曲額外含 `source: "user"` 與 `addedAt`（ISO 時間字串），**只存在 localStorage**。預設資料不要有 `source`。
- **聆聽日記（diary）**：`{ id, songId, songTitle, songArtist, cover, date:"YYYY-MM-DD", mood, text, createdAt }`；`text` 1–500 字。`songTitle/songArtist/cover` 為建立時的快照，歌曲被刪除後日記仍可顯示。
- 驗證一律走 `SoundShelfStorage.validateSong()`；不得在別處自創驗證規則。要新增欄位時，須同時更新：本節、`data.js`、`storage.js`（驗證與遷移）、新增表單、`renderSongs`、README。
- 新增預設歌曲：在 `DEFAULT_SONGS` 陣列**最後**新增物件、放封面到 `assets/images/`、id 接續流水號、歌手與歌名須為**虛構或已取得授權**。

## 8. Component Rules
- **新增卡片/清單列**：一律經 `renderSongs()` 內的 `buildCard()` / `buildRow()` 產生，**不手寫卡片 HTML**。
- **使用者輸入不得直接當 HTML**：一律使用 `el()` 輔助函式或 `textContent`；**禁止將使用者資料拼入 `innerHTML`、`insertAdjacentHTML`**。連結 `href` 必須先通過 `isHttpUrl()`。
- **新增類型/心情**：改 `data.js` 的 `GENRES` 或 `MOODS`；篩選鈕、下拉選單、統計長條會自動生成；並更新 §2、§7、至少新增一首該類型/心情的歌。
- **新增 view**：`index.html` 加 `<section id="view-xxx" class="view" hidden>`、導覽連結 `href="#xxx"`，並在 `app.js` 的 `VIEWS` 登記與撰寫 `renderXxx()`。
- **新增對話框**：原生 `<dialog>`，沿用 `.close`、`[data-close]`、`.btn`；Esc、×、點背景可關閉。
- **播放器**：只能經由 `renderPlayer()` 建立；`<audio>` 必須有 `controlsList="nodownload"`、`preload="none"`；iframe 必須經 `toEmbed()` 轉換（僅接受 YouTube watch/shorts/youtu.be 與 Spotify track 連結）並保留 `sandbox` 屬性；關閉對話框時必須停止並移除播放器（`stopPlayer`）。
- **空狀態**：任何列表都必須有空狀態（`.empty`，含一句友善說明與可行的下一步按鈕）。

## 9. Data Management Rules
- localStorage 只有一個鍵：`soundshelf.state`，結構：
  `{ version, userSongs:[], favorites:[songId], diary:[], settings:{ theme, shelfView } }`。
- **只能透過 `SoundShelfStorage`**（`load`、`save`、`validateSong`、`validateDiary`、`exportJson`）存取，不得在其他檔案直接呼叫 `localStorage`（主題預載 script 除外）。
- **版本**：`STATE_VERSION`（目前 1）。更動資料結構時：版本 +1，並在 `storage.js` 的 `MIGRATIONS` 加入 `n → n+1` 的轉換函式；舊資料必須能自動升級，不得丟失使用者資料。
- **錯誤處理**：JSON 損毀時，把原字串備份到 `soundshelf.state.corrupt`，回傳預設狀態並顯示提示；`save` 失敗（容量/禁用）要顯示 toast，不得讓程式崩潰。
- **載入時清理**：`load` 會剔除不合 Schema 的歌曲/日記、移除指向不存在歌曲的最愛。
- 預設資料（`data.js`）與使用者資料（localStorage）**嚴格分開**：永遠不把使用者資料寫回 `data.js`；合併只在 `app.js` 的 `allSongs()` 進行。

## 10. AI Development Workflow
每次修改依序：
1. **讀**：完整閱讀本文件與要改的檔案。
2. **計畫**：2–5 行說明要改什麼、為什麼、動到哪些檔案。
3. **最小修改**：只改必要部分；沿用 Schema、元件與設計系統；**不得變更主要風格**；**不得破壞 §2 既有功能**；**維持 RWD**。
4. **測試**：依 §12 逐項驗證（實際開啟頁面操作新舊功能）。
5. **回報**：結束時必須列出 **變更檔案、新增/修改的功能、測試結果、未解決事項**。
6. **同步文件**：若改了結構、Schema 或規則，同次更新本文件與 README。
**禁止**：引入框架/CDN 函式庫；使用付費/需金鑰服務；絕對路徑；違反 §1 播放與版權界線（未授權音檔、音檔下載）；使用真實專輯封面或未授權圖片；清空使用者 localStorage 資料。

## 11. Feature Extension Guide
- **新增一首預設歌曲**：§7 最後一點。
- **新增分類或心情**：§8。
- **新增功能（例：依歌手排序、匯出日記）**：(1) §2 功能表加一列；(2) 狀態放 `state`，持久化走 `SoundShelfStorage`（若新增儲存欄位 → §9 升版本）；(3) UI 沿用 `.chip/.btn/.panel`；(4) 補空狀態、鍵盤操作與 aria；(5) 跑完 §12。
- **調整回憶卡樣式**：只改 `memory.js` 的 `draw()`；保持 1080×1350 尺寸與色彩變數對應的色票。
- **新增可播放的預設歌曲**：把原創/CC0 mp3 放進 `assets/audio/`（建議 ≤500KB）、在 `assets/audio/README.md` 登記授權、再於資料加上 `audio` 欄位。
- **範例指令**：「請依 AGENTS.md，在 Indie 類型新增一首心情為 Chill 的歌曲，並附上封面。」

## 12. Quality Checklist
**功能**：卡片/清單切換；搜尋＋類型＋我的最愛可疊加；最愛重新整理後仍在；5 種心情歌單；日記可新增/刪除；新增音樂（含驗證、重複檢查）立即顯示且重整後仍在；隨機推薦；回憶卡可預覽並下載 PNG；預設歌曲可播放/暫停、關閉對話框後停止；貼 YouTube/Spotify 連結會出現內嵌播放器（非這兩者只顯示連結）；統計數字正確；主題與檢視偏好重整後保留。
**資料**：手動把 `soundshelf.state` 改成壞掉的 JSON → 網站仍可開啟並出現提示；舊版本資料能遷移。
**安全**：標題輸入 `<img src=x onerror=alert(1)>` 只會顯示為文字。
**RWD**：1280 / 820 / 390px 無橫向捲軸、無重疊。
**無障礙**：圖片 alt、按鈕名稱、`aria-pressed`/`aria-current`、鍵盤可操作、焦點可見、深淺色對比、`prefers-reduced-motion`。
**錯誤**：Console 無錯誤；`node --check js/*.js` 通過；所有路徑相對。

## 13. GitHub Pages Deployment Rules
1. 建立 **Public** repo（建議 `sound-shelf`），`index.html` 位於 repo 根目錄。
2. 推送到 `main`。Settings → Pages → Deploy from a branch → `main` / `(root)`。
3. 網址 `https://<帳號>.github.io/<repo>/`；檔名區分大小寫；所有資源用相對路徑。
4. 部署後在線上版重跑 §12；不得提交任何金鑰或個資。
