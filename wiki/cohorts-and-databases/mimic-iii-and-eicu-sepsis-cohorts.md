---
title: "MIMIC-III 與 eICU 敗血症世代篩選與前處理跨篇比較 (MIMIC-III & eICU Sepsis Cohorts)"
type: cohort
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, mimic-iii, eicu, eri, cohort-selection, sepsis-3, missing-data, imputation, treatment-withdrawal]
created: 2026-11-13
updated: 2026-11-13
sources_count: 9
status: evergreen
---

# MIMIC-III 與 eICU 敗血症世代篩選與前處理跨篇比較

> **🐾 汪博士資料集審視重點**：
> ![汪博士拿放大鏡思考](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)
> **為什麼同樣使用 MIMIC-III v1.4 和 Sepsis-3 定義，9 篇論文篩選出來的病患人數卻從 $14,012$ 人到 $20,927$ 人不等？基線死亡率也從 $11.3\%$ 跳到 $23.55\%$？** 本專頁深入拆解各研究在年齡門檻、時間窗擷取、缺失值剔除、撤除治療 (DNR) 排除與跨院驗證上的細微差異，這是撰寫醫療 AI 論文 `Methods` 與審視資料選擇偏差 (Selection Bias) 的關鍵依據！

---

## 1. 九篇敗血症 RL 論文之世代規模與前處理總表

| 論文 Citation | 內部開發資料集 ($N$) | 外部驗證資料集 ($N$) | 年齡門檻 | 擷取時間窗與時間步 $\Delta t$ | 終點指標與基線死亡率 | 缺失值插補與特殊排除規則 |
| :--- | :--- | :--- | :---: | :--- | :--- | :--- |
| **[Raghu et al., 2017a](../papers/2017-raghu-continuous-state-drl-mlhc.md)** | MIMIC-III v1.4<br>**$N = 17,898$** | 無 | $\ge 15$ 歲 | $T_0 - 24\text{h} \sim T_0 + 48\text{h}$ (72h)<br>$\Delta t = 4\text{h}$ | 院內死亡率：**13.7%** (測試集)<br>(存活 15,583 / 死亡 2,315) | 多維度 kNN 插補；排除缺失 $\ge 8$ 變數、無 IV 輸液、或 $T_0$ 後 $>16\text{h}$ 入 ICU 者 |
| **[Raghu et al., 2017b](../papers/2017-raghu-shaped-reward-drl-nips.md)** | MIMIC-III v1.4<br>**$N = 17,898$** | 無 | $\ge 15$ 歲 | $T_0 - 24\text{h} \sim T_0 + 48\text{h}$ (72h)<br>$\Delta t = 4\text{h}$ | 院內死亡率：**13.7%** | 同 Raghu 2017a（增加 SOFA 低/中/高三組分層） |
| **[Komorowski et al., 2018](../papers/2018-komorowski-ai-clinician.md)** | MIMIC-III v1.4<br>**$N = 17,083$**<br>(14,993 獨立病患) | **Philips eRI**<br>**$N = 79,073$**<br>(128 家醫院) | $\ge 18$ 歲 | MIMIC: $-24\text{h} \sim +48\text{h}$ (72h)<br>eRI: 入院後最初 72h<br>$\Delta t = 4\text{h}$ | MIMIC 90天死亡率：**18.9%**<br>MIMIC 院內死亡率：**11.3%**<br>eRI 院內死亡率：**16.4%** | Sample-and-hold + 線性/kNN 插補；**排除疑似撤除維生治療 (DNR) 病患**；eRI 排除 331/459 個紀錄不全之 ICU |
| **[Peng et al., 2018](../papers/2018-peng-moe-kernel-drl.md)** | MIMIC-III v1.4<br>**$N = 15,415$** | 無 | $18 \sim 91$ 歲 | 72h 序列<br>$\Delta t = 4\text{h}$ | 院內死亡率：**12.2%**<br>(存活 13,535 / 死亡 1,880) | **直接排除含有缺失值之病患**（導致樣本數減少約 2,000 人） |
| **[Nanayakkara et al., 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md)** | MIMIC-III v1.4 | 無 | $\ge 17$ 歲 | **$\Delta t = 1\text{h}$**（高頻生命徵象 + 12h 稀疏檢驗） | ICU 內死亡率 | Denoising GRU Autoencoder 處理稀疏檢驗；排除缺失 $>25\%$ 及 **轉出 ICU 後才在一般病房死亡者** |
| **[Yu & Huang, 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md)** | MIMIC-III v1.4<br>**$N = 14,012$** | 無 | $\ge 18$ 歲 | 72h 序列<br>**$\Delta t = 1\text{h}$** | 院內死亡率：**14.5%**<br>(存活 11,980 / 死亡 2,032) | 使用 **Extra Trees Regressor (ETR)** 將時間步插補為 1 小時解析度 |
| **[Lin et al., 2023](../papers/2023-lin-ddpg-dosing-strategy.md)** | MIMIC-III<br>(篩選 38,600 成人住院) | 無 | $> 15$ 歲 | $T_0 - 24\text{h} \sim T_0 + 48\text{h}$ (72h)<br>$\Delta t = 4\text{h}$ | 測試時間步基線死亡率：**15.7%** | kNN 插補 + Autoencoder 擴展至 200 維 |
| **[Wu et al., 2023](../papers/2023-wu-wd3qne-human-expertise.md)** | MIMIC-III v1.4<br>**$N = 17,083$**<br>(276,232 筆紀錄) | **eRI 子集**<br>**$N = 1,500$**<br>(24,279 筆紀錄) | $\ge 18$ 歲 | MIMIC: $-24\text{h} \sim +56\text{h}$ (80h, $\Delta t = 4\text{h}$, $T=20$)<br>eRI: $-36\text{h} \sim +72\text{h}$ ($\Delta t = 1\text{h}$, $T=80$) | MIMIC 90天死亡率：**18.9%**<br>(測試集死亡率 16.74%)<br>eRI 院內死亡率：**19.88%** | 排除撤除維生治療及連續缺失 $>24\text{h}$ 者；Random Forest 篩選 37 特徵 |
| **[Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md)** | MIMIC-III v1.4<br>**$N = 20,927$** | **eICU-CRD v2.0**<br>**$N = 14,875$**<br>(208 家醫院) | $\ge 18$ 歲 | 最初 **80h** 紀錄<br>$\Delta t = 4\text{h}$ | MIMIC 90天死亡率：**23.55%**<br>eICU 院內死亡率：**19.83%** | MIMIC: Sample-and-hold + kNN；eICU: **Median Imputation**；Z-score 截斷 $[-3.3, 3.3]$ |

