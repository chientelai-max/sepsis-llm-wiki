---
title: "Deep Reinforcement Learning for Sepsis Treatment"
type: paper
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, mimic-iii, reinforcement-learning, dddqn, reward-shaping, sofa, lactate, subgroup-analysis, ope]
created: 2026-11-13
updated: 2026-11-13
sources_count: 1
status: evergreen
citation: "Raghu et al., 2017b [NeurIPS Workshop / arXiv]"
pmid_or_doi: "arXiv:1711.09602"
sepsis_criteria: "Sepsis-3 (Suspected infection + SOFA >= 2)"
setting_and_cohort: "ICU (MIMIC-III v1.4, N = 17,898)"
raw_source: "../../raw/papers/2017-raghu-drl_sepsis_treatment_nips.pdf"
---

# Deep Reinforcement Learning for Sepsis Treatment

> **🐾 汪博士一句話核心貢獻 (TL;DR)**：
> ![汪博士比讚](../../圖片/dr_wang_05_thumbs_up_approved.jpg)
> **首度將臨床生理指標（$\Delta\text{SOFA}$ 與 $\Delta\text{Lactate}$）設計為強化學習的「中間獎勵函數 (Shaped Intermediate Reward)」，並誠實揭露 DRL 模型在重度器官衰竭（$\text{SOFA} > 15$）次族群中失靈的關鍵警訊！** 這條獎勵公式成為後續數年敗血症 RL 論文（如 Lin 2023 等）最常引用的標準範本。

---

## 1. 📌 文獻基本資訊與證據等級標籤

- **證據等級**：`[Retrospective 回溯性建模]`（單中心回溯性離線強化學習建模與次族群分析）
- **標準引用 (Citation)**：`Raghu et al., 2017b [NeurIPS Workshop / arXiv]`
- **發表會議/預印本**：*NIPS (NeurIPS) 2017 Workshop on Machine Learning for Health (ML4H)* / `arXiv:1711.09602`
- **作者團隊**：Aniruddh Raghu, Matthieu Komorowski, Leo Anthony Celi, Peter Szolovits, Marzyeh Ghassemi (MIT CSAIL & Imperial College London)
- **識別碼**：`arXiv:1711.09602`
- **本地英文原檔**：[2017-raghu-drl_sepsis_treatment_nips.pdf](../../raw/papers/2017-raghu-drl_sepsis_treatment_nips.pdf)
- **跨頁關聯**：
  - 前作對照：[Raghu et al., 2017a (MLHC 稀疏獎勵與 Autoencoder 版)](2017-raghu-continuous-state-drl-mlhc.md)
  - 評分與獎勵專頁：[SOFA 與 Sepsis-3 在強化學習中的角色](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)
  - 生物標記專頁：[強化學習關鍵特徵：BUN、Lactate、PaO2 與 PT](../biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md)

---

## 2. 🩸 敗血症定義與收案納入/排除標準 (Sepsis Criteria & Onset $T_0$)

- **診斷標準**：與前作 [Raghu et al., 2017a](2017-raghu-continuous-state-drl-mlhc.md) 完全相同，採用 **[Sepsis-3](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)**（疑似感染 + $\text{SOFA} \ge 2$）。
- **發病時間點 $T_0$ 與時間窗**：抗生素或微生物培養較早時間點為 $T_0$；擷取 $T_0$ 前 24 小時至後 48 小時（共 72 小時），以 **4 小時** 為一時間步。
- **排除標準**：排除 $<15$ 歲、$T_0$ 後 $>16\text{h}$ 才入 ICU、無靜脈輸液紀錄、或單一時間窗缺失 $\ge 8$ 個變數者。

---

## 3. 🏥 收案場域與資料集來源 (Setting & Cohort)

