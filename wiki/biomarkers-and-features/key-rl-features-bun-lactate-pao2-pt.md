---
title: "敗血症強化學習關鍵預測與決策特徵：BUN、Lactate、PaO2、PT 與心血管參數"
type: biomarker
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, biomarkers, bun, lactate, pao2, prothrombin-time, sofa, shock-index, windkessel, svr, feature-importance]
created: 2026-11-13
updated: 2026-11-13
sources_count: 9
status: evergreen
---

# 敗血症強化學習關鍵預測與決策特徵：BUN、Lactate、PaO2、PT 與心血管參數

> **🐾 汪博士特徵重要度解密**：
> ![汪博士星星眼驚嘆](../../圖片/dr_wang_03_starry_eyed_breakthrough.jpg)
> 在多達 47~50 項的 ICU 臨床變數中，深度強化學習模型到底最看重哪些生物標記來決定輸液與升壓藥劑量？跨篇綜合比較揭曉了一個令人驚訝的一致發現：**除了大家熟知的 `SOFA` 與 `Lactate` 外，血中尿素氮 (`BUN`) 與年齡 (`Age`) 在多篇獨立研究中雙雙奪下驅動 AI 決策與預測生死的第一、二名！** 此外，**`PaO2`（氧合）**、**`PT`（凝血酶原時間）** 與物理推估之 **全身血管阻力 (`SVR`)** 更是提升決策品質的隱藏王牌。

---

## 1. 跨篇特徵重要度排行榜 (Cross-Paper Feature Importance Matrix)

| 排名 / 重要性層級 | **[Komorowski et al., 2018](../papers/2018-komorowski-ai-clinician.md)**<br>(Random Forest 預測生死) | **[Peng et al., 2018](../papers/2018-peng-moe-kernel-drl.md)**<br>(MoE Gating 網路 9 大核心特徵) | **[Nanayakkara et al., 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md)**<br>(C51 排列特徵重要度 Top 8) | **[Yu & Huang, 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md)**<br>(Mini-Tree + BIRL 最佳獎勵特徵) | **[Wu et al., 2023](../papers/2023-wu-wd3qne-human-expertise.md)**<br>(RF OOB 特徵篩選 Top 8) | **[Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md)**<br>(驅動 AI vs. 醫師給藥關鍵特徵) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Top 1** | `SOFA` | `Age` | `CNS (GCS)` | **`PaO2`** ($\text{Reward}_{4.0}$) | **`BUN`** | **輸液**：`previous_dose`, `output_4hourly` |
| **Top 2** | `MechVent` (呼吸器) | `Elixhauser` (共病) | **`BUN`** | **`PT`** ($\text{Reward}_{4.0}$) | **`Age`** | **AI 升壓藥**：**`BUN`**, **`Age`**, `previous_dose` |
| **Top 3** | **`Lactate`** | `SOFA` | `SOFA` | `MAP` ($\text{Reward}_{3.0+}$) | `SOFA` | **醫師升壓藥**：`SOFA`, `Lactate`, `MeanBP` |
| **Top 4** | **`BUN`** | `FiO2` | `HR` (心率) | **`Lactate`** ($\text{Reward}_{3.0+}$) | `Platelets` | `Shock Index` |
| **Top 5** | `Creatinine` | **`BUN`** | `Hemoglobin (HB)` | `SOFA` ($\text{Reward}_{3.0}$) | `WBC` | `cumulated_balance` (累積液體平衡) |
| **Top 6–8** | `Platelets`, `Age`, `Urine Output` | `GCS`, `Albumin`, 軌跡長度, 近鄰最大距離 | 潛在檢驗 `l_3`, **Windkessel `R` (SVR)**, **Windkessel `SV`** | `qSOFA` ($\text{Reward}_{3.0}$) | `MeanBP`, `SysBP`, `SpO2` | `diaBP` (舒張壓), `output_total` |

---

## 2. 六大核心生物標記與生理特徵之臨床與 AI 決策機轉

### 2.1 血中尿素氮 (`BUN`, Blood Urea Nitrogen) — 跨篇一致認證的頭號隱藏指標！
- **跨篇證據**：
  - 在 [Wu et al., 2023](../papers/2023-wu-wd3qne-human-expertise.md) 的隨機森林特徵篩選中，**`BUN` 擊敗 `SOFA` 與 `Lactate` 高居全體 45 項特徵第 1 名**！
  - 在 [Nanayakkara et al., 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md) 排列重要度中位居第 2 名（僅次於 GCS）。
  - 在 [Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md) 中，**`BUN` 與 `Age` 是解釋「為何 AI 比人類醫師更早開立升壓藥」的最主要特徵**。
