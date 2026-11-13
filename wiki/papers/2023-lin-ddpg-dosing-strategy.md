---
title: "A dosing strategy model of deep deterministic policy gradient algorithm for sepsis patients"
type: paper
evidence_level: "[Retrospective 回溯性建模]"
tags: [sepsis, mimic-iii, reinforcement-learning, ddpg, actor-critic, autoencoder, fluid-resuscitation, vasopressor, u-curve]
created: 2026-11-13
updated: 2026-11-13
sources_count: 1
status: evergreen
citation: "Lin et al., 2023 [BMC Med Inform Decis Mak]"
pmid_or_doi: "PMID: 37147642 / DOI: 10.1186/s12911-023-02175-7"
sepsis_criteria: "Sepsis-3 (Suspected infection + SOFA >= 2)"
setting_and_cohort: "ICU (MIMIC-III, screened N = 38,600 adults)"
raw_source: "../../raw/papers/2023-lin-ddpg_dosing_strategy_bmc.pdf"
---

# A dosing strategy model of deep deterministic policy gradient algorithm for sepsis patients

> **🐾 汪博士一句話核心貢獻 (TL;DR)**：
> ![汪博士揮手微汗](../../圖片/dr_wang_01_greeting_sweat.jpg)
> **嘗試將 Actor-Critic 架構之 Deep Deterministic Policy Gradient (DDPG) 引入敗血症給藥策略，展示比傳統 DQN 快 10 倍的收斂速度與更高的醫師處方吻合度；但汪博士必須提醒：作者最後仍將 DDPG 輸出離散化回 $5 \times 5 = 25$ 格，且完全依賴具混淆偏差的 U 型曲線評估，是研讀醫療 RL 方法學時極佳的「反面教材與審稿練功範例」！**

---

## 1. 📌 文獻基本資訊與證據等級標籤

- **證據等級**：`[Retrospective 回溯性建模]`（單一資料庫回溯性離線建模，方法學與評估指標存在明顯限制）
- **標準引用 (Citation)**：`Lin et al., 2023 [BMC Med Inform Decis Mak]`
- **發表期刊**：*BMC Medical Informatics and Decision Making*, 23:81 (2023)
- **作者團隊**：Jian Lin,airong, et al. (Fuzhou University & Fujian Provincial Hospital)
- **識別碼**：`PMID: 37147642` / `DOI: 10.1186/s12911-023-02175-7`
- **本地英文原檔**：[2023-lin-ddpg_dosing_strategy_bmc.pdf](../../raw/papers/2023-lin-ddpg_dosing_strategy_bmc.pdf)
- **跨頁關聯**：
  - 對照基準模型：[Raghu et al., 2017b (MIT/ICL Dueling DDQN)](2017-raghu-shaped-reward-drl-nips.md)
  - AI 任務專頁：[強化學習於敗血症輸液與升壓藥決策演進](../ai-tasks-and-models/rl-fluid-vasopressor-dosing.md)

---

## 2. 🩸 敗血症定義與收案納入/排除標準 (Sepsis Criteria & Onset $T_0$)

- **診斷標準**：採用 **[Sepsis-3](../scoring-and-criteria/sofa-and-sepsis3-in-rl.md)** 定義（疑似感染 + $\text{SOFA} \ge 2$）。
- **時間窗口**：完全沿用 [Raghu et al., 2017a](2017-raghu-continuous-state-drl-mlhc.md) 之設定，擷取發病時間點 $T_0$ 前 24 小時至後 48 小時（共 72 小時），以 **4 小時** 為一時間步。
- **排除標準**：排除年齡 $\le 15$ 歲、資料大量缺失或無靜脈輸液紀錄者。

---

## 3. 🏥 收案場域與資料集來源 (Setting & Cohort)

- **收案場域**：重症加護病房 (ICU)
- **資料集來源**：[MIMIC-III](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md)
- **樣本數描述**：文中描述篩選了 38,600 筆 MIMIC-III 成人住院紀錄（$>15$ 歲），提取符合 Sepsis-3 之病患時間步樣本（測試集評估約 55,856 個時間步樣本，基線整體死亡率約 **15.7%**）。

---

## 4. 💊 臨床介入與終點指標 (PICO & Clinical Endpoints)

> [!CAUTION] ⚠️ 尚未經前瞻性 RCT 證實 — 依適應症混淆 (Confounding by Indication) 高風險
> 本研究並未執行標準的離線策略評估（如 WIS 或 Doubly Robust OPE），而是將「AI 與臨床醫師給藥劑量完全一致區間（文中稱 Brown Region）的觀察死亡率（11.59%）」當作模型效能證明。由於 DDPG 高度模仿臨床醫師對輕症病患開立「零或低劑量」處方，該區間的低死亡率主要反映輕症病患本身的良好預後，絕非模型帶來的因果療效。

