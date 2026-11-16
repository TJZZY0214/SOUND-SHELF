# SOUND SHELF｜音樂收藏書架
> Your music, your memories. 收藏喜歡的音樂，也收藏每首歌背後的回憶。

結合音樂收藏、心情歌單、聆聽日記、回憶卡（可下載 PNG）與個人統計的互動網站。純 HTML / CSS / JavaScript，無後端、無 API Key，資料存在瀏覽器 localStorage。

## 功能
收藏牆（卡片/清單）、類型篩選與搜尋、我的最愛、心情歌單、聆聽日記、新增音樂、隨機推薦、Music Memory 回憶卡、個人統計、淺色/深色模式。

**播放**：預設的 12 首歌為程式合成的原創循環音樂（CC0，非現有歌曲），可直接在站內播放；使用者新增歌曲時貼上 YouTube 或 Spotify 單曲連結，會以官方內嵌播放器播放。本站不提供音檔下載，也不含任何未授權音檔。預設歌手與歌名皆為虛構，封面為原創 SVG。

## 本機測試
```bash
python3 -m http.server 8000   # 開啟 http://localhost:8000
```
（請用本機伺服器而非 file://，回憶卡才能正常繪入封面。）

## 部署到 GitHub Pages
1. 建立 Public repo（例如 `sound-shelf`），把專案內容放在根目錄並推送到 `main`。
2. Settings → Pages → Deploy from a branch → `main` / `(root)`。
3. 網址：`https://<帳號>.github.io/sound-shelf/`

## 專案結構
`index.html`、`css/style.css`、`js/data.js`（預設資料）、`js/storage.js`（使用者資料）、`js/memory.js`（回憶卡）、`js/app.js`（介面）、`assets/images/`、`assets/audio/`。拆出 storage/memory 是為了讓資料與繪圖和 UI 分離。

## 給 AI 的規則
所有開發規則見 [AGENTS.md](AGENTS.md)。

## 使用的 AI
Claude Sonnet 5.5（Anthropic）協助產生程式碼與文件。
