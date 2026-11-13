---
title: "敗血症 (Sepsis) 論文 LLM Wiki 知識庫總目錄"
type: index
tags: [sepsis, index, moc, clinical-ai, guidelines, reinforcement-learning]
created: 2026-11-13
updated: 2026-11-13
sources_count: 9
status: evergreen
---

# 敗血症 (Sepsis) 論文 LLM Wiki 知識庫總目錄

> [!TIP] 🐾 汪博士導覽
> <img src="../圖片/dr_wang_01_greeting_sweat.jpg" width="130" align="right" style="border-radius: 12px; margin-left: 16px;" />
> 
> 歡迎來到 **敗血症 (Sepsis) 臨床醫學 × 醫療 AI 知識庫**！
> 目前已收錄並深度編譯 **9 篇敗血症強化學習 (RL) 輸液與升壓藥決策論文（2017–2024）**，共建立 **18 個高度互連之知識頁面**。每篇論文皆已完成 **證據等級**、**$T_0$ 定義**、**離線評估 (OPE) 指標** 與 **五大方法學陷阱** 審視！

---

## 1. 跨文獻綜合比較與方法學審視 (`wiki/synthesis/`)

| 頁面連結 | 一句話核心摘要 | 涵蓋文獻數 | 最後更新 |
| :--- | :--- | :---: | :---: |
| [敗血症跨文獻證據大表與方法學陷阱對照矩陣](./synthesis/sepsis-evidence-matrix.md) | 彙整 9 篇敗血症 RL 論文之世代規模、MDP 設計、OPE 指標與五大方法學陷阱（DNR 混淆、OPE 膨脹、高齡肺水腫風險）總表 | 9 | 2026-11-13 |
| [LLM 與醫療 AI 在醫學及工程教學上的核心注意事項](./synthesis/llm-and-rl-teaching-guide.md) | 以 9 篇敗血症 RL 論文為實證教材，整理教學必講之 6 大防誤導守則（拆穿 97.81% 存活率神話、DNR 反向因果、高齡肺水腫安全防線）與互動式 HTML 投影片連結 | 9 | 2026-11-13 |

---

## 2. 單篇論文與臨床指引深度解析 (`wiki/papers/`)

### 2.1 強化學習於敗血症輸液與升壓藥決策系列 (RL for Fluid & Vasopressor Dosing, 9 篇)

