# 櫻雪集團網站後台 — 技術架構文件

> 最後更新：2026-08-06 | 狀態：設計確認，待實作

---

## 一、概述

為櫻雪集團（SAKURA YUKI）旗下三個品牌建立統一內容管理後台：
- **SY Dive** 櫻雪潛水（3 頁）
- **SY Swim** 櫻雪游泳（2 頁，含 1 頁新建）
- **SY Snow** 櫻雪滑雪（1 頁，新建空架構）

技術棧：**Decap CMS** + **11ty (Eleventy)** + **Netlify** + **GitHub**

---

## 二、技術選型理由

| 層 | 選擇 | 理由 |
|---|------|------|
| CMS | Decap CMS (原 Netlify CMS) | Git-based、純靜態、零伺服器成本、與 Netlify 原生整合 |
| SSG | 11ty (Eleventy) | 最靈活，現有 HTML 可直接轉為 Nunjucks 模板，CSS 設計 100% 保留 |
| 部署 | Netlify | GitHub push → 自動 build → 自動部署 |
| 認證 | Netlify Identity + Git Gateway | Decap CMS 內建支援，免自建登入系統 |

---

## 三、目錄結構

```
sakura-yuki-website/                  # GitHub repo 根目錄
├── netlify.toml                      # Netlify 建置設定
├── package.json                      # 11ty + 依賴
├── .eleventy.js                      # 11ty 設定檔
│
├── src/                              # 原始碼（11ty 處理）
│   ├── _data/                        # 全域資料檔
│   │   └── site.json                 # 網站名稱、SEO、社群連結等全域設定
│   │
│   ├── _includes/                    # 共用模板片段
│   │   ├── base.njk                  # 基礎 HTML 框架（<head> / nav / footer）
│   │   ├── nav.njk                   # 導覽列
│   │   ├── footer.njk                # 頁尾
│   │   ├── hero.njk                  # Hero 區塊模板
│   │   ├── section-head.njk          # 區塊標頭（カナ + 標題 + sec-line）
│   │   ├── loc-card.njk              # 據點卡片
│   │   ├── faq-item.njk              # FAQ 項目
│   │   └── cta-buttons.njk           # CTA 按鈕組
│   │
│   ├── css/
│   │   ├── main.css                  # 共用設計系統（色盤/字體/動效/按鈕）
│   │   ├── dive.css                  # SY Dive 品牌樣式
│   │   ├── swim.css                  # SY Swim 品牌樣式（手繪蠟筆風）
│   │   └── snow.css                  # SY Snow 品牌樣式（待設計）
│   │
│   ├── js/
│   │   └── main.js                   # reveal 動效 / nav toggle / carousel
│   │
│   ├── images/                       # 圖片資源
│   │   ├── dive/                     # SY Dive 圖片
│   │   ├── swim/                     # SY Swim 圖片
│   │   ├── snow/                     # SY Snow 圖片
│   │   └── shared/                   # 共用（logo 等）
│   │
│   ├── dive/                         # SY Dive 頁面
│   │   ├── index.md                  # 主頁（23 區塊的 frontmatter）
│   │   ├── pool.md                   # 櫻雪游泳池頁（8 區塊）
│   │   └── okinawa.md                # 沖繩導覽頁（9 區塊）
│   │
│   ├── swim/                         # SY Swim 頁面
│   │   ├── index.md                  # 品牌首頁（新建）
│   │   └── renai-pool.md             # 仁愛游泳池（搬移現有內容）
│   │
│   └── snow/                         # SY Snow 頁面
│       └── index.md                  # 品牌首頁（新建空架構）
│
├── admin/                            # Decap CMS 後台
│   ├── index.html                    # 後台入口頁
│   └── config.yml                    # CMS 設定（collections / fields）
│
└── dist/                             # 11ty 建置輸出（Git-ignored，Netlify 產生）
    ├── dive/index.html
    ├── swim/index.html
    ├── snow/index.html
    └── ...
```

