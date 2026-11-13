---
title: "Continuous State-Space Models for Optimal Sepsis Treatment: a Deep Reinforcement Learning Approach"
type: paper
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, mimic-iii, reinforcement-learning, dddqn, continuous-state-space, autoencoder, fluid-resuscitation, vasopressor, ope, doubly-robust]
created: 2026-11-13
updated: 2026-11-13
sources_count: 1
status: evergreen
citation: "Raghu et al., 2017a [MLHC / PMLR]"
pmid_or_doi: "PMLR 68:147-163 / arXiv:1705.08422"
sepsis_criteria: "Sepsis-3 (Suspected infection + SOFA >= 2)"
setting_and_cohort: "ICU (MIMIC-III v1.4, N = 17,898)"
raw_source: "../../raw/papers/2017-raghu-continuous_state_space_drl_mlhc.pdf"
---

# Continuous State-Space Models for Optimal Sepsis Treatment: a Deep Reinforcement Learning Approach

> **🐾 汪博士一句話核心貢獻 (TL;DR)**：
> ![汪博士比讚](../../圖片/dr_wang_05_thumbs_up_approved.jpg)
> **敗血症強化學習 (RL) 走向「連續狀態空間 (Continuous State-Space)」的開山之作！** 首度捨棄將生理數值粗糙分群 (Clustering) 的離散狀態做法，直接以 47 項原始連續生理特徵（及 Sparse Autoencoder 潛在表徵）搭配 **Dueling Double-Deep Q-Network (Dueling DDQN)** 學習輸液與升壓藥劑量，並在回溯性離線策略評估 (Doubly Robust OPE) 中估計可降低 $1.8\% \sim 3.6\%$ 院內死亡率。

---

## 1. 📌 文獻基本資訊與證據等級標籤

- **證據等級**：`[Retrospective 回溯性建模]`（單一資料庫回溯性離線強化學習建模，無前瞻性臨床介入驗證）
- **標準引用 (Citation)**：`Raghu et al., 2017a [MLHC / PMLR]`
- **發表會議/期刊**：*Proceedings of Machine Learning for Healthcare (MLHC)*, PMLR Vol. 68, pp. 147–163, 2017
- **作者團隊**：Aniruddh Raghu, Matthieu Komorowski, Imran Ahmed, Leo Celi, Peter Szolovits, Marzyeh Ghassemi (MIT CSAIL & Imperial College London)
- **識別碼**：`arXiv:1705.08422` / `PMLR 68:147-163`
- **本地英文原檔**：[2017-raghu-continuous_state_space_drl_mlhc.pdf](../../raw/papers/2017-raghu-continuous_state_space_drl_mlhc.pdf)
- **跨頁關聯**：
  - AI 任務專頁：[強化學習於敗血症輸液與升壓藥決策演進](../ai-tasks-and-models/rl-fluid-vasopressor-dosing.md)
  - 資料庫專頁：[MIMIC-III 與 eICU 敗血症世代篩選比較](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md)
  - 臨床處置專頁：[敗血症輸液復甦與升壓藥給藥策略爭議](../clinical-management/fluid-and-vasopressor-strategy.md)

---

## 2. 🩸 敗血症定義與收案納入/排除標準 (Sepsis Criteria & Onset $T_0$)

- **診斷標準**：採用 **[Sepsis-3](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)** 定義（疑似感染 Suspected Infection + 器官功能障礙 $\text{SOFA} \ge 2$ 分，假設基線 $\text{SOFA} = 0$）。
- **疑似感染與發病時間點 $T_0$ (Onset Time) 定義**：
  1. **若先開立抗生素 (Antibiotics first)**：必須在 24 小時內有微生物培養採檢 (Culture sampling)。
  2. **若先採檢微生物培養 (Culture first)**：必須在 72 小時內給予抗生素。
  3. **發病時間點 $T_0$**：取上述兩事件中**較早發生者**作為 $T_0$。