| 論文筆記連結 | 標準 Citation | 證據等級 | 資料集與樣本數 ($N$) | 汪博士一句話核心貢獻 (TL;DR) |
| :--- | :--- | :---: | :--- | :--- |
| [2017-raghu-continuous-state-drl-mlhc.md](./papers/2017-raghu-continuous-state-drl-mlhc.md) | `Raghu et al., 2017a [MLHC]` | `[Retrospective 回溯性建模]` | MIMIC-III ($N=17,898$) | 首度以 47 維連續狀態與 200 維 Sparse Autoencoder 搭配 Dueling DDQN 學習敗血症給藥，DR 估計死亡率降至 $11.2\%$。 |
| [2017-raghu-shaped-reward-drl-nips.md](./papers/2017-raghu-shaped-reward-drl-nips.md) | `Raghu et al., 2017b [NeurIPS ML4H]` | `[Retrospective 回溯性建模]` | MIMIC-III ($N=17,898$) | 首度設計 $\Delta\text{SOFA} + \tanh(\Delta\text{Lactate})$ 中間獎勵函數，並揭露 RL 在重度器官衰竭 ($\text{SOFA}>15$) 次族群失靈。 |
| [2018-komorowski-ai-clinician.md](./papers/2018-komorowski-ai-clinician.md) | **`Komorowski et al., 2018 [Nature Medicine]`** ⭐ | `[Retrospective 回溯性建模]` | MIMIC-III ($N=17,083$)<br>**外部：eRI ($N=79,073$)** | **里程碑經典 (AI Clinician)**：以 750 離散狀態 MDP 於跨 128 院 eRI 驗證，提出「減少輸液、提早使用低劑量升壓藥」策略。 |
| [2018-peng-moe-kernel-drl.md](./papers/2018-peng-moe-kernel-drl.md) | `Peng et al., 2018 [AMIA]` | `[Retrospective 回溯性建模]` | MIMIC-III ($N=15,415$) | 以 LSTM Autoencoder 編碼病史記憶 (POMDP)，並以 MoE 結合 300 近鄰 Kernel RL 與受限 DDQN，防止純 DQN 開出 3.8 倍極端劑量。 |
| [2022-nanayakkara-cardiovascular-uncertainty-drl.md](./papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md) | `Nanayakkara et al., 2022 [PLOS Digit Health]` | `[Retrospective 回溯性建模]` | MIMIC-III ($\Delta t = 1\text{h}$) | 將雙元素 Windkessel 心血管微分方程嵌入 GRU 與 C51 分佈式 RL，並揭穿「死前 10h 醫師因 DNR 停用升壓藥」造成的評估混淆。 |
| [2023-yu-dirl-mt-dueling-weight-ope.md](./papers/2023-yu-dirl-mt-dueling-weight-ope.md) | `Yu & Huang, 2023 [BMC Med Inform]` | `[Retrospective 回溯性建模]` | MIMIC-III ($N=14,012$, $\Delta t=1\text{h}$) | 以 Mini-Tree 挖掘出 `PaO2` 與 `PT` 作為第四代獎勵指標結合貝氏逆強化學習 (DIRL-MT)，並提出低變異之 Dueling Weight OPE。 |
| [2023-lin-ddpg-dosing-strategy.md](./papers/2023-lin-ddpg-dosing-strategy.md) | `Lin et al., 2023 [BMC Med Inform]` | `[Retrospective 回溯性建模]` | MIMIC-III (篩選 38,600 人) | 將 Actor-Critic DDPG 引入敗血症給藥，收斂速度提升 10 倍，但仍將輸出離散化為 25 格且依賴具混淆偏差之 U-curve 評估。 |
| [2023-wu-wd3qne-human-expertise.md](./papers/2023-wu-wd3qne-human-expertise.md) | `Wu et al., 2023 [npj Digital Medicine]` | `[Retrospective 回溯性建模]` | MIMIC-III ($N=17,083$)<br>**外部：eRI ($N=1,500$)** | 以 RF 篩選 37 特徵（`BUN`, `Age` 居首），提出 WD3QNE 於輕症 ($\text{SOFA}<5$) 嵌入人類醫師保守價值以減少升壓藥濫用。 |
| [2024-choi-highlight-dddqn.md](./papers/2024-choi-highlight-dddqn.md) | `Choi et al., 2024 [Commun Med]` | `[Retrospective 回溯性建模]` | MIMIC-III ($N=20,927$)<br>**外部：eICU ($N=14,875$)** | 提出 Highlight DDDQN（抑制中間零獎勵步雜訊 $1/100$），深入剖析 AI 依賴高 `BUN`/`Age` 提前給藥之機轉與高齡肺水腫風險。 |

---

## 3. 臨床評分與診斷標準 (`wiki/scoring-and-criteria/`)

| 頁面連結 | 一句話核心摘要 | 涵蓋文獻數 | 最後更新 |
| :--- | :--- | :---: | :---: |
| [SOFA 與 Sepsis-3 在強化學習中的雙重角色](./scoring-and-criteria/sofa-and-sepsis3-in-rl.md) | 剖析 Sepsis-3 發病時間點 $T_0$ 操作定義、$-24\text{h}$ 預知偏差、$\Delta\text{SOFA}$ 中間獎勵衝突，以及 $\text{SOFA}<5$ / $5\sim 15$ / $>15$ 三大次族群表現差異 | 9 | 2026-11-13 |

---

## 4. 預測特徵與生物標記 (`wiki/biomarkers-and-features/`)

| 頁面連結 | 一句話核心摘要 | 涵蓋文獻數 | 最後更新 |
| :--- | :--- | :---: | :---: |
| [敗血症強化學習關鍵特徵：BUN、Lactate、PaO2、PT 與心血管參數](./biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md) | 跨篇比較特徵重要度排行榜：揭曉為何 `BUN` 與 `Age` 高居 AI 升壓藥決策榜首、`PaO2` 與 `PT` 如何煞住過度輸液，以及 `DiaBP` 與 Windkessel `SVR` 的角色 | 9 | 2026-11-13 |

---

## 5. 醫療資料庫與世代研究 (`wiki/cohorts-and-databases/`)

| 頁面連結 | 一句話核心摘要 | 涵蓋文獻數 | 最後更新 |
| :--- | :--- | :---: | :---: |
| [MIMIC-III 與 eICU 敗血症世代篩選與前處理跨篇比較](./cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md) | 解密為何同樣用 MIMIC-III 樣本數卻從 14,012 到 20,927 人不等，對比 1h vs. 4h 時間步、DNR 排除規則與 eRI/eICU 跨院驗證特性 | 9 | 2026-11-13 |

