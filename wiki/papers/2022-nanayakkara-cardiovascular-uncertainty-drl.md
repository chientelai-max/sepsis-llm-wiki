---
title: "Unifying cardiovascular modelling with deep reinforcement learning for uncertainty aware control of sepsis treatment"
type: paper
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, mimic-iii, reinforcement-learning, cardiovascular-modeling, windkessel, distributional-rl, c51, uncertainty, gru, human-in-the-loop, dnr-confounding]
created: 2026-11-13
updated: 2026-11-13
sources_count: 1
status: evergreen
citation: "Nanayakkara et al., 2022 [PLOS Digital Health]"
pmid_or_doi: "PMID: 36812516 / DOI: 10.1371/journal.pdig.0000012"
sepsis_criteria: "Sepsis-3 (Suspected infection + SOFA >= 2)"
setting_and_cohort: "ICU (MIMIC-III v1.4, 1-hour resolution)"
raw_source: "../../raw/papers/2022-nanayakkara-cardiovascular_uncertainty_drl_plos.pdf"
---

# Unifying cardiovascular modelling with deep reinforcement learning for uncertainty aware control of sepsis treatment

> **🐾 汪博士一句話核心貢獻 (TL;DR)**：
> ![汪博士星星眼驚嘆](../../圖片/dr_wang_03_starry_eyed_breakthrough.jpg)
> **將「心血管生理微分方程 (Two-Element Windkessel Model)」直接嵌入深度強化學習的物理知情 (Physics-Informed) 代表作！** 首度改採貼近床邊滴定速度的 **1 小時時間步** 與 **$3 \times 3 = 9$ 動作空間**，從動脈血壓逆推無法直接測量的「全身血管阻力 (SVR / $R$)」與「心搏量 (Stroke Volume, $SV$)」，並結合 **分佈式強化學習 (Distributional RL, C51) + 深度集成不確定性量化**，更一針見血揭穿了「臨終前 10 小時醫師停用升壓藥 (DNR) 造成 AI 與醫師決策巨大分歧」的真相！

---

## 1. 📌 文獻基本資訊與證據等級標籤

- **證據等級**：`[Retrospective 回溯性建模]`（結合心血管生理機制建模與不確定性感知之回溯性離線強化學習）
- **標準引用 (Citation)**：`Nanayakkara et al., 2022 [PLOS Digital Health]`
- **發表期刊**：*PLOS Digital Health*, 1(2): e0000012 (2022)
- **作者團隊**：Thesath Nanayakkara, Aryaman Gupta, Omer Gottesman, Marzyeh Ghassemi,oche, et al. (University of Melbourne & MIT)
- **識別碼**：`PMID: 36812516` / `DOI: 10.1371/journal.pdig.0000012`
- **本地英文原檔**：[2022-nanayakkara-cardiovascular_uncertainty_drl_plos.pdf](../../raw/papers/2022-nanayakkara-cardiovascular_uncertainty_drl_plos.pdf)
- **跨頁關聯**：
  - AI 任務專頁：[強化學習於敗血症輸液與升壓藥決策演進](../ai-tasks-and-models/rl-fluid-vasopressor-dosing.md)
  - 臨床處置專頁：[敗血症輸液復甦與升壓藥給藥策略爭議](../clinical-management/fluid-and-vasopressor-strategy.md)
  - 生物標記與特徵專頁：[強化學習關鍵特徵：BUN、Lactate、PaO2 與 PT](../biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md)

---

## 2. 🩸 敗血症定義與收案納入/排除標準 (Sepsis Criteria & Onset $T_0$)

- **診斷標準**：採用 **[Sepsis-3](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)** 定義（疑似感染 + $\text{SOFA} \ge 2$）。
- **關鍵時間解析度革新：改採 1 小時時間步 (1-Hour Timestep)**：
  - 過去研究（如 [Raghu 2017](2017-raghu-continuous-state-drl-mlhc.md)、[Komorowski 2018](2018-komorowski-ai-clinician.md)）皆使用 4 小時時間窗，但敗血性休克病患的血管張力與血壓在數十分鐘內即劇烈波動。本研究將時間解析度提升至 **每 1 小時一步**。
- **排除標準**：
  1. 年齡 $< 17$ 歲。
  2. 資料缺失率 $> 25\%$ 之病患。
  3. **排除「從 ICU 轉出至一般病房後才在院內死亡」之病患**：因為這類病患在轉出 ICU 時生命徵象看似穩定，若標記為死亡會嚴重誤導 ICU 階段的回報歸因。