- **時間窗口擷取**：擷取 $T_0$ 前 24 小時至 $T_0$ 後 48 小時（共 **72 小時**，即 $-24\text{h} \sim +48\text{h}$），以 **4 小時 (4-hour bins)** 為一個時間步 (Timestep)。
- **排除標準**：
  - 年齡 $< 15$ 歲之病患。
  - 在 $T_0$ 後超過 16 小時才入住 ICU 之病患。
  - 整段紀錄中從未接受過靜脈輸液 (IV fluid) 之病患。
  - 缺失變數達 $\ge 8$ 個以上之時間區段（避免過度插補失真）。

---

## 3. 🏥 收案場域與資料集來源 (Setting & Cohort)

- **收案場域**：重症加護病房 (ICU)
- **資料集來源**：[MIMIC-III v1.4](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md)（美國波士頓 Beth Israel Deaconess Medical Center 單一醫學中心，2001–2012 年）
- **總樣本數 ($N$)**：$N = 17,898$ 名符合 Sepsis-3 之 ICU 病患：
  - **存活者 (Survivors)**：$15,583$ 人
  - **院內死亡者 (Non-survivors)**：$2,315$ 人（測試集基線院內死亡率 **13.7%**）

---

## 4. 💊 臨床介入與終點指標 (PICO & Clinical Endpoints)

> [!CAUTION] ⚠️ 尚未經前瞻性 RCT 證實 — 與現行臨床指引存在落差
> 本研究為純回溯性觀察資料之離線強化學習 (Offline RL) 建模，其估計之死亡率下降（$1.8\% \sim 3.6\%$）完全來自離線統計估計器 (Doubly Robust OPE) 與回溯性劑量差異曲線 (U-curve)，**並未經過任何前瞻性臨床試驗驗證**。臨床端切勿直接依此模型輸出來取代 [Surviving Sepsis Campaign (SSC) 指引](../clinical-management/fluid-and-vasopressor-strategy.md) 之床邊血液動力學評估。

- **P (Patient)**：MIMIC-III 符合 Sepsis-3 之成人 ICU 病患 ($N = 17,898$)。
- **I (Intervention - AI Policy)**：由連續狀態空間 **Normal Q-Network** 或 **Autoencode Q-Network** 每 4 小時建議之「靜脈輸液量 (IV Fluid) × 最大升壓藥劑量 (Max Vasopressor)」組合。
- **C (Comparison - Physician Policy)**：歷史病歷中臨床醫師實際開立之每 4 小時輸液與升壓藥劑量。
- **O (Outcome)**：
  - **主要終點**：院內死亡率 (In-hospital mortality)。
  - **OPE 期望回報與估計死亡率**：
    - **臨床醫師策略 (Physician Policy)**：預期回報 (Discounted Return) $= 9.87$，估計死亡率 $= 13.9\% \pm 0.5\%$（實際觀察死亡率 $13.7\%$）。
    - **Normal Q-Network (47 維原始狀態)**：預期回報 $= 10.16$，估計死亡率 $= 12.8\% \pm 0.5\%$。
    - **Autoencode Q-Network (200 維稀疏自編碼器狀態)**：預期回報 $= 10.73$，估計死亡率 $= 11.2\% \pm 0.4\%$（相較基線降低約 $1.8\% \sim 3.6\%$）。

---

## 5. 🤖 AI 模型、特徵工程與完整評估指標

![汪博士講解架構](../../圖片/dr_wang_02_explaining_pointer.jpg)

