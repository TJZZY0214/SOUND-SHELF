# SOUND SHELF｜音樂收藏書架
> Your music, your memories. 收藏喜歡的音樂，也收藏每首歌背後的回憶。

結合音樂收藏、心情歌單、聆聽日記、回憶卡（可下載 PNG）與個人統計的互動網站。**單一 `index.html` 檔案**（CSS、JavaScript、封面圖、音樂全部內嵌），純 HTML / CSS / JavaScript，無後端、無 API Key，資料存在瀏覽器 localStorage。

## 功能
收藏牆（卡片/清單）、類型篩選與搜尋、我的最愛、心情歌單、聆聽日記、新增音樂、隨機推薦、Music Memory 回憶卡、個人統計、淺色/深色模式。

**播放**：預設的 12 首歌為程式合成的原創循環音樂（CC0，非現有歌曲），可直接在站內播放；使用者新增歌曲時貼上 YouTube 或 Spotify 單曲連結，會以官方內嵌播放器播放。本站不提供音檔下載，也不含任何未授權音檔。預設歌手與歌名皆為虛構，封面為原創 SVG。

## 使用方式
直接雙擊 `index.html` 即可開啟（需要網路時只有 Google Fonts，離線會自動使用系統字體）。

## 部署到 GitHub Pages
1. 建立 Public repo（例如 `sound-shelf`），把 index.html 放在根目錄並推送到 `main`。
2. Settings → Pages → Deploy from a branch → `main` / `(root)`。
3. 網址：`https://<帳號>.github.io/sound-shelf/`

## 專案結構
只有 `index.html`（內含 STYLE / DATA / STORAGE / MEMORY / APP / ASSETS 六個區塊）、`AGENTS.md`、`README.md`。做成單檔是為了不依賴任何外部路徑。

## 給 AI 的規則
所有開發規則見 [AGENTS.md](AGENTS.md)。

## 使用的 AI
Claude Sonnet 5.5（Anthropic）協助產生程式碼與文件。