---

## 3. 🏥 收案場域與資料集來源 (Setting & Cohort)

- **收案場域**：重症加護病房 (ICU)
- **資料集來源**：[MIMIC-III v1.4](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md)（70% 訓練集、15% 驗證集、15% 測試集）。
- **輸入特徵維度**：共 **41 項臨床變數**（拆分為每小時高頻更新的生命徵象與每 12 小時稀疏採檢的實驗室檢驗值）。

---

## 4. 💊 臨床介入與終點指標 (PICO & Clinical Endpoints)

> [!CAUTION] ⚠️ 尚未經前瞻性 RCT 證實 — 臨終撤除治療 (DNR) 嚴重干擾回溯性升壓藥評估
> 本研究透過時間軸拆解發現：在死亡病患過世前的最後 10 小時，臨床醫師使用升壓藥的比例急劇下降（反映家屬簽署 DNR 或撤除無效維生醫療），而只以「存活」為目標的 AI 則在 $70\% \sim 80\%$ 的瀕死狀態下強烈建議給予高劑量升壓藥。這證明**過去回溯性 RL 論文宣稱「AI 比醫師更積極給升壓藥能救命」，很大一部分其實是把「醫師臨終停藥」誤判為「醫師失誤未給藥」！**

- **P (Patient)**：MIMIC-III 成人 Sepsis-3 ICU 病患（1 小時解析度）。
- **I (Intervention - Uncertainty-Aware Physics-Guided DRL)**：結合 Windkessel 心血管潛在狀態、C51 分佈式強化學習、行為複製 (Behavior Cloning) 與認知不確定性懲罰之 **人機協作偏好分數 $\mathcal{P}(s,a)$**，每 1 小時推薦 9 種輸液/升壓藥組合之一。
- **C (Comparison)**：臨床醫師每小時實際處方。
- **O (Outcome)**：ICU 存活率、心血管生理狀態一致性（如低 SVR 與低 MAP 時之升壓藥反應），以及模型對未知狀態的不確定性預警能力。

---

## 5. 🤖 AI 模型、特徵工程與完整評估指標

![汪博士講解心血管物理融合架構](../../圖片/dr_wang_02_explaining_pointer.jpg)

### 5.1 核心創新一：結合雙元素 Windkessel 心血管微分方程的自編碼器
在敗血性休克中，決定要給「輸液（增加前負荷與心搏量 $SV$）」還是「升壓藥（增加全身血管阻力 $R$ / SVR）」的關鍵在於背後的血液動力學參數，但一般病患並未放置肺動脈導管 (Swan-Ganz) 測量 $SV$ 與 $R$。
作者設計了一個 **Physiology-Driven Recurrent Autoencoder (GRU)**：
1. 由高頻生命徵象與過去用藥序列，透過神經網路推估 4 個隱藏心血管生理參數：
   - **$R$**：全身血管阻力 (Systemic Vascular Resistance, SVR)
   - **$C$**：動脈順應性 (Arterial Compliance)
   - **$SV$**：心搏量 (Stroke Volume)，心輸出量 $\text{CO} = SV \times F$（$F$ 為心率 HR）
   - **$T$**：心室充盈時間 (Filling Time)
2. 將這 4 個潛在生理參數代入 **Two-Element Windkessel 心血管物理方程**，強制重現觀測到的收縮壓 ($P_{\text{sys}}$)、舒張壓 ($P_{\text{dias}}$)、平均動脈壓 ($P_{\text{MAP}}$) 與脈壓 ($P_{\text{pulse}}$)：
   $$P_{\text{MAP}} = \frac{(SV) \cdot F \cdot R}{60}$$
3. 同時使用另一個 **Denoising GRU Autoencoder** 編碼過去 12 小時的稀疏檢驗值，提取 10 維潛在實驗室狀態 $l_1 \dots l_{10}$。