- **收案場域**：重症加護病房 (ICU)
- **資料集來源**：[MIMIC-III v1.4](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md)
- **總樣本數 ($N$)**：$N = 17,898$ 名成人敗血症病患（存活 $15,583$ 人，院內死亡 $2,315$ 人）。
- **次族群分層 (Subgroup Stratification by Severity)**：
  - **低嚴重度 (Low SOFA)**：$\text{SOFA} < 5$（樣本占比最高，基線死亡率 $< 5\%$）
  - **中嚴重度 (Medium SOFA)**：$\text{SOFA } 5 \sim 15$（基線死亡率約 $15\% \sim 30\%$）
  - **高嚴重度 (High SOFA)**：$\text{SOFA} > 15$（樣本極稀疏，基線死亡率 $> 55\%$）

---

## 4. 💊 臨床介入與終點指標 (PICO & Clinical Endpoints)

> [!CAUTION] ⚠️ 尚未經前瞻性 RCT 證實 — 重度敗血症 (SOFA > 15) 明顯失準
> 本研究不僅為純回溯性建模，且作者在次族群分析中明確證實：**當病患處於重度多重器官衰竭（$\text{SOFA} > 15$）時，AI 模型建議之劑量與最低觀察死亡率完全脫鉤**。臨床上絕不可將此類回溯性 RL 模型用於重度敗血性休克病患之床邊決策。

- **P (Patient)**：MIMIC-III Sepsis-3 ICU 病患 ($N = 17,898$)，並依 SOFA 分為低 ($<5$)、中 ($5\sim 15$)、高 ($>15$) 三組。
- **I (Intervention)**：結合 $\Delta\text{SOFA}$ 與 $\Delta\text{Lactate}$ 中間獎勵訓練之連續狀態 **Dueling DDQN** 每 4 小時輸液與升壓藥決策（$5 \times 5 = 25$ 離散動作）。
- **C (Comparison)**：臨床醫師實際處方。
- **O (Outcome)**：院內死亡率 (In-hospital mortality) 與按嚴重度分層之劑量差異死亡率關係。

---

## 5. 🤖 AI 模型、特徵工程與完整評估指標

![汪博士講解獎勵設計](../../圖片/dr_wang_02_explaining_pointer.jpg)

### 5.1 核心方法學創新：臨床導向中間獎勵函數 (Clinically Guided Intermediate Reward)
為了克服前作僅靠終點生死獎勵 ($\pm 15$) 難以學習每 4 小時動態調整的缺點，作者設計了結合 **[SOFA 分數](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)** 與 **[血中乳酸 (Lactate)](../biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md)** 變化的短期獎勵函數：

$$r(s_t, s_{t+1}) = C_0 \mathbb{1}\left(s_{t+1}^{\text{SOFA}} = s_t^{\text{SOFA}} \land s_{t+1}^{\text{SOFA}} > 0\right) + C_1 \left(s_{t+1}^{\text{SOFA}} - s_t^{\text{SOFA}}\right) + C_2 \tanh\left(s_{t+1}^{\text{Lactate}} - s_t^{\text{Lactate}}\right)$$

- **參數設定**：$C_0 = -0.025$、$C_1 = -0.125$、$C_2 = -2$；終點時間步仍給予 **存活 $+15$ / 死亡 $-15$**。
- **臨床設計邏輯**：
  1. 若病患器官衰竭未改善且 $\text{SOFA} > 0$，給予微小懲罰 $C_0 = -0.025$。
  2. 若 $\text{SOFA}$ 上升（惡化）給予線性負獎勵，下降（改善）給予正獎勵（權重 $C_1 = -0.125$）。
  3. 若血中乳酸 (`Lactate`) 上升給予強烈負獎勵，並以 $\tanh(\cdot)$ 雙曲正切函數限制極端異常值造成的梯度爆炸（權重 $C_2 = -2$）。

