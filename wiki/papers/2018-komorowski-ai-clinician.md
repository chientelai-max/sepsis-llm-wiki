---
title: "The Artificial Intelligence Clinician learns optimal treatment strategies for sepsis in intensive care"
type: paper
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, mimic-iii, eicu, reinforcement-learning, ai-clinician, mdp, policy-iteration, fluid-resuscitation, vasopressor, hcope, wis, landmark-paper]
created: 2026-11-13
updated: 2026-11-13
sources_count: 1
status: evergreen
citation: "Komorowski et al., 2018 [Nature Medicine]"
pmid_or_doi: "PMID: 30349085 / DOI: 10.1038/s41591-018-0213-5"
sepsis_criteria: "Sepsis-3 (Suspected infection + SOFA >= 2)"
setting_and_cohort: "ICU (MIMIC-III N = 17,083; External Validation Philips eRI N = 79,073 across 128 hospitals)"
raw_source: "../../raw/papers/2018-komorowski-ai_clinician_nature_medicine.pdf"
---

# The Artificial Intelligence Clinician learns optimal treatment strategies for sepsis in intensive care

> **🐾 汪博士一句話核心貢獻 (TL;DR)**：
> ![汪博士星星眼驚嘆](../../圖片/dr_wang_03_starry_eyed_breakthrough.jpg)
> **敗血症強化學習領域最具代表性的《Nature Medicine》里程碑經典之作 (The AI Clinician)！** 將病患狀態聚類為 750 個離散狀態並以 Policy Iteration 求解最佳輸液與升壓藥策略，更在橫跨美國 128 家醫院、近 8 萬名病患的 **Philips eRI 外部驗證集** 完成驗證，首度提出「**相較於傳統臨床實務，更早給予低劑量升壓藥並限制靜脈輸液量**」的著名假設。

---

## 1. 📌 文獻基本資訊與證據等級標籤

- **證據等級**：`[Retrospective 回溯性建模]`（含大規模多中心跨院外部資料集回溯驗證，但無前瞻性臨床介入試驗）
- **標準引用 (Citation)**：`Komorowski et al., 2018 [Nature Medicine]`
- **發表期刊**：*Nature Medicine*, Vol. 24, pp. 1716–1720 (2018)
- **作者團隊**：Matthieu Komorowski, Leo A. Celi, Omar Badawi, Anthony C. Gordon, A. Aldo Faisal (Imperial College London, MIT, Philips Healthcare)
- **識別碼**：`PMID: 30349085` / `DOI: 10.1038/s41591-018-0213-5`
- **本地英文原檔**：[2018-komorowski-ai_clinician_nature_medicine.pdf](../../raw/papers/2018-komorowski-ai_clinician_nature_medicine.pdf)
- **跨頁關聯**：
  - AI 任務專頁：[強化學習於敗血症輸液與升壓藥決策演進](../ai-tasks-and-models/rl-fluid-vasopressor-dosing.md)
  - 臨床處置專頁：[敗血症輸液復甦與升壓藥給藥策略爭議](../clinical-management/fluid-and-vasopressor-strategy.md)
  - 資料庫專頁：[MIMIC-III 與 eICU 敗血症世代篩選比較](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md)

---

## 2. 🩸 敗血症定義與收案納入/排除標準 (Sepsis Criteria & Onset $T_0$)

- **診斷標準**：採用 **[Sepsis-3](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)** 國際共識定義（疑似感染 + 急性 $\text{SOFA} \ge 2$ 分，假設基線為 0）。
- **發病時間點 $T_0$ 與時間窗定義**：
  - **在 MIMIC-III 開發集**：先抗生素則 24h 內需有體液培養；先培養則 72h 內需有抗生素，取較早時間為 $T_0$。擷取 **$T_0$ 前 24 小時至 $T_0$ 後 48 小時（共 72 小時，每 4 小時一步）**。
  - **在 eRI 外部驗證集**：由於 eRI 缺乏完整的微生物培養紀錄，改以「入院診斷碼 (Admission Diagnosis) + 抗生素處方 + $\text{SOFA} \ge 2$」定義敗血症，並納入入院後 36 小時內確診者，擷取 **ICU 入院後最初 72 小時（每 4 小時一步）**。