---

## 四、Decap CMS config.yml 結構

### 4.1 後台設定

```yaml
backend:
  name: git-gateway
  branch: main

media_folder: "src/images"
public_folder: "/images"

collections:
  - name: "settings"
    label: "全域設定"
    ...
  - name: "dive"
    label: "SY Dive 櫻雪潛水"
    ...
  - name: "swim"
    label: "SY Swim 櫻雪游泳"
    ...
  - name: "snow"
    label: "SY Snow 櫻雪滑雪"
    ...
```

### 4.2 全域設定 Collection (`settings`)

使用 Decap CMS 的 `file` collection（單一檔案）：

| 欄位 | 類型 | 說明 |
|------|------|------|
| site_name | string | 網站名稱 |
| site_description | string | SEO 描述 |
| og_image | image | 社群分享圖 |
| nav_items | list | 導覽列項目（文字 + 連結） |
| contact_email | string | 客服信箱 |
| contact_phone | string | 客服電話 |
| line_url | string | LINE 官方帳號連結 |
| ig_handle | string | Instagram |
| fb_handle | string | Facebook |
| footer_text | text | Footer 文字 |

### 4.3 頁面 Collections 設計原則

每個品牌一個 collection，內含多個 page file。每個頁面使用結構化 frontmatter 定義所有區塊欄位。

---

## 五、頁面區塊 → Decap CMS Fields 對照

### 5.1 SY Dive 主頁 (`dive/index.md`) — 23 區塊

| # | 區塊 | Decap Field | 類型 |
|---|------|------------|------|
| 1 | Hero 主標題 | `hero.title` | string |
| 2 | Hero 副標 | `hero.subtitle` | string |
| 3 | Hero カナ | `hero.kana` | string |
| 4 | Hero 仁愛地址 | `hero.address_tpe` | string |
| 5 | Hero 沖繩地址 | `hero.address_oki` | string |
| 6 | Hero CTA 1 文字 | `hero.cta_primary` | string |
| 7 | Hero CTA 2 文字 | `hero.cta_secondary` | string |
| 8 | Hero 背景圖 | `hero.bg_image` | image |
| 9 | Hero 標記圖 | `hero.mark_image` | image |
| 10 | 據點區塊標題 | `locations.title` | string |
| 11 | 據點區塊說明 | `locations.description` | text |
| 12 | 仁愛卡片：全部欄位 | `locations.cards[0]` | object |
| 13 | 龍洞卡片：全部欄位 | `locations.cards[1]` | object |
| 14 | 沖繩卡片：全部欄位 | `locations.cards[2]` | object |
| 15 | 交通區塊：3 卡片 | `transport.cards[]` | list |
| 16 | 特色數據：4 組 | `features.items[]` | list |
| 17 | 課程區塊：3 卡片 | `courses.cards[]` | list |
| 18 | 沖繩地理：3 數據 | `okinawa_geo.items[]` | list |
| 19 | PADI 路徑：標題＋3步＋badge | `pathway.*` | group |
| 20 | 方案：龍洞＋沖繩 | `plans[]` | list |
| 21 | 排程：7 時間點＋摘要 | `schedule.*` | group |
| 22 | 泳池 teaser：說明＋4 項目 | `pool_teaser.*` | group |
| 23 | 冬雪夏水：標語 | `dual_season.tagline` | string |
| 24 | 優惠：標題＋說明＋2 折扣 | `discount.*` | group |
| 25 | 安全須知：3 卡片 | `safety.cards[]` | list |
| 26 | FAQ：N 組 Q&A | `faq.items[]` | list |
| 27 | 品牌架構：說明文 | `brand_tree.description` | text |
| 28 | 聯絡：地址×2＋電話＋email＋社群 | `contact.*` | group |
| 29 | 聯絡背景圖 | `contact.bg_image` | image |
| 30 | 招募：標題＋說明＋連結 | `recruit.*` | group |
| 31 | 保險：3 卡片＋底部提示 | `insurance.*` | group |
| 32 | Footer 品牌文字 | (使用全域設定或頁面級) | string |

