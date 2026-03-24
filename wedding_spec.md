# Wedding Photo Gallery — 婚紗照展示網頁
## Claude Code 開發規格文件 v1.0

---

## 1. 專案概述

本文件為婚紗照展示靜態網站的完整開發規格，交由 Claude Code 直接依此文件生成所有程式碼。

目標：一個簡約、美感、具婚紗氛圍的照片展示網頁，搭配流暢動態特效，可透過 QR Code 分享。

### 技術選型

- 純靜態網頁：index.html + CSS + Vanilla JS（零依賴，零框架）
- 托管：GitHub Pages（免費、自訂網址）
- 字型：Google Fonts（Cormorant Garamond + Jost）
- QR Code 分享：部署後使用外部工具生成，無需程式碼

### 專案目錄結構

```
wedding-photos/
├── index.html          ← 主要網頁（單一檔案）
├── photos/             ← 婚紗照資料夾
│   ├── 01.jpg
│   ├── 02.jpg
│   └── ...（共 20 張）
└── README.md
```

---

## 2. 視覺風格規格

### 2.1 色彩系統

整體以奶茶暖色系為主，營造溫馨婚紗氛圍：

| CSS 變數名稱 | 色碼 | 用途 |
|---|---|---|
| `--color-bg` | `#FAF7F4` | 頁面背景 |
| `--color-surface` | `#F0EAE3` | 卡片、區塊背景 |
| `--color-hero-start` | `#E8DDD5` | Hero 漸層起點 |
| `--color-hero-end` | `#C9B8B0` | Hero 漸層終點 |
| `--color-text-dark` | `#3D2F2A` | 主標題文字 |
| `--color-text-mid` | `#6B5650` | 副標題文字 |
| `--color-text-muted` | `#8A7068` | 輔助文字、裝飾 |
| `--color-accent` | `#B0908A` | 愛心、細節裝飾 |
| `--color-border` | `#E0D4CD` | 邊框、分隔線 |

### 2.2 字型規格

- **主標題**（新人名字、大標）：Cormorant Garamond，`font-weight: 300`，italic，`letter-spacing: 6px`
- **副標題、日期**：Jost，`font-weight: 200`，`letter-spacing: 4–5px`，`text-transform: uppercase`
- **內文、說明**：Jost，`font-weight: 300`，`line-height: 1.8`
- **Google Fonts 引入**：Cormorant Garamond 300/400 + italic；Jost 200/300/400

### 2.3 圓角與間距

- 圖片格：無圓角（邊對邊，瀑布流感覺）
- Lightbox 彈窗：`border-radius: 4px`
- 區塊間距：使用充裕的 padding，給版面呼吸空間

---

## 3. 頁面區塊結構

### 3.1 Hero 區塊

全寬高 `100vh`，奶茶色漸層背景，垂直置中排版。

內容元素由上到下：

1. 小裝飾文字：`Wedding Gallery`，uppercase，`letter-spacing: 4px`，color: muted
2. 新人名字：`[新郎] & [新娘]`，serif 大字，`&` 號用 italic 且稍大
3. 裝飾分隔線：左右細線 + 中間 ✦ 符號
4. 結婚日期：`YYYY · MM · DD`，uppercase，Jost 200
5. 向下滾動箭頭：細線箭頭，含上下浮動動畫

Hero 進場動畫：所有元素依序 fade-in + 向上位移（stagger delay，每項間隔 0.2s）

### 3.2 Gallery 區塊

標題：`— Our Story —`，置中，italic serif。

照片排版：CSS Grid，響應式分欄：

- 手機（`< 600px`）：1 欄
- 平板（`600–900px`）：2 欄
- 桌機（`> 900px`）：3 欄

照片比例：`3:4`（縱向，婚紗常見比例），使用 `aspect-ratio: 3/4` + `object-fit: cover`。

第 1 張（主照）：跨 2 欄，比例改為 `16:9` 作為橫幅大圖。

### 3.3 Footer 區塊

裝飾分隔線（同 Hero 樣式）＋一句浪漫引言（繁體中文）＋小裝飾 tag。

- 引言預設：`「兩個人的故事，從這一天開始。」`
- 結尾版權：`© 2025 [新郎] & [新娘]`

---

## 4. 動態特效規格

以下所有動畫效果全部用純 CSS + Vanilla JS 實現，不使用任何動畫函式庫。

### 4.1 頁面進場動畫（Hero）

**效果：Fade-in + 向上滑入（stagger）**

- 每個 Hero 子元素預設 `opacity: 0`，`transform: translateY(20px)`
- 使用 CSS `@keyframes fadeSlideUp` 定義動畫
- JavaScript 在 `DOMContentLoaded` 後依序為每個元素加上 animation class
- 各元素 `animation-delay`：0s / 0.2s / 0.4s / 0.6s / 0.8s
- `animation-duration: 0.8s`，easing: `cubic-bezier(0.25, 0.46, 0.45, 0.94)`

### 4.2 Gallery 進場動畫（Scroll Reveal）

**效果：照片滑入（Intersection Observer）**

- 每張照片預設 `opacity: 0`，`transform: translateY(40px)`
- 使用 `IntersectionObserver`（`threshold: 0.1`）監聽每張圖片
- 進入視窗時觸發 `.visible` class，套用 `transition: opacity 0.7s, transform 0.7s`
- easing: `cubic-bezier(0.22, 1, 0.36, 1)`
- 相鄰圖片 delay 交錯：`index % 3 * 0.1s`（同排照片略微錯開）

### 4.3 照片 Hover 特效

**效果：縮放 + 遮罩 + 放大圖示**