---

## 2. 關鍵資料庫特性與跨篇差異解析

### 2.1 MIMIC-III v1.4 (Medical Information Mart for Intensive Care III)
- **醫院背景**：美國波士頓 Beth Israel Deaconess Medical Center 單一醫學中心（2001–2012 年），涵蓋 5 個不同性質的加護病房（MICU, SICU, CCU, CSRU, TSICU）。
- **核心優勢**：
  1. 包含極為詳盡的微生物培養採檢時間、抗生素給藥、每小時生命徵象與輸液/升壓藥滴注紀錄，能精確計算 Sepsis-3 發病時間點 $T_0$。
  2. 連結美國社會安全死亡索引 (Social Security Death Index)，**具備出院後 90 天存活狀態追蹤**（因此 [Komorowski 2018](../papers/2018-komorowski-ai-clinician.md)、[Wu 2023](../papers/2023-wu-wd3qne-human-expertise.md)、[Choi 2024](../papers/2024-choi-highlight-dddqn.md) 皆以 90 天死亡率為終點，死亡率約 $18.9\% \sim 23.55\%$；而只看院內死亡率的論文如 [Raghu 2017a](../papers/2017-raghu-continuous-state-drl-mlhc.md) 死亡率僅 $11.3\% \sim 13.7\%$）。
- **為何 [Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md) 的 MIMIC-III 樣本數達 $20,927$ 人（高於 Komorowski 的 $17,083$ 人）？**
  - Komorowski 嚴格排除了「疑似撤除維生治療（死前停用升壓藥）」及「無靜脈輸液紀錄」的病患，共剔除了約 3,800 筆住院（這也是為何 Komorowski 的 MIMIC 90 天死亡率為 $18.9\%$，而未排除這群臨終病患的 Choi 2024 死亡率高達 $23.55\%$！）。

### 2.2 Philips eRI 與公開版 eICU-CRD v2.0
- **eRI (Philips eICU Research Institute)**：
  - 為飛利浦公司維護之大型私有遠距 ICU 資料庫（涵蓋 459 個 ICU、超過 330 萬筆入院）。[Komorowski et al., 2018](../papers/2018-komorowski-ai-clinician.md) 取得完整授權，但發現其中 **331 個 ICU (72%) 的靜脈輸液或升壓藥紀錄不完整**，篩選後保留 128 家醫院共 $N = 79,073$ 筆敗血症住院。[Wu et al., 2023](../papers/2023-wu-wd3qne-human-expertise.md) 則僅抽取其中 $N = 1,500$ 人做外部驗證。
- **eICU-CRD v2.0 (公開版 eICU Collaborative Research Database)**：
  - 由 MIT LCP 公開釋出之多中心資料庫（涵蓋美國 **208 家醫院**、約 20 萬筆 ICU 住院，2014–2015 年）。[Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md) 從中篩選出 **$N = 14,875$** 名敗血症病患進行跨院驗證。
- **eICU / eRI 的三大共同限制**：
  1. **缺乏微生物培養 (Microbiology Cultures) 完整資料**：在 eICU 中難以完全照搬 MIMIC-III 的「培養 + 抗生素時間窗」來定義 $T_0$，往往需搭配 APACHE 入院診斷碼或 ICD 編碼。
  2. **僅有院內死亡率 (In-hospital Mortality)**：缺乏出院後 90 天存活追蹤。
  3. **各醫院檢驗與給藥紀錄密度差異極大**：跨院驗證時高度依賴插補策略（如 [Choi 2024](../papers/2024-choi-highlight-dddqn.md) 在 eICU 被迫改用中位數插補）。