### 5.2 櫻雪游泳池頁 (`dive/pool.md`) — 8 區塊

| # | 區塊 | Decap Field | 類型 |
|---|------|------------|------|
| 1 | Hero 標題 | `hero.title` | string |
| 2 | Hero 副標 | `hero.subtitle` | text |
| 3 | Hero CTA 文字 | `hero.cta_text` | string |
| 4 | Hero 背景圖 | `hero.bg_image` | image |
| 5 | 像家一樣的泳池：說明文 | `intro.description` | text |
| 6 | 全景圖 | `intro.panorama_image` | image |
| 7 | 4 個統計數據 | `intro.stats[]` | list |
| 8 | 服務卡片：4 張 | `services.cards[]` | list |
| 9 | 設備卡片：3 張 | `facilities.cards[]` | list |
| 10 | 交通卡片：3 張 | `transport.cards[]` | list |
| 11 | 交通地址文字 | `transport.address` | text |
| 12 | CTA 區標題 | `cta.title` | string |
| 13 | CTA 區說明 | `cta.subtitle` | text |
| 14 | CTA 區圖片 | `cta.image` | image |
| 15 | CTA 按鈕×2 | `cta.buttons[]` | list |

### 5.3 沖繩導覽頁 (`dive/okinawa.md`) — 9 區塊

| # | 區塊 | Decap Field | 類型 |
|---|------|------------|------|
| 1 | Hero 標題 | `hero.title` | string |
| 2 | Hero 副標 | `hero.subtitle` | string |
| 3 | Hero CTA 文字 | `hero.cta_text` | string |
| 4 | Hero 背景圖 | `hero.bg_image` | image |
| 5 | 簡介說明文 | `intro.description` | text |
| 6 | 3 個地理數據 | `intro.stats[]` | list |
| 7 | 為什麼跟櫻雪：4 icon | `why_us.items[]` | list |
| 8 | 美食饗宴：8 張卡片 | `food.cards[]` | list |
| 9 | 必訪景點：8 張輪播 | `spots.slides[]` | list |
| 10 | 據點地址 | `location.address` | string |
| 11 | Google Map iframe URL | `location.map_url` | string |
| 12 | CTA 聯絡資訊 | `cta.info` | text |
| 13 | CTA 表單（靜態，保留模板） | — | — |

---

## 六、11ty 模板架構

### 6.1 模板層級

```
base.njk                          # 最外層：<html> → <head> → <body> → nav + content + footer
├── dive/index.njk                # SY Dive 主頁模板（延伸 base.njk）
├── dive/pool.njk                 # SY Dive 泳池頁
├── dive/okinawa.njk              # SY Dive 沖繩導覽
├── swim/index.njk                # SY Swim 首頁
├── swim/renai-pool.njk           # SY Swim 仁愛池
└── snow/index.njk                # SY Snow 首頁
```

### 6.2 模板渲染流程

```
Markdown (.md) frontmatter
        ↓
11ty 讀取 frontmatter → data 物件
        ↓
Nunjucks 模板 (.njk) 使用 {{ data.field }} 渲染 HTML
        ↓
輸出自動優化（minify CSS/JS）
        ↓
輸出至 dist/ 目錄
```

### 6.3 關鍵 11ty 設定 (`.eleventy.js`)

```js
module.exports = function(eleventyConfig) {
  // 靜態資源直接複製
  eleventyConfig.addPassthroughCopy("src/images");
  eleventyConfig.addPassthroughCopy("src/css");
  eleventyConfig.addPassthroughCopy("src/js");
  eleventyConfig.addPassthroughCopy("admin");

  return {
    dir: {
      input: "src",
      output: "dist",
      includes: "_includes",
      data: "_data"
    },
    markdownTemplateEngine: "njk",
    htmlTemplateEngine: "njk"
  };
};
```

