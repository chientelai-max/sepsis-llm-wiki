---
title: "SOFA 與 Sepsis-3 在強化學習中的雙重角色：收案定義、中間獎勵與次族群分層"
type: scoring
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, sepsis-3, sofa, qsofa, sirs, onset-t0, reward-shaping, subgroup-analysis]
created: 2026-11-13
updated: 2026-11-13
sources_count: 9
status: evergreen
---

# SOFA 與 Sepsis-3 在強化學習中的雙重角色：收案定義、中間獎勵與次族群分層

> **🐾 汪博士評分系統解析**：
> ![汪博士拿教鞭講解](../../圖片/dr_wang_02_explaining_pointer.jpg)
> 在敗血症強化學習文獻中，**SOFA (Sequential Organ Failure Assessment)** 不只是用來定義 **[Sepsis-3](../papers/2018-komorowski-ai-clinician.md)** 收案門檻（$\text{SOFA} \ge 2$）的診斷標準，更同時扮演了 **「AI 狀態空間的核心特徵」**、**「形塑中間獎勵函數 (Reward Shaping) 的靶心」** 以及 **「區分輕中重症次族群邊界 ($\text{SOFA} < 5$ vs. $> 15$)」** 的多重角色！

---

## 1. Sepsis-3 收案與發病時間點 $T_0$ (Sepsis Onset Time) 的標準操作定義

在 [Raghu et al., 2017a](../papers/2017-raghu-continuous-state-drl-mlhc.md) 與 [Komorowski et al., 2018](../papers/2018-komorowski-ai-clinician.md) 建立的標準流水線中（後續 7 篇論文皆沿用此框架）：

1. **疑似感染 (Suspected Infection) 時間窗匹配規則**：
   - **狀況 A（先開立抗生素 Antibiotics $\to$ 後採檢培養 Culture）**：微生物培養必須在抗生素給藥後 **24 小時內** 完成採檢。
   - **狀況 B（先採檢培養 Culture $\to$ 後開立抗生素 Antibiotics）**：抗生素必須在微生物培養採檢後 **72 小時內** 給予。
   - **發病時間點 $T_0$**：定義為上述配對中「抗生素給藥」或「微生物培養採檢」**較早發生的時間點**。
2. **急性器官功能障礙 ($\Delta\text{SOFA} \ge 2$)**：
   - 在 $T_0$ 前後時間窗（通常為 $T_0 - 48\text{h}$ 至 $T_0 + 24\text{h}$）內，總 **SOFA 分數 $\ge 2$ 分**（遵循 Sepsis-3 共識，若未知病患入院前基線狀態，假設基線 $\text{SOFA} = 0$）。
3. **軌跡擷取範圍**：
   - 標準做法擷取 **$T_0$ 前 24 小時至 $T_0$ 後 48 小時（共 72 小時）**，或延伸至 **80 小時**（[Wu 2023](../papers/2023-wu-wd3qne-human-expertise.md), [Choi 2024](../papers/2024-choi-highlight-dddqn.md)）。

> [!WARNING] 方法學陷阱一：$T_0$ 前 24 小時的「回溯性預知偏差 (Hindsight Bias)」
> 請注意！將軌跡起點設在 **$T_0 - 24\text{h}$** 意味著模型在 $T_0$ 尚未發生前的 24 小時（6 個時間步）就已經知道「這位病患在 24 小時後會確診 Sepsis-3」！這在回溯性分析治療軌跡時很常見，但若要在真實臨床部署這套系統，在 $T_0 - 24\text{h}$ 當下醫師與系統根本還不知道病患是否會成為 Sepsis-3 個案。

---

## 2. SOFA 作為強化學習「中間獎勵函數 (Intermediate Reward)」的演進與爭議