### 5.2 模型架構與特徵
- **狀態空間**：48 項連續特徵（在前作 47 項特徵基礎上加入 `Timestep` 時間步序號）。
- **模型架構**：兩層 128 維隱藏層之 **Dueling Double-Deep Q-Network (Dueling DDQN)**，Leaky-ReLU、BatchNorm、Prioritized Experience Replay，目標 Q 值截斷放寬至 **$Q_{\text{thresh}} = \pm 20$**（因加入中間獎勵後累積回報範圍擴大）。

### 5.3 次族群表現與離線評估結果 (Subgroup Performance Analysis)
（本篇為強化學習策略論文，無分類任務之 AUROC/AUPRC/PPV/NPV）

| 疾病嚴重度分層 | 樣本分布特性 | AI vs. 醫師處方趨勢 | 劑量差異與實際死亡率 (U-curve) 表現 |
| :--- | :--- | :--- | :--- |
| **Low SOFA ($< 5$)** | 佔總時間步多數，死亡率極低 ($<5\%$) | AI 與醫師大多數建議少給升壓藥，但 AI 仍偶爾建議高劑量 | 由於基線死亡率過低，不同劑量差距間的死亡率曲線相當平坦 |
| **Medium SOFA ($5 \sim 15$)** | 具足夠樣本數且死亡率適中 ($15\% \sim 30\%$) | AI 建議較保守的輸液與適度升壓藥 | **呈現典型 U 型曲線**：當醫師給藥與 AI 完全一致（差距 $= 0$）時死亡率最低，偏離越大死亡率越高 |
| **High SOFA ($> 15$)** | **樣本極度稀疏**，死亡率極高 ($>55\%$) | 醫師大量使用最高劑量升壓藥 (Bin 4)，AI 亦傾向高劑量但變異極大 | **❌ U 型曲線完全瓦解！** 零差距處並非最低死亡率，曲線呈混亂鋸齒狀 |

- **TRIPOD+AI 驗證成熟度**：僅單中心內部驗證 (MIMIC-III)，無跨院外部驗證。

---

## 6. 🔍 臨床落地痛點與「汪博士放大鏡方法學審視」

![汪博士放大鏡審視](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)

1. **🔍 陷阱五（病人次族群異質性）：揭開整體平均表現下的「重度病患盲區」**：
   - 本篇最重要的學術誠實貢獻在於將 U-curve 拆開成三個 SOFA 等級。如果只看全體病患合併的圖，會誤以為 AI 在所有敗血症病患上都表現完美；但拆開後發現，整體的漂亮曲線其實是被人數最多的低/中度病患主導，而在真正最需要精準決策的 **重度器官衰竭 ($\text{SOFA} > 15$)** 病患中，因為歷史存活樣本太少，RL 模型根本學不到可靠策略！
2. **🔍 陷阱三（獎勵函數形塑偏差 Reward Shaping Bias）**：
   - 加入 $\Delta\text{SOFA}$ 與 $\Delta\text{Lactate}$ 雖然加速收斂，但也可能誘導 AI 採取「能短暫壓低指標（例如過度升壓短暫維持 MAP 以降低 SOFA 心血管分項）卻損害長期微循環或器官預後」的短視行為 (Reward Hacking)。這個疑問直接啟發了後續 [Yu & Huang, 2023](2023-yu-dirl-mt-dueling-weight-ope.md) 採用逆強化學習 (Inverse RL) 自動從資料探勘獎勵函數，以及 [Choi et al., 2024](2024-choi-highlight-dddqn.md) 開發 Highlight DDDQN 重新回歸純終點獎勵。
3. **🔍 Doubly Robust OPE 在確定性策略下的脆弱性**：
   - 作者在論文第 4.2 節正式剖析了為何 Doubly Robust (DR) 估計器在評估確定性神經網路策略時會失效：因為醫療動作空間有 25 個選項，AI 的貪婪決策在多數時間步與醫師不同，導致 Importance Weight 為 0，DR 評估幾乎退化成純粹給自己的預估價值函數 $\widehat{V}(s)$ 打分數。
