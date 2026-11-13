---
title: "LLM 與醫療 AI 在醫學及工程教學上的核心注意事項（以敗血症強化學習 9 篇論文為例）"
type: synthesis
evidence_level: "[Systematic Review / Meta-analysis]"
tags: [sepsis, medical-education, llm-teaching, reinforcement-learning, methodology-pitfalls, clinical-safety]
created: 2026-11-13
updated: 2026-11-13
sources_count: 9
status: evergreen
---

# LLM 與醫療 AI 在醫學及工程教學上的核心注意事項（以敗血症強化學習為例）

> **🐾 汪博士教學導讀**：
> ![汪博士拿教鞭講解](../../圖片/dr_wang_02_explaining_pointer.jpg)
> 當我們將 **大型語言模型 (LLM)** 與 **醫療 AI 論文（如本知識庫收錄的 9 篇敗血症強化學習給藥研究）** 用於醫學院、住院醫師 Journal Club 或生醫工程課堂教學時，最大的風險就是**學生（甚至一般 LLM）很容易被論文摘要裡「AI 超越人類醫師、存活率高達 97.8%」的漂亮數字迷惑，而忽略了背後的統計陷阱與臨床致命風險**！本專頁整理出教學上必須反覆強調的 **六大核心守則**。

---

## 1. 為什麼一般 ChatGPT / RAG 摘要不適合直接拿來教醫療 AI？

| 比較維度 | 傳統單次 LLM 問答 / 一般 RAG | **Karpathy 式 LLM Wiki 結構化教學知識庫** |
| :--- | :--- | :--- |
| **對論文宣稱指標的態度** | **順從摘要 (Sycophancy)**：論文寫「存活率提升至 97.81% ([Wu 2023](../papers/2023-wu-wd3qne-human-expertise.md))」，LLM 就照抄讚美，誤導學生以為問題已解決 | **強制方法學審視**：自動對比 [Choi 2024](../papers/2024-choi-highlight-dddqn.md) 與 [Nanayakkara 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md)，指出 97.81% 是離線評估 (OPE) 外推膨脹的假象 |
| **跨論文矛盾偵測** | 每次檢索單一片段，看不出 9 篇論文雖同樣用 MIMIC-III，樣本數卻差了近 7,000 人 | 建立 [跨篇世代比較表](../cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md)，清楚標示年齡、缺失值與 DNR 排除標準差異 |
| **臨床安全與指引對照** | 容易將回溯性模型建議直接當成臨床治療建議輸出 | **強制插入紅色安全警示框 (`[!CAUTION]`)**，對照 SSC 2021 國際臨床指引與大型 RCT (CLASSIC, CLOVERS) |
| **可追溯性 (Traceability)** | 常發生引用幻覺 (Hallucinated Citations) 或張冠李戴 | 嚴格綁定 `raw/papers/` 不可變英文原檔、PMID/DOI 與頁碼數據 |

---

## 2. 教學必講：運用 LLM 與教授敗血症 AI 論文的「六大教學守則」

![汪博士拿放大鏡思考](../../圖片/dr_wang_04_thinking_magnifying_glass.jpg)

### 守則一：教學生拆穿「離線評估數字神話 (Metric Inflation & OPE Gaming)」
- **教學痛點**：工程背景學生常以為 Loss 越低、Expected Return 越高就代表模型越好；醫學背景學生則容易被換算後的「估計存活率」震懾。
- **本庫實證教材**：
  - **[Wu et al., 2023 (npj Digit Med)](../papers/2023-wu-wd3qne-human-expertise.md)** 宣稱 WD3QNE 模型在 MIMIC-III 測試集達到 **$97.81\%$ 的 90 天存活率**（死亡率僅 $2.19\%$！）。在教學時必須帶學生看懂：當評估確定性策略時，Importance Sampling 權重多數歸零，Doubly Robust 估計器退化為讓 AI 自己的 Q 網路給自己打分數（拿到 $23.63/24$ 滿分），再透過 S 型曲線外推才造就這個違反重症醫學常識的數字。
  - **[Raghu et al., 2017a](../papers/2017-raghu-continuous-state-drl-mlhc.md)** 與 **[Lin et al., 2023](../papers/2023-lin-ddpg-dosing-strategy.md)** 使用的 **U 型曲線（零劑量差距處死亡率最低）**，必須教導學生識別 **依適應症混淆 (Confounding by Indication)**：因為 AI 與醫師最常達成一致的動作是「0 劑量（不給升壓藥）」，而不需要升壓藥的病患本來就是病情最輕微的存活者！