| 獎勵設計流派 | 代表論文 | SOFA 在獎勵函數中的數學形式 | 優點與潛在風險 |
| :--- | :--- | :--- | :--- |
| **不使用 SOFA 作為中間獎勵（純終點生死獎勵）** | [Raghu 2017a](../papers/2017-raghu-continuous-state-drl-mlhc.md)<br>[Komorowski 2018](../papers/2018-komorowski-ai-clinician.md)<br>[Nanayakkara 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md)<br>[Choi 2024](../papers/2024-choi-highlight-dddqn.md) | $r_t = 0 \quad (\forall t < T)$ | **優點**：直接對齊最終臨床目標（90天/院內存活），避免人為指標誘導短視行為。<br>**風險**：訊號稀疏（需搭配 Policy Iteration 或 [Choi 2024](../papers/2024-choi-highlight-dddqn.md) 的 Highlight DDDQN $\frac{1}{100}$ 降權技巧）。 |
| **$\Delta\text{SOFA} + \Delta\text{Lactate}$ 複合中間獎勵** | [Raghu 2017b](../papers/2017-raghu-shaped-reward-drl-nips.md)<br>[Lin 2023](../papers/2023-lin-ddpg-dosing-strategy.md) | $-0.025\mathbb{1}(\Delta\text{SOFA}=0 \land \text{SOFA}>0) - 0.125\Delta\text{SOFA} - 2\tanh(\Delta\text{Lactate})$ | **優點**：提供每 4 小時的即時器官改善回饋。<br>**風險**：**SOFA 心血管分項本身就包含「升壓藥劑量」！** 若給升壓藥會直接讓 SOFA 心血管分數增加 2~4 分，可能與維持 MAP 的目標產生內部獎勵衝突。 |
| **純 $\Delta\text{SOFA}$ 線性中間獎勵** | [Wu 2023](../papers/2023-wu-wd3qne-human-expertise.md) | $R_t = 0.6 \times (\text{SOFA}_t - \text{SOFA}_{t+1})$ | **優點**：公式簡潔，直接獎勵降低 SOFA。<br>**風險**：在輕症 $\text{SOFA}<5$ 時模型仍會過度開立升壓藥，迫使作者外掛人類專家規則。 |
| **結合 SOFA、qSOFA 與休克/凝血指標之逆強化學習** | [Yu & Huang 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md) | 將 `SOFA` + `qSOFA` 定義為 $\text{Reward}_{3.0}$，與 `MAP`/`Lactate`/`PaO2`/`PT` 比較 | **關鍵發現**：單用 `SOFA` + `qSOFA` ($\text{Reward}_{3.0}$) 的策略表現最差（估計死亡率 $13.4\%$），反而是直接結合 **`MAP` + `Lactate` + `PaO2` + `PT`** 不依賴綜合評分時表現最佳（估計死亡率 $11.3\%$）！ |

---

## 3. 按 SOFA 嚴重度分層的次族群表現（為何 $\text{SOFA} < 5$ 與 $\text{SOFA} > 15$ 是兩大禁區？）

![汪博士放大鏡審視](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)

綜合 [Raghu et al., 2017b](../papers/2017-raghu-shaped-reward-drl-nips.md)、[Wu et al., 2023](../papers/2023-wu-wd3qne-human-expertise.md) 與 [Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md) 的實證數據，強化學習模型在三個 SOFA 區間展現出截然不同的可靠性：

1. **🟢 中度器官衰竭（$\text{SOFA } 5 \sim 15$）：RL 模型的核心甜蜜點**
   - 擁有充足的存活與死亡歷史對照樣本，且病患對輸液與升壓藥調整具備生理反應空間。U 型曲線在此區間最為顯著（[Raghu et al., 2017b](../papers/2017-raghu-shaped-reward-drl-nips.md)）。
2. **🟡 輕度器官衰竭（$\text{SOFA} < 5$）：AI 容易「過度醫療 (Overtreatment)」**
   - 此區間病患基線死亡率 $< 5\%$，臨床醫師極少給升壓藥。但純深度強化學習模型（如標準 D3QN 或 Highlight DDDQN）常因預期未來風險，在此區間過早建議高劑量升壓藥或輸液。[Wu et al., 2023](../papers/2023-wu-wd3qne-human-expertise.md) 證明在此區間直接遵循人類醫師保守策略 ($Q^{\text{clin}}$) 更加安全。
3. **🔴 極重度多重器官衰竭（$\text{SOFA} > 15$）：資料稀疏與不可逆衰竭的 AI 盲區**
   - [Raghu et al., 2017b](../papers/2017-raghu-shaped-reward-drl-nips.md) 證明在 $\text{SOFA} > 15$ 時，AI 建議劑量與最低死亡率完全無關（U 型曲線崩解），因為此階段病患多數已使用最大劑量升壓藥仍無法逆轉多重器官衰竭，且伴隨高比例 DNR 撤除治療決策。
