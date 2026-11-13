---
title: "汪博士 (Dr. Wang) 敗血症知識庫插圖素材索引表"
type: glossary
tags: [dr-wang, assets, visual-guide, slides, html, sepsis]
created: 2026-11-13
updated: 2026-11-13
status: evergreen
---

# 汪博士 (Dr. Wang) 敗血症知識庫插圖素材索引表

本檔案記錄 `圖片/` 資料夾內所有「汪博士」插圖的視覺特徵、情緒標籤與敗血症研究適用場景。
當 Agent 撰寫敗血症 Wiki 筆記、製作 Journal Club 簡報 (`outputs/slides/`) 或生成互動式 HTML 網頁 (`outputs/html/`) 時，請依本表挑選最符合臨床與 AI 語境的插圖。

---

## 插圖清單與敗血症應用場景 (Illustration Index)

| 編號 | 預覽 / 檔名 | 視覺動作與細節描述 | 情緒標籤 (Tags) | 敗血症研究建議適用場景 (Sepsis Use Cases) |
| :--- | :--- | :--- | :--- | :--- |
| **01** | ![汪博士打招呼微汗](./dr_wang_01_greeting_sweat.jpg)<br>`dr_wang_01_greeting_sweat.jpg` | 戴學士帽與圓眼鏡的柴犬汪博士，左手抱書、舉起右前掌熱情打招呼，臉頰帶著微紅與幾滴汗珠，尾巴開心搖擺。 | `開場導讀` `微汗苦笑` `警報疲勞` `落地困難` | 1. **Journal Club 簡報/HTML 封面與開場導讀**<br>2. **點出臨床落地現實負擔**（「這個模型每小時要抽血一次，急診護理師跟汪博士都流汗了💦」或「高假警報率造成的 Alert Fatigue」） |
| **02** | ![汪博士拿教鞭講解](./dr_wang_02_explaining_pointer.jpg)<br>`dr_wang_02_explaining_pointer.jpg` | 左手抱書、右手拿著教學指揮棒指向斜上方，臉上帶著自信燦爛的笑容，背景有公式與圖表塗鴉。 | `模型架構` `病理機轉` `指引流程` `PICO拆解` | 1. **AI 預測模型架構、特徵工程與時間窗 ($T_0$, Lead Time) 拆解**<br>2. **Surviving Sepsis Campaign 臨床處置流程與病理生理機轉導讀** |
| **03** | ![汪博士星星眼驚嘆突破](./dr_wang_03_starry_eyed_breakthrough.jpg)<br>`dr_wang_03_starry_eyed_breakthrough.jpg` | 雙眼變成閃亮大星星、張嘴驚喜大笑，雙爪捧在紅撲撲的臉頰旁，周圍充滿閃耀星光。 | `臨床珍珠` `RCT突破` `前瞻性驗證` `高價值發現` | 1. **臨床珍珠 (Clinical Pearls) 標記**<br>2. **大型前瞻性臨床試驗 (如 TREWS) 或多中心 RCT 顯著降低敗血症死亡率之重大突破** |
| **04** | ![汪博士拿放大鏡思考質疑](./dr_wang_04_thinking_magnifying_glass.jpg)<br>`dr_wang_04_thinking_magnifying_glass.jpg` | 一手拿著放大鏡貼近圓眼鏡仔細端詳、另一手托腮思考，眉頭微蹙，背景浮現問號 `?` 與發光電燈泡塗鴉。 | `方法學陷阱` `標籤洩漏` `外部驗證` `研究假設` | 1. **審視五大敗血症方法學陷阱**（$T_0$ 定義不一致、Data Leakage、低 PPV、外部驗證崩跌、回溯性偏差）<br>2. **提出新研究假設 (`wiki/ideas/`) 與 Lint 健檢抓漏** |
| **05** | ![汪博士比讚總結](./dr_wang_05_thumbs_up_approved.jpg)<br>`dr_wang_05_thumbs_up_approved.jpg` | 雙眼彎成開心的月牙狀 (`^ ^`)、露出溫暖自信笑容，左手夾著書本、右手舉起粉紅肉球比出大大的「讚」。 | `TL;DR總結` `最佳實踐` `高等級證據` `Takeaways` | 1. **單篇論文 TL;DR 與核心結論 (Key Takeaways)**<br>2. **經多中心 RCT 或 SSC 國際指引強烈推薦之臨床最佳實踐 (Best Practice)** |

---

## 日常筆記 Callout 嵌入範本 (Markdown Snippets)

### 1. 💎 臨床珍珠 (Clinical Pearls)
```markdown
> [!TIP] 💎 汪博士臨床珍珠 (Clinical Pearl)
> <img src="../../圖片/dr_wang_03_starry_eyed_breakthrough.jpg" width="135" align="right" style="border-radius: 12px; margin-left: 16px;" />
> 
> **核心洞察**：在敗血性休克復甦階段，結合微循環灌流指標（如微血管回填時間 CRT — ANDROMEDA-SHOCK 試驗）相比單純追逐 Lactate 下降，更能避免過度輸液並改善器官衰竭！
```

### 2. 🔍 方法學陷阱審視 (Methodological Pitfalls)
```markdown
> [!WARNING] 🔍 汪博士放大鏡：方法學陷阱審視 (Methodological Pitfall)
> <img src="../../圖片/dr_wang_04_thinking_magnifying_glass.jpg" width="135" align="right" style="border-radius: 12px; margin-left: 16px;" />
> 
> **注意 $T_0$ 標籤洩漏與低 PPV**：本篇雖然宣稱在 MIMIC-IV 達到 AUROC 0.91，但其輸入特徵包含了「抗生素開立紀錄與乳酸抽血頻率」，且在盛行率 5% 的急診場域下 PPV 僅有 14%（NNE = 7.1），直接落地會造成嚴重警報疲勞！
```

### 3. ⚠️ 回溯性 AI vs. 臨床指引安全防線
```markdown
> [!CAUTION] ⚠️ 醫療證據安全防線：尚未經前瞻性 RCT 證實
> <img src="../../圖片/dr_wang_01_greeting_sweat.jpg" width="135" align="right" style="border-radius: 12px; margin-left: 16px;" />
> 
> 本篇為基於回溯性觀察資料之強化學習 (RL) 模型推論，其建議之高劑量升壓藥時機與現行 **Surviving Sepsis Campaign (SSC) 指引** 存在差異，臨床決策請以高等級 RCT 與國際指引為準。
```
