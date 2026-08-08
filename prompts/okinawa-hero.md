# 沖繩導覽 Hero 圖 — AI 生成提示池

> 最後更新：2026-08-06
> 用途：沖繩導覽頁 Hero 背景圖，半透明疊在寶礦力藍→琉球藍漸層上
> 生成工具：Draw Things (macOS)

---

## 需求規格

| 項目 | 設定 |
|------|------|
| 主題 | 三人小團體沙灘嬉戲潑水 |
| 人物 | 2 女 1 男，25-30 歲年輕上班族 |
| 服裝 | 半濕半乾混合（女：櫻花粉，男：米白短褲＋亞麻襯衫） |
| 動作 | 互相潑水、大笑，正面/半側面捕捉瞬間 |
| 光線 | 正午豔陽，高對比，藍天白雲 |
| 色調 | 寶礦力藍系海洋（#d4f1f9 → #3a7ca5 感） |
| 風格 | 寶礦力水得廣告風，青春活力但不幼齒 |
| 構圖 | 橫幅 1216×832，半透明疊加 Hero 漸層用 |

---

## Prompt

```
A group of three young East Asian friends, two women and one man, ages 25-30,
playing and splashing water at each other on a tropical beach, candid laughter,
genuine joyful expressions, mid-action capture,

one woman wearing a soft cherry blossom pink tank top and light denim shorts,
wet fabric clinging naturally,
second woman in a simple cherry blossom pink one-piece swimsuit,
the man in light beige shorts and a half-unbuttoned white linen shirt,

bright midday sunlight, cloudless deep blue sky, crystal-clear turquoise ocean
in the background, white sand beach, water droplets frozen mid-air catching sunlight,
strong shadows and high contrast,

composition: wide horizontal frame, subjects slightly off-center in the lower third,
expansive ocean and sky filling the upper portion,

photographic, shot on Fujifilm Provia, natural skin texture, film grain,
Pocari Sweat advertisement aesthetic — youthful, refreshing, energetic,
editorial sports lifestyle photography
```

---

## 負向詞

```
dark, moody, gloomy, overcast, sunset, evening, night,
sexualized, seductive, lingerie, revealing, pinup, glamour,
stiff pose, studio lighting, artificial, fake smile, stock photo,
too young, teenage, children, elderly,
crowded beach, messy, dirty, pollution,
illustration, 3D render, CGI, cartoon, painting,
blurry face, distorted hands, extra limbs, bad anatomy
```

---

## Draw Things 參數

| 參數 | 值 |
|------|-----|
| 模型 | SDXL (sd_xl_base_1.0) |
| 尺寸 | 1216 × 832 |
| Steps | 30 |
| CFG | 7.5 |
| Sampler | DPM++ 2M Karras |
| Seed | -1（隨機） |