- 容器：`overflow: hidden`，`transition: transform 0.4s ease`
- hover 時圖片：`transform: scale(1.06)`，`transition: 0.5s ease`
- hover 時疊加半透明遮罩（`::after` pseudo-element）：`background: rgba(61,47,42,0.25)`，opacity 從 `0 → 1`
- 遮罩中央顯示放大圖示：白色細框正方形 + 內部放大鏡 SVG icon
- 圖示動畫：`opacity 0 → 1`，`scale(0.8) → scale(1)`，`delay: 0.05s`

### 4.4 Lightbox 放大檢視

**效果：點擊照片後全螢幕展示**

#### 開啟動畫

- 背景遮罩：固定全螢幕，`background: rgba(30,20,18,0.92)`，`backdrop-filter: blur(4px)`
- 遮罩 fade in：`opacity 0 → 1`，`duration: 0.3s`
- 照片：`scale(0.88) + opacity 0` → `scale(1) + opacity 1`，`duration: 0.35s`，easing: `cubic-bezier(0.22, 1, 0.36, 1)`

#### 關閉動畫

- 反向播放：`scale(1) → scale(0.92)`，`opacity 1 → 0`，`duration: 0.25s`
- 動畫結束後移除 DOM 元素

#### Lightbox 功能

- 左右箭頭切換（`<` `>`），箭頭 hover 時 `opacity 0.5 → 1`
- 鍵盤支援：`←` `→` 切換，`ESC` 關閉
- 點擊背景關閉
- 底部顯示當前張數：`3 / 20`
- 手機支援 touch swipe 左右切換（`touchstart` / `touchend` 偵測）

### 4.5 滾動箭頭動畫

- Hero 底部向下箭頭：`translateY(0px) → translateY(8px) → translateY(0px)`，loop，`duration: 1.6s`，easing: `ease-in-out`
- 點擊箭頭平滑滾動至 Gallery 區塊（`scrollIntoView({ behavior: 'smooth' })`）

### 4.6 Footer 引言文字動畫

- Scroll Reveal：同 Gallery 照片，fade-in + 向上滑入
- 引言文字每行加上輕微 stagger，使用 JS 設定個別 `animation-delay`

---

## 5. 照片配置

### 5.1 圖片規格建議

- 格式：JPG（品質 85–90%），避免 PNG（檔案過大）
- 長邊建議：2000px 以內（兼顧品質與載入速度）
- 命名規則：`01.jpg`, `02.jpg`, ..., `20.jpg`（補零）
- 存放路徑：`./photos/` 資料夾

### 5.2 JS 照片陣列定義

在 `index.html` 的 `<script>` 區塊定義，方便日後新增或調整順序：

```javascript
const photos = [
  { src: './photos/01.jpg', caption: '' },  // 第1張：主視覺橫幅大圖
  { src: './photos/02.jpg', caption: '' },
  { src: './photos/03.jpg', caption: '' },
  // ... 共 20 張
  { src: './photos/20.jpg', caption: '' },
];
```

`caption` 欄位可留空，或填入短文字（會在 Lightbox 底部顯示）。

### 5.3 Grid 排版規則

- 第 1 張：`grid-column: 1 / -1`，`aspect-ratio: 16/9`（橫幅主照）
- 第 2–20 張：正常 `3:4` 直式排版，自動填入 Grid
- Grid gap：`3px`（讓照片緊密但有微小間隙）

---

## 6. 響應式設計（RWD）

| 裝置 | 斷點 | Gallery 欄數 |
|---|---|---|
| 手機 | `< 600px` | 1 欄 |
| 平板 | `600–900px` | 2 欄 |
| 桌機 | `> 900px` | 3 欄（最大寬度 1200px 置中）|

- Hero 文字在手機版縮小：名字 `font-size: 28px`（桌機 `44px`）
- Lightbox 箭頭在手機版隱藏，改用 swipe 操作
- Hero 高度手機版：`100svh`（safe area aware）

---

## 7. GitHub Pages 部署流程

1. 在 GitHub 新建 Public repo，命名為 `wedding-photos`
2. 本地建立專案結構（`index.html` + `photos/` 資料夾）
3. 將婚紗照片放入 `photos/` 資料夾，命名 `01.jpg`–`20.jpg`
4. `git init` → `git add .` → `git commit` → `git push`
5. GitHub repo → Settings → Pages → Source: main branch / root
6. 約 1–2 分鐘後，網址生效：`https://[帳號].github.io/wedding-photos`
7. 至 [qr-code-generator.com](https://qr-code-generator.com) 輸入網址，生成 QR Code 下載

---

## 8. Claude Code 使用指令

將本文件交給 Claude Code 後，直接輸入以下指令開始開發：

```
請閱讀本規格文件（wedding_spec.md），依照所有規格生成完整的 index.html。要求：
- 單一 index.html 檔案，內嵌所有 CSS 和 JS
- 完整實現第 4 節所有動態特效
- 照片陣列預設填入 01.jpg 到 20.jpg
- 新人名字欄位預留 [新郎姓名] 和 [新娘姓名] 待替換
- 結婚日期預留 [YYYY · MM · DD] 待替換
- 完成後確認 Lighthouse 無明顯 accessibility 警告
```

---

## 9. 完成後客製化替換清單

Claude Code 生成程式碼後，只需替換以下欄位：

| 項目 | 預留符號 | 說明 |
|---|---|---|
| 新郎姓名 | `[新郎姓名]` | Hero 區塊名字 |
| 新娘姓名 | `[新娘姓名]` | Hero 區塊名字 |
| 結婚日期 | `[YYYY · MM · DD]` | Hero 日期 |
| 引言文字 | `[引言內容]` | Footer 浪漫短句 |
| 照片路徑 | `photos/01.jpg` 等 | photos/ 資料夾內 |
| 照片說明 | `caption` 欄位 | 可留空或填短句 |

---

*— End of Document —*