- **病理生理與臨床意義**：
  - `BUN` 升高同時反映了三件事：(1) **有效循環血量不足導致腎前性灌流下降（近端腎小管對尿素再吸收增加）**、(2) **敗血症急性腎損傷 (Sepsis-Associated AKI)**、(3) **高分解代謝狀態 (Hypercatabolism)**。
  - AI 發現高 `BUN` 病患若不提早維持灌流壓，極易進展為不可逆腎衰竭與死亡；但 [Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md) 也警告，高 `BUN` + 高齡病患若盲目提前大量輸液，極易誘發**急性肺水腫**。

### 2.2 血中乳酸 (`Lactate`) — 組織低灌流與獎勵形塑的核心指標
- **跨篇證據**：
  - [Raghu et al., 2017b](../papers/2017-raghu-shaped-reward-drl-nips.md) 與 [Lin et al., 2023](../papers/2023-lin-ddpg-dosing-strategy.md) 將 $-2 \tanh(s_{t+1}^{\text{Lactate}} - s_t^{\text{Lactate}})$ 設為中間獎勵權重最大的項目（權重為 $\Delta\text{SOFA}$ 的 16 倍）。
  - [Yu & Huang, 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md) 將 `Lactate` 與 `MAP` 並列為敗血性休克核心獎勵 ($\text{Reward}_{3.0+}$)。
  - [Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md) 發現人類臨床醫師主要依據 `Lactate` 與 `SOFA` 飆升才開始追趕升壓藥與輸液。

### 2.3 動脈血氧分壓 (`PaO2`) 與 凝血酶原時間 (`PT`, Prothrombin Time) — 煞住過度輸液的守門員
- **跨篇證據**：
  - [Yu & Huang, 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md) 透過 Mini-Tree 從 30 個特徵中自動挖掘出 **`PaO2` 與 `PT`** 為區分敗血症生死資訊量最高的「第四代獎勵指標 ($\text{Reward}_{4.0}$)」。
- **病理生理與臨床意義**：
  - 當加入 `PaO2` 與 `PT` 作為獎勵後，AI 模型學會將**靜脈輸液量減少 40%**。因為臨床上過度輸液會導致肺泡間質水腫（直接使 `PaO2` 惡化）以及血液稀釋性凝血因子濃度下降（直接使 `PT` 延長），同時 `PT` 延長也代表敗血症誘發凝血病變 (Sepsis-Induced Coagulopathy, SIC)。

### 2.4 尿量 (`Urine Output`)、累積液體平衡 (`Cumulated Balance`) 與 舒張壓 (`DiaBP`)
- **跨篇證據**：
  - [Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md) 指出，AI 與人類醫師在決定**靜脈輸液量**時有高達 $70\%$ 的一致性，雙方共同依賴的前五大特徵正是：`previous_dose`、`output_4hourly`（近 4 小時尿量）、`output_total`、`cumulated_balance`（累積液體平衡）與 **`diaBP`（舒張壓）**。
- **臨床珍珠 (Clinical Pearl)**：
  - **舒張壓 (`DiaBP`)** 是反映全身血管張力 (Vascular Tone) 與冠狀動脈灌流壓最直接的床邊指標！當敗血症引發嚴重血管擴張時，舒張壓會顯著低落（如 $< 40 \sim 50\text{ mmHg}$），此時單靠輸液無法升壓，必須及時介入 Norepinephrine（呼應近年 **Diastolic Shock Index = HR / DiaBP** 的臨床研究熱潮）。

### 2.5 Windkessel 物理推估之全身血管阻力 (`SVR` / $R$) 與心搏量 (`SV`)
- **跨篇證據**：
  - [Nanayakkara et al., 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md) 透過雙元素 Windkessel 微分方程從收縮壓、舒張壓、MAP 與心率中逆推出隱藏的血管阻力 $R$ 與心搏量 $SV$，兩者雙雙進入 C51 模型排列重要度前 8 名，且模型成功學會在 $(SV)\cdot R$ 乘積下降時提高升壓藥推薦率。