### 5.1 狀態空間與特徵工程 (State Space & Preprocessing)
- **47 項臨床特徵**（每 4 小時聚合一次平均值或總和）：
  - **人口學與基礎狀態 (5 項)**：`Age`, `Gender`, `Weight`, `Readmission`, `Elixhauser Comorbidity Score`。
  - **生命徵象與生理監測 (11 項)**：`HR`, `SysBP`, `MeanBP`, `DiaBP`, `RR`, `Temp`, `SpO2`, `FiO2`, `GCS`, `MechVent` (呼吸器使用), `Shock Index`。
  - **實驗室檢驗與血液氣體 (26 項)**：含 [`Lactate`](../biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md), [`BUN`](../biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md), `Creatinine`, `WBC`, `Platelets`, `Hemoglobin`, `Bilirubin`, `Albumin`, `Glucose`, `Potassium`, `Sodium`, `Chloride`, `Magnesium`, `Calcium`, `Ionized Calcium`, `CO2`, `SGPT`, `SGOT`, `pH`, `PaO2`, `PaCO2`, `Base Excess`, `PaO2/FiO2`, `PTT`, `PT`, `INR`。
  - **液體平衡與嚴重度評分 (5 項)**：`4-hourly Urine Output`, `Total Urine Output`, `Cumulated Fluid Balance`, `SIRS`, [`SOFA`](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)。
- **缺失值填補**：採用多重維度 **k-Nearest Neighbors (kNN)** 進行插補。
- **兩種連續狀態表徵比較**：
  1. **Normal Q-N**：直接將標準化後的 47 維連續向量輸入神經網路。
  2. **Autoencode Q-N**：訓練一個單隱層 **Sparse Autoencoder（200 個隱藏單元，L1 正則化係數 $10^{-4}$）**，提取 200 維稀疏潛在表徵 (Latent State) 作為 RL 狀態。

### 5.2 動作空間與獎勵函數 (Action Space & Reward)
- **動作空間 ($5 \times 5 = 25$ 個離散動作)**：
  - 將「每 4 小時靜脈輸液總量 (IV Fluids, mL/4h)」與「每 4 小時最大升壓藥速率 (Max Vasopressor, $\mu\text{g/kg/min}$ Norepinephrine equivalent)」分別切分為 **5 個等級**：Bin 0 為不給藥 ($0$)，Bin 1~4 為非零劑量之四分位數 (Quartiles)。
- **獎勵函數 (Sparse Terminal Reward)**：
  - 僅在軌跡最後一個時間步給予終點獎勵：**院內存活 $+15$**，**院內死亡 $-15$**；中間時間步獎勵皆為 $0$。

### 5.3 強化學習架構：Dueling Double-Deep Q-Network (Dueling DDQN)
- **Double DQN**：分離 Action Selection（Online Network $\theta$）與 Action Evaluation（Target Network $\theta'$），減緩傳統 DQN 的 Q 值過度高估 (Overestimation Bias)。
- **Dueling Network 架構**：將 Q 值拆解為「病患本身健康狀態價值 $V(s)$」與「特定治療動作相對優勢 $A(s,a)$」：
  $$Q(s, a) = V(s) + A(s, a) - \frac{1}{|\mathcal{A}|}\sum_{a'} A(s, a')$$
  這對醫療場景極為關鍵，因為許多時間步下「病患本身的基礎器官衰竭程度 $V(s)$」比單一 4 小時給藥差異更能決定生死。
- **網路超參數與穩定技巧**：2 層隱藏層（各 128 個神經元）、Batch Normalization、Leaky-ReLU、Prioritized Experience Replay (PER)，並將目標網路輸出強制截斷於 **$\pm 15$ (Q-value Clipping)** 以防止發散。

### 5.4 評估方法與完整表現指標
由於此任務為治療決策而非二元疾病預測（無 AUROC/AUPRC/PPV/NPV），作者採用兩套離線評估方法：
1. **Doubly Robust (DR) Off-Policy Evaluation (OPE)**：結合 Importance Sampling 與預估獎勵模型 $\widehat{R}(s,a)$，計算各策略之預期折扣回報，再透過 on-policy SARSA 模型建立「預期回報 $\leftrightarrow$ 觀察死亡率」校正曲線。
2. **劑量差異 vs. 實際死亡率 U 型曲線 (U-curve)**：橫軸為「醫師實際給藥減去 AI 建議給藥之差距」，縱軸為測試集中該時間步對應病患之實際院內死亡率。當差距為 $0$（醫師與 AI 一致）時觀察到最低死亡率。