---

## 七、部署流程

### 7.1 一次性設定

```
1. 建立 GitHub repo: ifgto0923-design/sakura-yuki-website
2. 在 Netlify 建立新 site，連接此 repo
3. Build command: npx @11ty/eleventy
4. Publish directory: dist
5. 啟用 Netlify Identity（用於 Decap CMS 登入）
6. 啟用 Git Gateway
7. 在 Netlify Identity 中邀請管理員 email
```

### 7.2 日常使用流程

```
管理者                         系統
───────                       ──────
開啟 /admin/                   → Decap CMS 登入頁
Netlify Identity 登入          → 驗證身份
選擇品牌 → 選擇頁面            → 載入編輯表單
修改欄位內容                    →
上傳圖片（拖曳）                → 存入 Git repo
點擊「發布」                    → Git commit + push
                               → Netlify 偵測 push
                               → 自動跑 11ty build
                               → 部署到 CDN（～30 秒）
                               → 網站更新完成 ✅
```

### 7.3 域名規劃

| 品牌 | 建議域名 |
|------|---------|
| SY Dive 主站 | `sydiving.com.tw` |
| SY Swim | `sydiving.com.tw/swim/` 或獨立域名 |
| SY Snow | `sydiving.com.tw/snow/` 或獨立域名 |

---

## 八、品牌設計系統對照

| 設計元素 | SY Dive | SY Swim | SY Snow |
|---------|---------|---------|---------|
| 字體標題 | Noto Serif TC | ZCOOL KuaiLe | Noto Serif TC |
| 字體內文 | Noto Sans TC/JP | Noto Sans TC | Noto Sans TC |
| 主色 | 琉球藍 #3a7ca5 | 寶礦力藍 #0080ff | 待設計 |
| 輔色 | 櫻吹雪 #eea5b1 | 薄櫻 #f7c5cc | 待設計 |
| CTA | 落櫻緋 #c44536 | 薄櫻 #f7c5cc | 待設計 |
| 底色 | 宣紙白 #faf6f0 | 奶白 #fdfaf5 | 待設計 |
| 風格 | 宋代美學 + 日本排版 | 手繪蠟筆風 | 待設計 |
| 卡片 | 直角、0.5px 線 | 不對稱圓角、box-shadow | 待設計 |

---

## 九、實作階段規劃

| 階段 | 內容 | 預估 |
|------|------|------|
| **Phase 1** | 建立 repo + 11ty 骨架 + base 模板 + 全域 CSS | 基礎建設 |
| **Phase 2** | SY Dive 主頁 11ty 模板 + Decap config | 核心頁面 |
| **Phase 3** | SY Dive 泳池頁 + 沖繩導覽頁模板 | 現有內容 |
| **Phase 4** | Decap CMS admin 設定 + Netlify Identity | 後台上線 |
| **Phase 5** | SY Swim 品牌首頁 + 仁愛池搬移 | 游泳品牌 |
| **Phase 6** | SY Snow 品牌首頁空架構 | 滑雪品牌 |
| **Phase 7** | Netlify 部署 + 域名設定 + 測試 | 正式上線 |

---

## 十、附錄：現有檔案遷移對照

| 現有路徑 | 新路徑 | 變更 |
|---------|--------|------|
| `sy-dive-site/index.html` | `src/dive/index.md` + `src/_includes/dive/index.njk` | HTML → Markdown frontmatter + 模板 |
| `sy-dive-site/櫻雪游泳池.html` | `src/swim/renai-pool.md` + 模板 | 移至 SY Swim 品牌下 |
| `sy-dive-site/沖繩潛水導覽.html` | `src/dive/okinawa.md` + 模板 | 更名為英文檔名 |
| `sy-dive-site/sy-dive-images/*` | `src/images/dive/*` | 重組目錄結構 |
| CSS（分散在各頁 <style>） | `src/css/main.css` + 品牌 CSS | 集中管理 |