---

## 6. AI 演算法與臨床任務 (`wiki/ai-tasks-and-models/`)

| 頁面連結 | 一句話核心摘要 | 涵蓋文獻數 | 最後更新 |
| :--- | :--- | :---: | :---: |
| [強化學習於敗血症輸液與升壓藥決策之演進](./ai-tasks-and-models/rl-fluid-vasopressor-dosing.md) | 綜整 2017–2024 年三代敗血症 RL 架構演進圖譜、MDP 五要素對照矩陣、OOD 動作抑制機制，以及 WIS/DR/WDR/DW/U-curve 五大 OPE 評估優劣剖析 | 9 | 2026-11-13 |

---

## 7. 臨床治療策略與爭議 (`wiki/clinical-management/`)

| 頁面連結 | 一句話核心摘要 | 涵蓋文獻數 | 最後更新 |
| :--- | :--- | :---: | :---: |
| [敗血症輸液復甦與升壓藥給藥策略：AI 建議 vs. 臨床指引](./clinical-management/fluid-and-vasopressor-strategy.md) | 對比 AI「限制輸液、提早積極升壓」建議與 SSC 2021 指引/CLASSIC/CLOVERS 試驗，並詳列四大臨床安全盲區（高齡高 BUN 肺水腫、輕症過度升壓、DNR 反向因果） | 9 | 2026-11-13 |

---

## 8. 縮寫字典、研究靈感與視覺素材 (`wiki/glossary/` & `wiki/ideas/`)
- [敗血症臨床與 AI 標準縮寫暨專有名詞字典](./glossary/sepsis-abbreviations.md) — 收錄常用重症醫學、生物標記 (`BUN`, `Lactate`, `PaO2`, `PT`) 與強化學習 (`MDP`, `D3QN`, `WD3QNE`, `DIRL-MT`, `OPE`, `WIS`, `WDR`, `DWDR`) 核心縮寫。
- [敗血症強化學習研究缺口與 5 大新研究題目靈感庫](./ideas/rl-sepsis-research-gaps-and-ideas.md) — 從 9 篇論文 Limitations 提煉出「DNR 因果校正 RL」、「預防高齡肺水腫之 Safe CMDP」、「結合 Diastolic Shock Index 之連續滴定」等 5 個高潛力研究題目。
- [汪博士 (Dr. Wang) 插圖素材索引表](../圖片/dr-wang-catalog.md) — 5 款汪博士情緒動作與臨床珍珠/方法學陷阱嵌入語法。

---

## 📡 敗血症必讀基石文獻雷達 (Landmark Papers Radar — 供 Lint 追蹤)

以下為本知識庫追蹤之敗血症臨床與 AI 經典文獻清單（消化後將自動打勾並連結至對應筆記）：
- [ ] **[Guideline]** *Surviving Sepsis Campaign: International Guidelines for Management of Sepsis and Septic Shock 2021* (`Evans et al., 2021 [Intensive Care Med / Crit Care Med]`)
- [ ] **[Consensus]** *The Third International Consensus Definitions for Sepsis and Septic Shock (Sepsis-3)* (`Singer et al., 2016 [JAMA]`)
- [ ] **[Validation]** *Assessment of Clinical Criteria for Sepsis (SOFA / qSOFA)* (`Seymour et al., 2016 [JAMA]`)
- [ ] **[Phenotyping]** *Derivation, Validation, and Potential Treatment Implications of Novel Clinical Phenotypes for Sepsis (SENECA α, β, γ, δ)* (`Seymour et al., 2019 [JAMA]`)
- [ ] **[External Validation]** *External Validation of a Widely Implemented Proprietary Sepsis Prediction Model in Hospitalized Patients (Epic Sepsis Model)* (`Wong et al., 2021 [JAMA Intern Med]`)
- [ ] **[Prospective Trial]** *Prospective, Multi-site Study of Patient Outcomes After Implementation of the TREWS Machine Learning-Based Early Warning System for Sepsis* (`Adams et al., 2022 [Nature Medicine]`)
- [x] **[RL Treatment]** *The Artificial Intelligence Clinician Learns Optimal Treatment Strategies for Sepsis in Intensive Care* (`Komorowski et al., 2018 [Nature Medicine]`) — [已消化深度筆記](./papers/2018-komorowski-ai-clinician.md)