| 評估策略 | 折扣預期回報 (Discounted Return) | 估計院內死亡率 (Est. Mortality) | 升壓藥處方特性 | 輸液處方特性 |
| :--- | :---: | :---: | :--- | :--- |
| **Random Policy (隨機策略)** | $-8.21$ | $79.7\% \pm 1.2\%$ | 均勻隨機 | 均勻隨機 |
| **Physician Policy (臨床醫師)** | $9.87$ | $13.9\% \pm 0.5\%$ | 多數時間不給 (Bin 0) | 集中於中低劑量 |
| **Normal Q-N (47 維連續狀態)** | $10.16$ | $12.8\% \pm 0.5\%$ | 傾向開立較高頻率升壓藥 | 集中於低劑量與高劑量兩端 |
| **Autoencode Q-N (200 維潛在狀態)** | **$10.73$** | **$11.2\% \pm 0.4\%$** | **與醫師高度相似（謹慎少用升壓藥）** | 學習效果較弱（多數建議 Bin 0，但 U-curve 顯示輸液偏離時死亡率變化不顯著） |

- **TRIPOD+AI 驗證成熟度**：**僅單中心內部訓練/測試分割 (Internal Split on MIMIC-III)**，無跨院外部驗證，無前瞻性臨床試驗。

---

## 6. 🔍 臨床落地痛點與「汪博士放大鏡方法學審視」

![汪博士放大鏡審視](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)

1. **⚠️ 陷阱一與五：U 型曲線 (U-curve) 的「依適應症混淆 (Confounding by Indication)」**：
   - 論文以「當醫師給藥與 AI 建議差距為 0 時死亡率最低」作為 AI 策略優越的證據。然而，在 Autoencode Q-N 與醫師最常給的動作都是 `Bin 0 (不給升壓藥、不給或少給輸液)`。**臨床上不需要輸液與升壓藥的病患本來就是病情最輕微、存活率最高的一群人！** 當差距很大時，往往代表病患正處於嚴重休克需要大量急救用藥，自然死亡率高。這項致命統計偏差後來被 Gottesman et al. (2018) 與後續文獻多次點名批判。
2. **⚠️ 稀疏終點獎勵 (Sparse Terminal Reward) 的歸因困難 (Credit Assignment Problem)**：
   - 整段 72 小時（18 個時間步）只有最後一步有 $\pm 15$ 獎勵，中間生理改善或惡化完全沒有訊號。這導致 Autoencode Q-N 無法有效學出精細的靜脈輸液策略。作者因此在同年底的續作（[Raghu et al., 2017b](2017-raghu-shaped-reward-drl-nips.md)）中引入了結合 `SOFA` 與 `Lactate` 的中間獎勵函數。
3. **⚠️ 確定性策略 (Deterministic Policy) 導致 Importance Sampling 退化**：
   - 當評估確定性貪婪策略 ($\pi_e(a|s) \in \{0, 1\}$) 時，只要某一步醫師動作與 AI 不同，重要性權重 $\rho_t = 0$，整條軌跡後續的真實結果都會被截斷，使 Doubly Robust 估計器退化為完全依賴近似獎勵模型 $\widehat{R}$ 的 Model-based 估計，容易產生自我印證的樂觀偏差。
4. **⚠️ 忽視跨步歷史 (Markov Assumption 破功)**：
   - 本篇僅將單一 4 小時時間窗的 47 個特徵（或 Autoencoder 表徵）當作完整狀態，假設符合馬可夫性質 (MDP)，忽略了病患過去 12~24 小時的生理變化趨勢（此問題後續由 [Peng et al., 2018](2018-peng-moe-kernel-drl.md) 與 [Nanayakkara et al., 2022](2022-nanayakkara-cardiovascular-uncertainty-drl.md) 以循環神經網路 LSTM/GRU 解決）。