- **關鍵排除標準（特別處理「撤除維生治療 / DNR」偏差）**：
  1. 年齡 $< 18$ 歲。
  2. 無靜脈輸液紀錄或核心數據大量缺失者。
  3. **排除疑似撤除維生治療 (Withdrawal of Treatment) 病患**：若病患在資料收集結束後 24 小時內死亡，且生前原本有使用升壓藥但在死亡前被完全停用，則予以排除（避免 AI 將「臨終放棄急救停用升壓藥導致死亡」誤學為「不給升壓藥會致死」）。
  4. **在 eRI 醫院層級的嚴格篩選**：原始 eRI 涵蓋 459 個 ICU（超過 330 萬筆入院），作者排除了其中 **331 個輸液或升壓藥紀錄品質不佳的 ICU**，最終保留 128 家醫院。

---

## 3. 🏥 收案場域與資料集來源 (Setting & Cohort)

- **收案場域**：重症加護病房 (ICU)
- **雙世代資料集規模**：
  1. **模型開發與內部驗證集 ([MIMIC-III v1.4](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md))**：
     - 單一醫學中心 5 個 ICU（2001–2012 年），共 **$N = 17,083$** 筆敗血症住院（來自 14,993 名獨立病患）。
     - 分割：**80% 訓練集 ($n = 13,666$)**、**20% 內部驗證集 ($n = 3,417$)**。
     - 死亡率：院內死亡率 **11.3%**，90 天死亡率 **18.9%**。
  2. **跨院外部測試集 (Philips eICU Research Institute, eRI)**：
     - 美國 **128 家醫院**（2003–2016 年），共 **$N = 79,073$** 筆敗血症 ICU 住院。
     - 死亡率：院內死亡率 **16.4%**（eRI 無出院後 90 天存活追蹤）。

---

## 4. 💊 臨床介入與終點指標 (PICO & Clinical Endpoints)

> [!CAUTION] ⚠️ 尚未經前瞻性 RCT 證實 — 與現行臨床指引存在落差
> 儘管本研究發表於《Nature Medicine》並擁有近 8 萬例外部資料驗證，但其本質仍為**回溯性觀察數據之離線強化學習 (Offline RL)**。AI Clinician 建議「大幅減少早期輸液並提早給予升壓藥」雖與近年限制性輸液趨勢（如 CLASSIC trial, CLOVERS trial）部分呼應，但離線策略評估 (WIS) 極易受未觀測混淆因子（如病患潛在心腎功能、DNR 決策、床邊超音波容積狀態）影響。臨床實務仍須遵循 [Surviving Sepsis Campaign (SSC) 2021 指引](../clinical-management/fluid-and-vasopressor-strategy.md) 進行個體化動態輸液反應性評估。

- **P (Patient)**：成人 ICU Sepsis-3 病患（MIMIC-III $N = 17,083$；eRI $N = 79,073$）。
- **I (Intervention - AI Clinician)**：基於 750 個離散狀態馬可夫決策過程 (MDP) 所推導之每 4 小時最佳輸液與升壓藥劑量組合。
- **C (Comparison - Clinician Policy)**：臨床醫師實際處方。
- **O (Outcome)**：
  - **主要終點**：MIMIC-III 為 **90 天死亡率 (90-day mortality)**；eRI 外部驗證集為 **院內死亡率 (In-hospital mortality)**。
  - **策略價值評估 (WIS Policy Value, 95% LB / IQR)**：
    - 在 eRI 外部測試集上，AI Clinician 策略價值中位數為 **$84.5$ (IQR $84.3 \sim 87.7$)**，顯著高於臨床醫師策略之 **$56.9$ (IQR $54.7 \sim 58.8$)**。
    - 當臨床醫師實際給予的劑量與 AI Clinician 建議一致（輸液誤差 $< 10\text{ mL/h}$、升壓藥誤差 $< 0.02\ \mu\text{g/kg/min}$）時，病患享有最低的 90 天與院內死亡率；偏離越大，死亡率呈劑量依賴性上升。

