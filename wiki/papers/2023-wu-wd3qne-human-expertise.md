---
title: "A value-based deep reinforcement learning model with human expertise in optimal treatment of sepsis"
type: paper
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, mimic-iii, eicu, reinforcement-learning, wd3qne, d3qn, human-expertise, random-forest, feature-selection, sofa, bun, ope]
created: 2026-11-13
updated: 2026-11-13
sources_count: 1
status: evergreen
citation: "Wu et al., 2023 [npj Digital Medicine]"
pmid_or_doi: "PMID: 36759668 / DOI: 10.1038/s41746-023-00755-5"
sepsis_criteria: "Sepsis-3 (Suspected infection + SOFA >= 2)"
setting_and_cohort: "ICU (MIMIC-III N = 17,083; External Validation eRI N = 1,500)"
raw_source: "../../raw/papers/2023-wu-wd3qne_human_expertise_npj_digit_med.pdf"
---

# A value-based deep reinforcement learning model with human expertise in optimal treatment of sepsis

> **🐾 汪博士一句話核心貢獻 (TL;DR)**：
> ![汪博士比讚](../../圖片/dr_wang_05_thumbs_up_approved.jpg)
> **針對純 RL 模型常在「輕症敗血症 ($\text{SOFA} < 5$)」亂開高劑量升壓藥的臨床痛點，提出「將人類臨床醫師專業知識嵌入加權雙重決策網路 (WD3QNE)」！** 當 $\text{SOFA} < 5$ 時直接採信臨床醫師保守安全的價值評估，當 $\text{SOFA} \ge 5$ 時才交由動態加權之 D3QN 進行最佳化，並在 MIMIC-III 與 eRI 外部驗證集完成測試。

---

## 1. 📌 文獻基本資訊與證據等級標籤

- **證據等級**：`[Retrospective 回溯性建模]`（含跨資料庫 eRI 外部回溯驗證，無前瞻性臨床試驗）
- **標準引用 (Citation)**：`Wu et al., 2023 [npj Digital Medicine]`
- **發表期刊**：*npj Digital Medicine*, 6:21 (2023)
- **作者團隊**：Xiaoli Wu, Ruxin Wang, et al. (Chinese Academy of Sciences & Shenzhen Institutes of Advanced Technology)
- **識別碼**：`PMID: 36759668` / `DOI: 10.1038/s41746-023-00755-5`
- **本地英文原檔**：[2023-wu-wd3qne_human_expertise_npj_digit_med.pdf](../../raw/papers/2023-wu-wd3qne_human_expertise_npj_digit_med.pdf)
- **跨頁關聯**：
  - AI 任務專頁：[強化學習於敗血症輸液與升壓藥決策演進](../ai-tasks-and-models/rl-fluid-vasopressor-dosing.md)
  - 評分與嚴重度分層：[SOFA 與 Sepsis-3 在強化學習中的角色](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)
  - 生物標記專頁：[強化學習關鍵特徵：BUN、Lactate、PaO2 與 PT](../biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md)

---

## 2. 🩸 敗血症定義與收案納入/排除標準 (Sepsis Criteria & Onset $T_0$)

- **診斷標準**：採用 **[Sepsis-3](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)** 定義（疑似感染 + $\text{SOFA} \ge 2$）。
- **時間窗與解析度差異**：
  - **MIMIC-III 開發集**：擷取 $T_0$ 前 24 小時至 $T_0$ 後 56 小時（共 **80 小時**），以 **4 小時** 為一時間步（共 $T = 20$ 步）。
  - **eRI 外部驗證集**：擷取 $T_0$ 前 36 小時至 $T_0$ 後 72 小時，以 **1 小時** 為一時間步（最長 $T = 80$ 步）。
- **排除標準**：
  - 年齡 $< 18$ 歲。
  - 疑似撤除維生治療 (Withdrawal of treatment) 之病患。
  - 關鍵變數連續缺失超過 24 小時之病患。

---

## 3. 🏥 收案場域與資料集來源 (Setting & Cohort)

- **收案場域**：重症加護病房 (ICU)
- **雙世代資料集**：
  1. **內部開發與測試集 ([MIMIC-III v1.4](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md))**：
     - 共 **$N = 17,083$** 名敗血症病患（共 $276,232$ 筆時間步紀錄）。
     - **90 天存活者**：$13,855$ 人；**90 天死亡者**：$3,228$ 人（基線 90 天存活率 **81.1%**，死亡率 **18.9%**）。
     - 分割：80% 訓練集、10% 驗證集、10% 測試集（測試集基線存活率 $83.26\%$）。
  2. **跨院外部測試集 (eICU Research Institute, eRI)**：
     - 隨機抽樣 **$N = 1,500$** 名敗血症病患（共 $24,279$ 筆時間步紀錄，以院內存活率為終點）。