- **P (Patient)**：MIMIC-III 成人 Sepsis-3 ICU 病患。
- **I (Intervention - DDPG Model)**：基於 Actor-Critic 架構之離線修改版 DDPG 每 4 小時給藥建議。
- **C (Comparison)**：MIT/ICL 團隊之 DQN 模型（[Raghu et al., 2017b](2017-raghu-shaped-reward-drl-nips.md)）與臨床醫師實際處方。
- **O (Outcome)**：
  - **訓練收斂速度與 TD-Error**。
  - **「零劑量差距區 (Brown Region)」樣本占比與對應之觀察院內死亡率**：
    - **MIT/ICL DQN**：與醫師處方完全一致之樣本數為 $7,136$ 筆（佔 $12.77\%$），該區間病患死亡率為 **$14.17\%$**。
    - **本研究 DDPG**：與醫師處方完全一致之樣本數達 **$17,293$ 筆（佔 $30.96\%$，較 DQN 提升 $+142.3\%$）**，該區間病患死亡率為 **$11.59\%$**（相較基線 $15.7\%$ 低 $4.11\%$）。

---

## 5. 🤖 AI 模型、特徵工程與完整評估指標

![汪博士講解 DDPG 架構](../../圖片/dr_wang_02_explaining_pointer.jpg)

### 5.1 狀態表徵與獎勵函數（沿用 Raghu 2017a/b）
- **48 項臨床特徵 $\to$ 200 維 Autoencoder 狀態表徵**：
  - 使用多重 kNN 填補缺失值並標準化後，透過 Autoencoder 將 48 維特徵擴展提取為 **200 維潛在狀態向量**。
- **獎勵函數**：
  - 完全採用 [Raghu et al., 2017b](2017-raghu-shaped-reward-drl-nips.md) 之公式：
    $$r(s_t, s_{t+1}) = C_0 \mathbb{1}(s_{t+1}^{\text{SOFA}} = s_t^{\text{SOFA}} \land s_{t+1}^{\text{SOFA}} > 0) + C_1(s_{t+1}^{\text{SOFA}} - s_t^{\text{SOFA}}) + C_2 \tanh(s_{t+1}^{\text{Lactate}} - s_t^{\text{Lactate}})$$
    其中 $C_0 = -0.025, C_1 = -0.125, C_2 = -2$，終點存活 $+15$、死亡 $-15$。

### 5.2 離線修改版 DDPG (Actor-Critic) 架構
- 包含 4 個神經網路：**Online Actor $\mu(s|\theta^\mu)$**、**Target Actor $\mu'(s|\theta^{\mu'})$**、**Online Critic $Q(s,a|\theta^Q)$**、**Target Critic $Q'(s,a|\theta^{Q'})$**（軟更新係數 $\tau = 0.001$）。
- **針對歷史離線病歷的兩項修改**：
  1. 捨棄線上環境互動，直接從歷史病歷建立之 Experience Pool 抽樣訓練。
  2. **獎勵加權抽樣 (Reward-Weighted Experience Replay)**：給予帶有終點生死獎勵 ($\pm 15$) 的時間步更高的抽樣權重，加速終點訊號向前傳遞。
- **訓練效率表現**：
  - DDPG 約在 **2,000 steps** 即達到穩定收斂，而傳統 DQN 需約 **20,000 steps**（收斂步數減少 **10 倍**，總訓練時間縮短 **1.7 倍**，且 TD-Error 顯著較低）。

---

## 6. 🔍 臨床落地痛點與「汪博士放大鏡方法學審視」

![汪博士放大鏡審視](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)

1. **🔍 方法學最大矛盾：號稱使用連續動作演算法 (DDPG)，卻還是把動作切成 $5 \times 5 = 25$ 個離散格子！**
   - DDPG 的存在意義本來是為了輸出真正的「連續劑量數值（例如輸液 $135\text{ mL/h}$、升壓藥 $0.08\ \mu\text{g/kg/min}$）」。然而作者在實驗與評估時，仍然把輸液和升壓藥分成 $5 \times 5 = 25$ 個離散區間，甚至在論文最後的 Limitations 區塊自己坦承：「*First, our action space was discrete... not continuous*」。這使得採用 DDPG 的核心理論優勢未能真正發揮。
2. **🔍 離線強化學習 (Offline RL) 中的 Actor 外推崩潰與隱性行為複製**：
   - 在標準 Offline RL 文獻（如 Fujimoto et al., ICML 2019 BCQ）中早已證明：直接把不做約束的 DDPG 用在離線資料集上會因為 Actor 查詢未見過的動作導致嚴重發散。本篇之所以沒有發散，且與臨床醫師動作一致率高達 $30.96\%$（比 DQN 高出 $142\%$），很可能是因為網路在離線抽樣下退化為近似 **行為複製 (Behavior Cloning)**，大量推薦最常見的「零劑量或低劑量」處方。
3. **🔍 評估指標缺陷：把「與醫師一致比例高」直接等同於「療效更好」**：
   - 作者定義「Brown Region（AI 與醫師給藥差距為 0 的區間）」，並以 DDPG 落在此區間的樣本數比 DQN 多、死亡率 $11.59\%$ 比 DQN 的 $14.17\%$ 低，推論 DDPG 策略更優。事實上，如果一個模型 100% 照抄醫師對輕症病患「不給升壓藥」的決定，它在 Brown Region 的樣本數就會最大化且死亡率極低，但這對需要 AI 輔助決策的重症休克病患毫無實質幫助！