---

## 5. 🤖 AI 模型、特徵工程與完整評估指標

![汪博士講解 AI Clinician 架構](../../圖片/dr_wang_02_explaining_pointer.jpg)

### 5.1 48 項臨床特徵與前處理
- 每 4 小時時間窗擷取 **48 項變數**：包含人口學（Age, Gender, Weight, Readmission, Elixhauser）、生命徵象（HR, SBP, MBP, DBP, RR, Temp, SpO2）、檢驗值（Lactate, BUN, Creatinine, Bilirubin, Platelets, WBC, Hb, Glucose, 電解質、凝血功能、ABG 等）、呼吸器參數（FiO2, MechVent）與液體平衡（4h 輸入/尿量、累積平衡、SOFA、SIRS）。
- **缺失值填補**：若前向有值則採用 Sample-and-hold (LOCF)，剩餘缺失以多重線性迴歸 / kNN 插補。

### 5.2 離散狀態聚類與安全約束馬可夫決策過程 (Tabular MDP)
1. **狀態空間離散化 ($|\mathcal{S}| = 750 + 2$)**：
   - 使用 **`k-means++`** 將所有 48 維標準化生理時間步聚類為 **750 個離散臨床狀態 (Clusters)**，外加 2 個吸收終點狀態（90 天存活 / 90 天死亡）。
2. **動作空間 ($|\mathcal{A}| = 5 \times 5 = 25$)**：
   - **靜脈輸液 (IV Fluids)**：包含晶體液、膠體液與血製品（校正張力），分為 0 及非零四分位數共 5 檔（對應中位數約 $0, 30, 80, 180, 530\text{ mL/4h}$）。
   - **升壓藥 (Vasopressors)**：將 Norepinephrine, Epinephrine, Vasopressin, Dopamine, Phenylephrine 統一換算為 **Norepinephrine Equivalent ($\mu\text{g/kg/min}$)**，取每 4 小時最大速率分為 0 及非零四分位數共 5 檔（對應中位數約 $0, 0.04, 0.11, 0.22, 0.64\ \mu\text{g/kg/min}$）。
3. **罕見動作安全限制 (Safety Constraint)**：
   - 在每個狀態 $s$ 下，**僅允許選擇在歷史資料中至少出現過 $\ge 5$ 次的動作 $(s,a)$**，排除臨床醫師從未在該狀態下嘗試過的危險極端處方。