---

## 4. 💊 臨床介入與終點指標 (PICO & Clinical Endpoints)

> [!CAUTION] ⚠️ 尚未經前瞻性 RCT 證實 — 離線評估換算之「97.81% 存活率」存在嚴重外推膨脹
> 本研究透過 Doubly Robust OPE 算出預期回報 $23.63$（滿分 $24$），並經由回報-存活率映射曲線推估測試集 90 天存活率高達 **97.81%**（死亡率僅 2.19%！）。在真實重症醫學中，敗血症包含大量不可逆之晚期惡性腫瘤、重度免疫不全與高齡多重共病患者，任何單純調整輸液與升壓藥的策略都不可能將死亡率從 18.9% 降至 2.19%。此極端數字是 **離線策略評估 (OPE) 在 Q 值逼近上限時的典型外推失真 (Extrapolation Inflation)**，引用時務必加上方法學警示！

- **P (Patient)**：成人 ICU Sepsis-3 病患（MIMIC-III $N = 17,083$；eRI $N = 1,500$）。
- **I (Intervention - WD3QNE)**：使用 Random Forest 篩選之 37 項特徵，結合動態加權 D3QN 與輕症人類專家規則 ($\text{SOFA} < 5$) 之輸液與升壓藥策略（$5 \times 5 = 25$ 離散動作）。
- **C (Comparison)**：臨床醫師處方 (Clinician)、傳統 DQN、Double DQN (DDQN)、Dueling DDQN (D3QN-45 與 D3QN-37)。
- **O (Outcome)**：Doubly Robust 預期回報與映射估計之 90 天存活率（MIMIC-III）/ 院內存活率（eRI）。

---

## 5. 🤖 AI 模型、特徵工程與完整評估指標

![汪博士講解 WD3QNE 架構](../../圖片/dr_wang_02_explaining_pointer.jpg)

### 5.1 核心創新一：Random Forest 袋外誤差 (OOB) 特徵篩選（45 項 $\to$ 37 項）
作者利用隨機森林分類器預測病患 90 天存活率，計算每項特徵的袋外重要度 (Out-of-Bag Feature Importance)，剔除重要度墊底的 8 項冗餘或高雜訊變數（如 `Gender`, `Readmission`, `Magnes`, `Calcium` 等），保留 **37 項核心特徵**。
- **最重要前 12 大特徵排序**：
  1. **[`BUN` (血中尿素氮)](../biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md)**（高居第一！）
  2. `Age` (年齡)
  3. [`SOFA`](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)
  4. `Platelets` (血小板)
  5. `WBC` (白血球)
  6. `MeanBP` (平均動脈壓)
  7. `SysBP` (收縮壓)
  8. `SpO2` (血氧飽和度)
  9. `Shock Index` (休克指數 HR/SBP)
  10. `Weight` (體重)
  11. `GCS` (昏迷指數)
  12. `Creatinine` (肌酸酐)

### 5.2 核心創新二：WD3QNE（動態加權目標值 + 輕症人類專家嵌入）
1. **平衡高估與低估的動態權重 $p$ (Weighted D3QN)**：
   - 傳統 Dueling DQN 直接取 $\max_a Q$ 容易**高估 (Overestimation)**；而標準 D3QN 用目標網路評估動作有時會產生**低估 (Underestimation)**。
   - 作者設計動態權重 $p = \frac{\varphi_{a_{t+1}}}{\varphi_{a_{t+1}} + \sigma_{a_{t+1}}}$（在訓練過程中隨步數收斂至 $0.5$），將兩者線性組合以穩定目標 Q 值。
2. **嵌入輕症臨床專家知識 ($\text{SOFA} < 5$ Human Expertise Injection)**：
   - 作者觀察到純 RL 模型在輕症敗血症（$\text{SOFA} < 5$）時，仍頻繁建議開立中高劑量升壓藥（忽略了升壓藥會造成心律不整、指端缺血等副作用，而人類醫師在 $\text{SOFA} < 5$ 時極少用升壓藥且病患存活率極高）。
   - 因此在計算目標價值時設定明確分流規則：
     - **當 $\text{SOFA} < 5$（輕症）**：強制採用模擬人類醫師策略之價值網路 $Q^{\text{clin}}(S_t, a_t^{\text{clin}})$。
     - **當 $\text{SOFA} \ge 5$（中重症）**：採用 AI 最佳化之動態加權價值網路 $Q^{\text{WD3QN}}(S_t, a_t)$。
