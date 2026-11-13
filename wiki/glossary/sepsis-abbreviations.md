---
title: "敗血症臨床與醫療 AI 標準縮寫暨專有名詞字典 (Sepsis Glossary)"
type: glossary
tags: [sepsis, glossary, abbreviations, clinical, medical-ai, reinforcement-learning]
created: 2026-11-13
updated: 2026-11-13
sources_count: 9
status: evergreen
---

# 敗血症臨床與醫療 AI 標準縮寫暨專有名詞字典

本字典收錄敗血症知識庫中保留英文之標準臨床、生物標記、重症資料庫與醫療 AI / 強化學習評估縮寫。每次 `Ingest` 新論文遇到新縮寫時將自動擴充。

---

## 1. 臨床診斷、評分與血液動力學指標 (Clinical Scores & Hemodynamics)

| 英文縮寫 | 英文全名 | 台灣繁體中文慣用譯名與臨床意義 |
| :--- | :--- | :--- |
| **Sepsis-3** | Third International Consensus Definitions for Sepsis and Septic Shock | 第 3 版國際敗血症與敗血性休克共識定義（疑似感染 + SOFA 急性上升 $\ge 2$ 分） |
| **SOFA** | Sequential Organ Failure Assessment | 序貫器官衰竭評估（涵蓋呼吸、凝血、肝臟、心血管、中樞神經、腎臟 6 大系統，0~24 分） |
| **qSOFA** | quick SOFA | 快速 SOFA 評分（呼吸速率 $\ge 22$/min、意識改變 GCS $< 15$、收縮壓 $\le 100$ mmHg） |
| **SIRS** | Systemic Inflammatory Response Syndrome | 全身性發炎反應症候群（Sepsis-1/2 舊版標準，敏感度高但特異度低） |
| **NEWS / NEWS2** | National Early Warning Score (2) | 英國國家早期預警評分（常作為急診與病房敗血症預測之重要 Baseline） |
| **MAP / MBP** | Mean Arterial Pressure / Mean Blood Pressure | 平均動脈壓（敗血性休克初始復甦目標通常為 $\ge 65$ mmHg） |
| **SBP / DBP (SysBP / DiaBP)** | Systolic / Diastolic Blood Pressure | 收縮壓 / 舒張壓（舒張壓反映全身血管張力與冠狀動脈灌流，為 AI 輸液決策前五大特徵） |
| **SI** | Shock Index | 休克指數（$\text{HR} / \text{SBP}$，正常約 $0.5 \sim 0.7$，升高代表潛在休克） |
| **SVR ($R$)** | Systemic Vascular Resistance | 全身血管阻力（敗血性休克因血管擴張導致 SVR 下降；[Nanayakkara 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md) 以 Windkessel 方程推估） |
| **SV / CO** | Stroke Volume / Cardiac Output | 心搏量 / 心輸出量（$\text{CO} = \text{SV} \times \text{HR}$） |
| **GCS** | Glasgow Coma Scale | 格拉斯哥昏迷指數（評估中樞神經功能，3~15 分） |
| **CRT** | Capillary Refill Time | 微血管回填時間（評估微循環灌流之重要臨床指標，如 ANDROMEDA-SHOCK 試驗） |

---

## 2. 生物標記與重症治療處置 (Biomarkers & ICU Management)

| 英文縮寫 | 英文全名 | 台灣繁體中文慣用譯名與臨床意義 |
| :--- | :--- | :--- |
| **Lactate** | Blood Lactate | 血中乳酸（反映組織低灌流與缺氧代償，敗血性休克定義需 $> 2$ mmol/L） |
| **BUN** | Blood Urea Nitrogen | 血中尿素氮（反映腎臟灌流、AKI 與蛋白質分解代謝；在 [Wu 2023](../papers/2023-wu-wd3qne-human-expertise.md) 與 [Choi 2024](../papers/2024-choi-highlight-dddqn.md) 高居 AI 決策第 1 特徵） |
| **PaO2 / FiO2** | Partial Pressure of Oxygen / Fraction of Inspired Oxygen | 動脈血氧分壓 / 吸入氧氣分率（評估呼吸衰竭與 ARDS 之核心指標；[Yu 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md) 選為 $\text{Reward}_{4.0}$） |
| **PT / INR / PTT** | Prothrombin Time / International Normalized Ratio / Partial Thromboplastin Time | 凝血酶原時間 / 國際標準化比值 / 部分凝血活酶時間（反映敗血症誘發凝血病變 SIC 與稀釋性凝血異常；[Yu 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md) 選為 $\text{Reward}_{4.0}$） |
| **PCT** | Procalcitonin | 前降鈣素（細菌感染與引導抗生素停藥之重要生物標記） |
| **CRP** | C-Reactive Protein | C-反應蛋白（非特異性發炎指標） |
| **SSC** | Surviving Sepsis Campaign | 戰勝敗血症運動（制定國際敗血症臨床治療指引之權威組織） |
| **VP** | Vasopressor | 升壓藥（常用將各類升壓藥換算為 Norepinephrine Equivalent $\mu\text{g/kg/min}$） |
| **NE** | Norepinephrine | 正腎上腺素 / 去甲腎上腺素（敗血性休克第一線首選升壓藥） |
| **IV Fluid** | Intravenous Fluid | 靜脈輸液（包含晶體液 Crystalloids 與膠體液 Colloids） |
| **MechVent** | Mechanical Ventilation | 侵襲性機械通氣（呼吸器使用） |
| **CRRT** | Continuous Renal Replacement Therapy | 連續性腎臟替代療法（敗血症合併急性腎損傷 AKI 之重症支持治療） |
| **DNR / Withdrawal** | Do Not Resuscitate / Withdrawal of Life-Sustaining Treatment | 不施行心肺復甦術 / 撤除維生治療（[Nanayakkara 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md) 證實為干擾回溯性 RL 升壓藥評估之重要混淆因子） |