4. **獎勵與求解 (Policy Iteration)**：
   - 終點存活獎勵 $+100$，終點死亡懲罰 $-100$，折扣因子 $\gamma = 0.99$，中間轉移獎勵為 $0$。
   - 建立轉移機率矩陣 $T(s,a,s')$ 後，以動態規劃 **Policy Iteration** 精確求解最佳策略 $\pi^*$。

### 5.3 High-Confidence Off-Policy Evaluation (HCOPE) 與 500 模型集成篩選
- 由於 `k-means++` 具隨機性，作者獨立訓練了 **500 個不同的聚類與 MDP 模型**。
- **策略軟化 (Policy Softening)**：為避免確定性策略導致 Importance Sampling 權重歸零，將目標策略設為 **$99\%$ 選擇 AI 最佳動作 + $1\%$ 平均分配給其餘 24 個動作**（臨床醫師行為策略則設為 $95\%$ 經驗機率 + $5\%$ 平滑）。
- 使用 **Weighted Importance Sampling (WIS)** 搭配 **2,000 次 Bootstrapping**，選出在 MIMIC-III 內部驗證集上 **95% 信賴區間下界 (95% Lower Bound)** 最高的那一個模型作為最終 **AI Clinician**，並鎖定參數直接送入 eRI 外部測試集評估。

### 5.4 AI Clinician vs. 臨床醫師處方差異（關鍵臨床發現）

| 比較項目 | 臨床醫師 (Clinicians in eRI) | AI Clinician (最佳模型) | 臨床意涵與機轉解釋 |
| :--- | :---: | :---: | :--- |
| **升壓藥使用頻率 (VP > 0)** | **17%** 時間步 | **30%** 時間步 | AI 建議**更早、更頻繁使用低劑量升壓藥**以維持器官灌流壓 (MAP) |
| **靜脈輸液給予量** | 較高（常持續大量輸液） | **顯著較低**（休克初期後迅速減量） | 避免過度輸液造成肺水腫、組織水腫與器官灌流惡化 (Fluid Overload) |
| **eRI 外部驗證 WIS 中位價值** | $56.9$ (IQR $54.7 \sim 58.8$) | **$84.5$** (IQR $84.3 \sim 87.7$) | 跨 128 家醫院驗證顯示一致的正向策略價值提升 |
| **特徵重要度 (Random Forest 預測生死)** | — | `SOFA`, `Ventilation`, `Lactate`, `BUN`, `Creatinine`, `Platelets`, `Age` | 腎功能與組織灌流指標為驅動決策核心 |

- **TRIPOD+AI 驗證成熟度**：**具備跨資料集、跨 128 家醫院之大規模外部回溯驗證 (External Retrospective Validation)**，但尚未進行前瞻性臨床試驗。

---

## 6. 🔍 臨床落地痛點與「汪博士放大鏡方法學審視」

![汪博士放大鏡審視](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)

1. **🔍 陷阱三與 OPE 脆弱性（Gottesman et al., 2019 Nature Medicine 專文批判焦點）**：
   - 本篇發表後引發醫療 AI 界針對離線強化學習評估的經典大辯論（Gottesman et al., *Nature Medicine* 2019）：
     - 在 WIS 評估中，由於 AI 策略被軟化為 $99\%$ 貪婪 + $1\%$ 隨機，一旦某位病患在 18 個時間步中醫師有好幾步跟 AI 不同，該軌跡的累積權重就會乘上好幾個 $0.01/0.05$，幾乎變成 $0$。
     - 換句話說，**WIS 的高分其實只由極少數「從頭到尾醫師給藥剛好都跟 AI 一模一樣」的軌跡所決定**！如果這群「剛好跟 AI 一樣（少給水、給低劑量升壓藥）」的病患本身就是病情穩定或休克迅速緩解的病患，WIS 就會產生嚴重的樂觀高估。
2. **🔍 離散聚類 (750 Clusters) 的資訊流失與邊界跳躍**：
   - 將 48 維連續生理空間硬切成 750 個 Voronoi 格子，會使兩個生理數值非常接近、剛好落在聚類邊界兩側的病患被分到不同狀態，甚至得到截然不同的給藥建議；同時也完全忽略了時間序列的動態變化率（只看當下 4 小時窗，無記憶性）。
3. **🔍 4 小時時間步 (4-hour Timestep) 與床邊升壓藥滴定脫節**：
   - 在 ICU 床邊，Norepinephrine 半衰期僅數分鐘，護理師與醫師是**每幾分鐘至每小時**根據 MAP 動態調整滴速；4 小時才做一次決策且只給出一個四分位數區間，難以直接轉化為床邊輸液幫浦醫囑（此痛點後續促使 [Nanayakkara et al., 2022](2022-nanayakkara-cardiovascular-uncertainty-drl.md) 與 [Yu & Huang, 2023](2023-yu-dirl-mt-dueling-weight-ope.md) 改採 **1 小時時間步**）。
4. **🔍 外部驗證集排除了 72% (331/459) 的 ICU**：
   - 雖然 eRI 最終納入 79,073 人看似龐大，但因電子病歷輸液紀錄完整性問題排除了超過七成的醫院，代表醫療 AI 模型高度依賴高品質結構化輸入，在一般紀錄不完整的醫院直接部署會面臨嚴重的資料偏移 (Dataset Shift)。
