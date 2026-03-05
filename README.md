# YAF Web（Vue 3 + TypeScript + Vite）

本專案為活動網站前端，使用 Vue 3 + Vite 建置，靜態資源與可更新內容集中於 `public/`。

## 專案架構

- `src/`
  - `main.ts`：入口檔
  - `App.vue`：根元件
  - `router/index.ts`：路由設定
  - `views/`：頁面（路由對應）
  - `components/`：可重用元件
- `public/`
  - `img/`：圖片素材（首頁視覺、社團攤位圖等）
  - `markdown/`：以 Markdown 管理的內容
  - `txt/`：文字資料（活動資訊、表單連結等）
  - `club_info.csv`：社團名單資料
- `index.html`：Vite 入口 HTML
- `vite.config.ts`：Vite 設定

## 透過 public 上傳新資料更新資訊

此專案以 `public/` 作為對外可存取的靜態資源來源。頁面會透過 `import.meta.env.BASE_URL` 讀取檔案，因此只要更新 `public/` 內的檔案即可更新畫面內容。

常見更新流程：

`⚠️注意:確保你上傳的branch是在gh-page而不是main，不然無法顯示更新內容⚠️`

1. 將新資料放到 `public/` 對應資料夾
  - 文字資料：`public/txt/`
  - Markdown 內容：`public/markdown/`
  - 圖片資源：`public/img/`
2. 保持檔名與路徑不變（或同步調整程式中的引用路徑）
3. 重新整理頁面即可看到更新

### 範例：更新活動資訊

`src/views/HomePage.vue` 會讀取 `public/txt/activity_info.txt`。格式如下：

```
2026雲緣起YF08
活動時間
2026.06.28 SUN 10:00~17:00
活動地點
國立雲林科技大學活動中心
主辦單位
國立雲林科技大學 櫻華社
國立虎尾科技大學 Cosplay研究社
指導單位
雲林縣政府
```

只要上傳並覆蓋 `public/txt/activity_info.txt`，首頁「活動簡介」會自動更新。

## `HomePage.vue` 功能說明

`src/views/HomePage.vue` 是首頁頁面，主要功能：

- 透過 `ParallaxImage` 疊加多層視覺素材並呈現視差動態
- 載入 `public/txt/activity_info.txt` 並解析成活動資訊結構
- 以 `ContentBlock` 顯示「活動簡介」與「最新消息」
- 嵌入 Facebook 粉專 iframe 供最新動態瀏覽

## `ContentBlock.vue` 功能說明

`src/components/ContentBlock.vue` 是統一內容區塊元件，主要功能：

- 提供標題與內容排版
- 支援多種內容來源：
  - 純文字（`content`）
  - HTML 字串（`isHtml`）
  - Markdown 檔案（`isMarkdown`，由 `public/` 讀取）
  - 子元件（`isComponent`，搭配 `componentProps`）
- 進場淡入效果（scroll 觸發）與強制顯示（`forceVisible`）
- 內容為空或 `disable` 時顯示「尚未開放」圖示


## 如何更改網頁嵌入預覽


- 前往`index.html`
- 找到以下內容，並更改`property="og:description"`中的`content`內容文字

```

    <meta property="og:type" content="article">
    <meta property="og:title" content="雲緣起官方網站">
    <meta property="og:description" content="6/28 雲緣起YF08 雲林科技大學 歡迎一起雲緣啟動!">
    <meta property="og:image" content="/img/logo.jpg">

```

## `ParallaxImage.vue` 運作邏輯

`src/components/ParallaxImage.vue` 用於首頁多層視差圖片，核心邏輯如下：

- 以 `img` 呈現，實際路徑會加上 `import.meta.env.BASE_URL`
- 接受 `direction` 與 `offsetMultiplier`，根據 `scrollY` 計算 `translate3d` 位移
- 使用 `requestAnimationFrame` 節流 scroll 事件，減少重排
- 支援響應式參數（desktop/tablet/mobile），在 `resize` 時重新計算
- 以 `position: absolute` + `pointer-events: none` 疊在主圖上

## `router/index.ts` 說明

`src/router/index.ts` 負責路由設定：

- 使用 `createWebHistory('/YAF_web/')` 設定 base path
- 首頁 `/` 直接載入 `HomePage.vue`
- 其餘頁面多為動態載入（lazy import）
- `scrollBehavior` 控制換頁捲動：有歷史位置則還原，否則回到頁面頂部

## `style.css` 說明

`src/style.css` 為全域樣式，重點如下：

- 全域字體設定（`YakuHanJPs` 與日文字體）
- `body` 使用上下兩層背景圖，並禁止橫向捲動
- 標題、段落、清單的基本間距
- 按鈕與 `#app` 的基礎樣式

## 重新 build 流程（主程式有變更時）

當 `src/` 內主程式、元件或路由有修改，需要重新產出 `dist/`：

1. 安裝套件（首次或有更新時）
  - `npm install`
2. 重新 build
  - `npm run build`
3. 本機預覽（可選）
  - `npm run preview`

若要更新 GitHub Pages（已設定 `gh-pages`）：

- `npm run deploy`