---

## 3. 醫療 AI、強化學習 (RL)、離線策略評估 (OPE) 與資料庫

| 英文縮寫 | 英文全名 | 台灣繁體中文慣用譯名與方法學意義 |
| :--- | :--- | :--- |
| **MDP / POMDP** | (Partially Observable) Markov Decision Process | （部分可觀測）馬可夫決策過程（強化學習之數學基礎框架） |
| **DQN / DDQN** | (Double) Deep Q-Network | （雙重）深度 Q 網路（DDQN 分離動作選擇與評估以減緩 Q 值高估） |
| **D3QN / DDDQN** | Dueling Double-Deep Q-Network | 競爭型雙重深度 Q 網路（將 $Q(s,a)$ 拆為狀態價值 $V(s)$ 與動作優勢 $A(s,a)$，敗血症 RL 最主流基底模型） |
| **WD3QNE** | Weighted D3QN with Embedded Human Expertise | 嵌入人類專家知識之加權競爭型雙重深度 Q 網路（[Wu et al., 2023](../papers/2023-wu-wd3qne-human-expertise.md) 提出，於 $\text{SOFA}<5$ 採信臨床醫師策略） |
| **Highlight DDDQN** | Highlight Dueling Double-Deep Q-Network | 終點突顯型 DDDQN（[Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md) 提出，將中間零獎勵步之損失縮小 $1/100$ 以突顯終點生死訊號） |
| **DDPG** | Deep Deterministic Policy Gradient | 深度確定性策略梯度（Actor-Critic 架構演算法，[Lin et al., 2023](../papers/2023-lin-ddpg-dosing-strategy.md) 應用於敗血症） |
| **C51 (Distributional RL)** | Categorical Distributional Reinforcement Learning (51 atoms) | 類別分佈式強化學習（預測回報之完整機率分佈而非單一期望值，[Nanayakkara et al., 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md) 採用） |
| **IRL / BIRL / DIRL-MT** | (Bayesian / Deep) Inverse Reinforcement Learning (with Mini-Tree) | （貝氏/深度）逆強化學習（從專家存活軌跡反推獎勵函數權重，[Yu & Huang, 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md) 提出） |
| **MoE / KRL** | Mixture-of-Experts / Kernel Reinforcement Learning | 混合專家模型 / 核方法強化學習（[Peng et al., 2018](../papers/2018-peng-moe-kernel-drl.md) 結合近鄰 Kernel RL 與受限 DQN） |
| **OPE / HCOPE** | (High-Confidence) Off-Policy Evaluation | （高信賴度）離線策略評估（利用歷史行為策略資料評估新 AI 策略價值的統計方法） |
| **IS / WIS** | (Weighted) Importance Sampling | （加權）重要性抽樣（[Komorowski et al., 2018](../papers/2018-komorowski-ai-clinician.md) 採用，長軌跡下易發生權重退化） |
| **DR / WDR** | (Weighted) Doubly Robust Estimator | （加權）雙重穩健估計器（結合 IS 權重與預估價值模型 $\widehat{Q}$） |
| **DW / DWDR** | Dueling Weight (Doubly Robust) Estimator | 競爭權重（雙重穩健）估計器（[Yu & Huang, 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md) 提出，以價值比率取代連乘權重降低變異） |
| **Onset $T_0$** | Sepsis Onset Time ($T_0$) | 敗血症發病時間點（AI 模型定義時間窗與 Lead Time 的基準點） |
| **Lead Time** | Prediction Lead Time / Horizon | 預測提前時間窗（模型在 $T_0$ 發生前幾小時成功發出預警） |
| **AUROC / AUPRC** | Area Under ROC / Precision-Recall Curve | ROC / PR 曲線下面積 |
| **PPV / NPV / NNE** | Positive / Negative Predictive Value / Number Needed to Evaluate | 陽性預測值 / 陰性預測值 / 需評估人數（$=1/\text{PPV}$） |
| **TRIPOD+AI** | Transparent Reporting of a multivariable prediction model for Individual Prognosis Or Diagnosis (+AI) | 臨床預測模型與 AI 研究之國際透明報告規範 |
| **MIMIC-III / IV** | Medical Information Mart for Intensive Care | 美國麻省理工與 Beth Israel 醫院公開之大型重症加護病房電子病歷資料庫 |
| **eICU-CRD / eRI** | eICU Collaborative Research Database / Philips eICU Research Institute | 涵蓋全美多中心 ICU 之遠距重症資料庫（eICU-CRD 為公開版 208 院；eRI 為飛利浦大型完整版） |