3. **獎勵函數設計**：
   - 中間獎勵僅取決於相鄰時間步的 SOFA 變化：$R_t = \beta_s (\text{SOFA}_t - \text{SOFA}_{t+1})$，其中 $\beta_s = 0.6$。
   - 終點獎勵：**存活 $+24$**，**死亡 $-24$**（折扣因子 $\gamma = 0.99$）。

### 5.3 各模型完整離線評估指標對照表

| 模型架構 | 特徵數 | MIMIC-III 測試集預期回報 (Expected Return) | MIMIC-III 估計 90 天存活率 | eRI 外部驗證集預期回報 | eRI 外部驗證集估計存活率 |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Clinician (臨床醫師)** | — | $14.11$ | $83.26\%$ | $11.52$ | $80.12\%$ |
| **DQN** | 45 | $18.42 \pm 0.31$ | $90.15\% \pm 0.45\%$ | — | — |
| **DDQN** | 45 | $20.15 \pm 0.25$ | $92.84\% \pm 0.38\%$ | — | — |
| **D3QN-45** | 45 | $21.58 \pm 0.21$ | $94.92\% \pm 0.28\%$ | — | — |
| **D3QN-37 (特徵篩選後)** | 37 | $22.64 \pm 0.18$ | $96.48\% \pm 0.19\%$ | $20.45$ | $93.62\%$ |
| **WD3QNE (本研究最佳)** | **37** | **$23.63 \pm 0.15$** | **$97.81\% \pm 0.12\%$** | **$21.81$** | **$95.83\%$** |

- **處方改善效果**：相較於未加入人類專家的 D3QN-37，WD3QNE 在輕症 ($\text{SOFA} < 5$) 時段大幅壓低了不必要的高劑量升壓藥處方，使給藥分佈更符合臨床安全常規。
- **TRIPOD+AI 驗證成熟度**：具備跨院外部資料集驗證 (eRI $N=1,500$)，無前瞻性臨床試驗。

---

## 6. 🔍 臨床落地痛點與「汪博士放大鏡方法學審視」

![汪博士放大鏡審視](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)

1. **🔍 汪博士嚴格把關：為何會算出「97.81% 存活率」這種違反醫學常識的數字？**
   - 終點存活獎勵設為 $+24$，而 WD3QNE 算出來的 Doubly Robust 預期回報高達 **$23.63$**（幾乎等於滿分 $24$！）。
   - 為什麼會拿到 $23.63$？因為在 Doubly Robust 公式中，當 AI 策略與醫師策略不同時，重要性權重歸零，評估值直接等於 AI 自己訓練出來的 Q 網路預估值 $\widehat{V}(s_0)$。如果 Q 網路對自己的策略過度自信（預估每個病患用 AI 的藥都能拿 $+23.63$ 分），再把 $+23.63$ 代入由臨床醫師軌跡擬合的「回報 $\to$ 存活率 S 型曲線」，就會直接對應到曲線頂端的 **$97.81\%$ 存活率**！
   - 這正是後續 **[Choi et al., 2024](2024-choi-highlight-dddqn.md)** 強烈批評此類 OPE 映射曲線、並改用「實際配對劑量病患真實存活率」與「監控 Q 值高估/低估」的原因。
2. **🔍 特徵重要度的重要互證：`BUN` 與 `Age` 高居前兩名！**
   - 儘管 OPE 絕對數值膨脹，但本篇使用 Random Forest 獨立篩選出 **`BUN`（第 1 名）與 `Age`（第 2 名）** 為預測敗血症 90 天生死的最核心變數，這與 2024 年 [Choi et al., 2024](2024-choi-highlight-dddqn.md) 發現 AI 模型最仰賴 `BUN` 與 `Age` 來決定升壓藥劑量的結論不謀而合！
3. **🔍 硬切 $\text{SOFA} < 5$ 切換規則的邊界不連續問題**：
   - 當病患 SOFA 從 4 分變成 5 分時，模型從 100% 聽從人類醫師瞬間跳躍為 100% 聽從 AI 網路，缺乏平滑過渡機制（相比之下，[Peng et al., 2018](2018-peng-moe-kernel-drl.md) 的 Gating Network 或 [Nanayakkara et al., 2022](2022-nanayakkara-cardiovascular-uncertainty-drl.md) 的不確定性連續加權更為平滑）。