### 5.2 核心創新二：$3 \times 3 = 9$ 動作空間與人機協作不確定性控制
- **為何從 25 個動作縮減為 $3 \times 3 = 9$ 個動作？**
  - 當時間步縮短為 **1 小時** 時，若仍維持 $5 \times 5 = 25$ 個動作，許多動作組合在歷史資料中極度稀疏。因此作者將每小時輸液與升壓藥各分為 **3 個臨床有意義的等級**：
    - **靜脈輸液 (IV Fluids)**：$0\text{ mL/h}$（無）、$1 \sim 500\text{ mL/h}$（中低量）、$> 500\text{ mL/h}$（大量輸液復甦）。
    - **升壓藥 (Vasopressors, NE-equiv)**：$0\ \mu\text{g/kg/min}$（無）、$0.001 \sim 0.15\ \mu\text{g/kg/min}$（中低劑量）、$> 0.15\ \mu\text{g/kg/min}$（高劑量）。
- **Categorical Distributional RL (C51) + 25 個 Bootstrap Deep Ensembles**：
  - 不只預測單一期望 Q 值，而是預測回報的完整機率分佈 $Z(s,a)$（捕捉醫療結果的內在隨機性 **Aleatoric Uncertainty**）。
  - 訓練 25 個獨立的 Bootstrap C51 網路，以各網路預測分佈間的 **KL Divergence** 量化模型對該狀態-動作的未知程度（**認知不確定性 Epistemic Uncertainty $u(s,a)$**）。
- **人機協作偏好分數 (Human-in-the-Loop Preference Score)**：
  $$\mathcal{P}(s,a) = \beta \text{Softmax}\left(\tilde{Q}^*(s,a)\right) + (1 - \beta) G(s,a) - \lambda u(s,a)$$
  其中 $G(s,a)$ 為模仿醫師處方的 Behavior Cloner 機率。當某動作的不確定性 $u(s,a)$ 過高時，自動予以懲罰並退回臨床醫師熟悉的安全處方！

### 5.3 特徵重要度與臨床行為解析
- **排列特徵重要度 (Permutation Feature Importance) 前 10 名**：
  1. `CNS (GCS)`、2. `BUN`、3. `SOFA`、4. `HR`、5. `Hemoglobin (HB)`、6. 潛在檢驗表徵 `l_3`、7. **Windkessel 血管阻力 `R` (SVR)**、8. **Windkessel 心搏量 `SV`**、9. `Temp`、10. `SpO2`。
  - 證實物理推估出的血管阻力 $R$ 與心搏量 $SV$ 對價值評估具有關鍵貢獻！
- **心血管生理合理性驗證**：
  - 當推估之 $(SV) \cdot R$ 與 `MAP` 下降（代表血管擴張性休克）時，RL 模型推薦使用升壓藥的機率單調上升，完全符合敗血性休克病理生理學。

---

## 6. 🔍 臨床落地痛點與「汪博士放大鏡方法學審視」

![汪博士放大鏡審視](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)

1. **🔍 正面批判傳統離線策略評估 (OPE: WIS / DR) 的失真，拒絕跟風報虛高分數**：
   - 作者（包含 OPE 權威 Omer Gottesman）在文中明確指出：在敗血症這類長軌跡、高變異的醫療問題中，傳統 WIS 與 DR 估計器非常容易被「什麼都不做 (Do-nothing / Zero-drug policy)」的策略鑽漏洞而拿到虛高分數。因此本篇拒絕用單一 OPE 數字宣稱「超越醫師 X%」，改以生理機轉一致性與臨終軌跡剖析來驗證模型。
2. **🔍 發現「死前 10 小時撤除維生治療 (DNR / Withdrawal)」對 RL 的結構性扭曲**：
   - 圖 7 清楚顯示：在死亡病患死前 48 小時到死前 12 小時，醫師與 AI 使用升壓藥的比例相當接近；但到了**死前最後 10 小時**，醫師的升壓藥使用率從 $55\%$ 斷崖式掉到 $30\%$ 以下，而 AI 卻維持在 $75\% \sim 80\%$。這是因為臨床團隊與家屬在病患多重器官不可逆衰竭時決定撤除升壓藥（安寧緩和），但資料庫裡沒有標註 DNR 時間點，導致所有無知的 RL 模型都把「最後 10 小時停藥 $\to$ 死亡」當成因果關係！
3. **🔍 雙元素 Windkessel 模型的簡化假設**：
   - 雖然嵌入 Windkessel 方程極具巧思，但該方程假設主動脈瓣關閉後無逆流、且未直接納入靜脈回流 (Venous Return) 與右心房壓 (CVP)，在合併嚴重心肌病變 (Septic Cardiomyopathy) 或重度瓣膜疾病患者身上可能產生參數估計偏誤。