### 守則二：教學生看見電子病歷沒寫的「隱形臨床決策 (Unmeasured Confounders & DNR)」
- **教學痛點**：AI 模型只看得到結構化表格裡的 47 個數字，看不到病床邊的家屬會談與安寧決策。
- **本庫實證教材**：
  - 為什麼 **[Komorowski 2018](../papers/2018-komorowski-ai-clinician.md)** 與 **[Choi 2024](../papers/2024-choi-highlight-dddqn.md)** 的 AI 都建議比臨床醫師多開 **2 倍以上頻率的升壓藥**（40.13% vs. 15.58%）？
  - **[Nanayakkara et al., 2022 (PLOS Digit Health)](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md)** 提供了最震撼的教學圖表：在死亡病患過世前的**最後 10 小時**，臨床醫師的升壓藥使用率從 $55\%$ 斷崖式掉到 $30\%$ 以下（因為家屬簽署 DNR / 撤除無效維生治療），但不知道 DNR 存在的 AI 卻在 $80\%$ 的瀕死狀態下瘋狂建議給升壓藥！這能讓學生深刻理解 **反向因果 (Reverse Causality)**。

### 守則三：嚴格區分「回溯性觀察關聯」與「前瞻性臨床安全 (Clinical Safety Boundary)」
- **教學痛點**：不可讓學生誤以為發表在《Nature Medicine》或《Communications Medicine》的 AI 策略就能直接寫進醫院醫囑系統。
- **本庫實證教材**：
  - **[Choi et al., 2024](../papers/2024-choi-highlight-dddqn.md)** 與 **[Wu et al., 2023](../papers/2023-wu-wd3qne-human-expertise.md)** 發現 AI 最依賴 **`BUN`（血中尿素氮）與 `Age`（年齡）**，會在 `SOFA` 與 `Shock Index` 尚未惡化前就提前給予積極輸液與升壓藥。
  - **臨床教學警示**：必須向學生強調，對高齡且高 `BUN`（心腎儲備極差）的病患，在血壓未掉前就猛灌輸液，在真實臨床上會直接誘發 **急性心衰竭與肺水腫 (Acute Pulmonary Edema)**！
  - **[Peng et al., 2018](../papers/2018-peng-moe-kernel-drl.md)** 更證明未受安全約束的純 DQN 開立「最大劑量輸液 + 最大劑量升壓藥」的頻率是醫師的 **3.8 倍**。

### 守則四：剖析「次族群異質性 (Subgroup Heterogeneity)」——平均值會說謊
- **教學痛點**：整體測試集平均表現良好，不代表對所有嚴重度的病患都安全。
- **本庫實證教材**：
  1. **輕症 ($\text{SOFA} < 5$)**：純 RL 模型容易忽略升壓藥副作用而過度給藥（需如 [Wu 2023](../papers/2023-wu-wd3qne-human-expertise.md) 嵌入人類醫師保守規則）。
  2. **中症 ($\text{SOFA } 5 \sim 15$)**：歷史樣本充足，RL 模型最具參考價值的區間。
  3. **重症 ($\text{SOFA} > 15$)**：[Raghu et al., 2017b](../papers/2017-raghu-shaped-reward-drl-nips.md) 證明此區間樣本極度稀疏且多重器官衰竭不可逆，AI 策略完全失靈。

### 守則五：注意「時間解析度」與「醫療時代偏移 (Temporal Dataset Shift)」
- **教學痛點**：
  - **時間步脫節**：床邊護理師每 5–15 分鐘滴定一次 Norepinephrine，但 9 篇論文中有 7 篇使用 **4 小時時間步**（僅 [Nanayakkara 2022](../papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md) 與 [Yu 2023](../papers/2023-yu-dirl-mt-dueling-weight-ope.md) 推進至 **1 小時**）。
  - **資料集年代落差**：連 2024 年發表的論文都還在用 **2001–2012 年的 MIMIC-III**！當時臨床盛行 Rivers (2001) 的早期目標導向大量輸液 (EGDT)，因此 AI 建議「少給水、早升壓」顯得先進；但現今 **SSC 2021 指引** 與 **CLASSIC / CLOVERS RCT** 時代的醫師早已改變實務。

### 守則六：在教學中使用 LLM 的實務工作流規範 (Human-in-the-Loop Pedagogy)
1. **禁止無源生成 (No Unsourced Claims)**：要求學生與助教使用 LLM 整理文獻時，每一項數據都必須附上 `Citation + PMID/DOI + 本地原檔頁碼`。
2. **強制雙向詰問 (Socratic & Adversarial Prompting)**：不要問 LLM「這篇論文有多好？」，而要像 `AGENTS.md` 一樣強制命令 LLM：「請用五大方法學陷阱找出這篇論文的統計漏洞與臨床落地風險」。
3. **從 Limitations 誕生新研究 (Limitations-to-Ideas Pipeline)**：引導學生比對多篇論文的 Limitations（詳見 [研究靈感庫](../ideas/rl-sepsis-research-gaps-and-ideas.md)），將批判轉化為可執行的碩博士論文題目。
