# AGENTS.md — 敗血症 (Sepsis) 論文 LLM Wiki 知識庫核心規範 (Schema)

本專案採用 Karpathy 的 **LLM Wiki** 架構，專為 **「敗血症臨床醫學與治療指引 (Clinical Medicine & Guidelines)」** 及 **「敗血症醫療 AI 與臨床預測模型 (Medical AI & Prediction Models)」** 雙主軸研究量身打造。
我們的目標是建立一個**持續累積、自動交叉參照、具備重症醫學證據把關與 AI 方法學審視能力**的知識庫，並結合專屬吉祥物**「汪博士 (Dr. Wang)」**產出高品質的筆記、Journal Club 簡報、互動式 HTML 網頁與論文草稿。

身為維護此知識庫的 LLM Agent，你必須嚴格遵守本文件的架構、欄位規範、方法學把關標準與工作流。

---

## 1. 三層架構與目錄規範 (Three-Layer Architecture)

```text
sepsis-llm-wiki/
├── AGENTS.md                        # 核心規範 (Schema)：定義敗血症欄位規範、方法學把關、工作流與汪博士視覺整合
├── 圖片/                             # 「汪博士 (Dr. Wang)」插圖素材庫
│   ├── dr-wang-catalog.md           # 汪博士 5 款表情動作索引表與臨床/AI 嵌入語法範本
│   └── dr_wang_01~05_*.jpg          # 汪博士插圖原檔
├── raw/                             # 【第一層：不可變原始文獻層 (Immutable Raw Sources — 禁止修改)】
│   ├── papers/                      # 原始英文期刊論文 (PDF / Markdown / HTML)
│   ├── guidelines/                  # 國際臨床指引 (如 Surviving Sepsis Campaign) 與大型 Systematic Review / Meta-analysis
│   ├── assets/                      # 從論文擷取之關鍵圖表 (ROC, Kaplan-Meier, SHAP, Flowcharts)
│   ├── my-notes/                    # 使用者個人的實驗數據、醫院內部研究筆記或會議摘要草稿
│   └── llm-wiki-karpathy-en.md      # Karpathy LLM Wiki 原始英文構想文件
├── wiki/                            # 【第二層：LLM 持續編譯的敗血症知識庫 (Compounding Wiki)】
│   ├── index.md                     # 內容導向總目錄（含證據等級標記、一句話摘要、涵蓋文獻數）
│   ├── log.md                       # 時間軸操作紀錄（格式：`## [YYYY-MM-DD] ingest/query/lint | 標題`）
│   ├── papers/                      # 單篇論文/指引深度解析 (`<year>-<first-author>-<short-slug>.md`)
│   ├── synthesis/                   # 跨文獻綜合比較大表 (Evidence Table)、分類樹與矛盾演進紀錄
│   ├── scoring-and-criteria/        # 臨床評分與診斷標準專頁（Sepsis-3, SOFA, qSOFA, NEWS2, SIRS 跨篇比較）
│   ├── biomarkers-and-features/     # 預測特徵與生物標記專頁（Lactate, PCT, CRP, HRV, Vital Signs 閾值與重要度）
│   ├── cohorts-and-databases/       # 醫療資料庫與世代研究專頁（MIMIC-III/IV, eICU, AmsterdamUMCdb, 本土世代特性）
│   ├── ai-tasks-and-models/         # AI 演算法與臨床任務專頁（早期預警、RL 輸液/升壓藥決策、亞型分群 Phenotyping）
│   ├── clinical-management/         # 臨床治療策略與爭議專頁（限制性 vs. 自由輸液、類固醇、抗生素時機窗證據演進）
│   ├── glossary/                    # 敗血症臨床與 AI 縮寫暨專有名詞字典 (Glossary)
│   └── ideas/                       # 具發表潛力的新研究題目、待驗證假設、研究缺口 (Research Gaps) 與吐槽筆記
└── outputs/                         # 【第三層：學術產出與視覺化呈現 (Outputs)】
    ├── slides/                      # Journal Club / 實驗室報告簡報 (Marp / Slidev Markdown)
    ├── html/                        # 含汪博士導讀的互動式教學與文獻導讀網頁
    └── drafts/                      # 論文 Introduction / Related Work / Discussion 草稿與計畫書 Evidence Table
```

> **鐵律**：`raw/` 目錄下的任何檔案為不可變之真實來源 (Source of Truth)，Agent **絕對不可修改或刪除** `raw/` 內的檔案。

---

## 2. 語言、引用、統計指標與格式規範 (Conventions)

### 2.1 語言與英文原檔保存
1. **全面以台灣繁體中文撰寫**：Wiki 內所有分析、摘要、比較表、簡報與 HTML 皆使用台灣醫學與工程慣用繁體中文（如：敗血症、敗血性休克、輸液復甦、升壓藥、加護病房、電子病歷、資料集、基準測試、微調、推論）。
2. **臨床與 AI 標準縮寫保留英文**：
   - 諸如 `Sepsis-3`, `SOFA`, `qSOFA`, `NEWS2`, `SIRS`, `MAP`, `Lactate`, `PCT`, `CRP`, `Vasopressor`, `Norepinephrine (NE)`, `CRRT`, `EHR`, `AUROC`, `AUPRC`, `PPV`, `NPV`, `SHAP`, `MIMIC-IV`, `eICU` 等標準縮寫一律保留英文。
   - 首次於單篇筆記出現時附註中文說明，並自動將新縮寫收錄至 [`wiki/glossary/sepsis-abbreviations.md`](wiki/glossary/sepsis-abbreviations.md)。
3. **文獻引用格式**：
   - 內文與表格引用統一標註：**`FirstAuthor et al., YYYY [Journal]`**（例如：`Singer et al., 2016 [JAMA]` 或 `Adams et al., 2022 [Nature Medicine]`），並附上 **PMID / DOI**、Wiki 內部連結與 `raw/` 本地英文原檔連結，方便日後直接轉換為 EndNote / BibTeX 引用。

### 2.2 混合模式：YAML Frontmatter + 相容性 Markdown 連結
1. **強制 YAML Frontmatter**：
   每篇 `wiki/` 檔案頂部必須包含結構化 YAML（支援 Obsidian Dataview 與腳本解析）：
   ```yaml
   ---
   title: "論文或專頁標題"
   type: paper | guideline | synthesis | scoring | biomarker | cohort | ai-model | clinical | glossary | idea
   evidence_level: "[Guideline 指引]" | "[Multi-center RCT]" | "[Single-center RCT]" | "[Prospective 前瞻性驗證]" | "[Retrospective 回溯性建模]" | "[Systematic Review / Meta-analysis]"
   tags: [sepsis, mimic-iv, early-prediction, sofa]
   created: YYYY-MM-DD
   updated: YYYY-MM-DD
   sources_count: 1
   status: evergreen | draft | needs-review
   # 若為單篇論文 (paper / guideline) 必填：
   citation: "FirstAuthor et al., YYYY [Journal]"
   pmid_or_doi: "PMID: xxxxxxxx / DOI: 10.xxxx/xxxxx"
   sepsis_criteria: "Sepsis-3 (SOFA >= 2)"
   setting_and_cohort: "ICU / ED (MIMIC-IV, N = xx,xxx)"
   raw_source: "../../raw/papers/YYYY-author-slug.pdf"
   ---
   ```
2. **雙向相容連結**：統一使用**標準 Markdown 相對路徑連結**（例如 `[SOFA Score](../scoring-and-criteria/sofa-score.md)`），兼顧 Obsidian Graph View 與一般 Markdown/HTML 渲染器。

---

## 3. 單篇敗血症論文強制拆解欄位 (`wiki/papers/`)

每當消化一篇敗血症論文時，單篇筆記必須結構化拆解以下 **6 大必備區塊**：

1. **📌 文獻基本資訊與證據等級標籤**：
   - 醒目標示證據等級（`[Guideline 指引]` / `[Multi-center RCT]` / `[Prospective 前瞻性驗證]` / `[Retrospective 回溯性建模]`）。
   - 附上完整 Citation、PMID/DOI、本地英文原檔連結 [`raw/papers/...`]，以及「🐾 汪博士一句話核心貢獻 (TL;DR)」。
2. **🩸 敗血症定義與收案納入/排除標準 (Sepsis Criteria & Onset $T_0$)**：
   - 採用哪種診斷定義（Sepsis-3 / SIRS / ICD 編碼 / 臨床醫師判定）？
   - **關鍵檢核：敗血症發病時間點 $T_0$ (Sepsis Onset Time) 如何定義？**（例如：血液培養開立時間 vs. 抗生素給藥時間 vs. SOFA 上升 $\ge 2$ 分的時間點？是否排除了入院前已發病或院內轉床病人？）
3. **🏥 收案場域與資料集來源 (Setting & Cohort)**：
   - 場域（急診 ED / 加護病房 ICU / 一般病房 Ward / 院前 Pre-hospital）。
   - 資料來源（單中心/多中心、公開資料庫如 MIMIC-III/IV, eICU, AmsterdamUMCdb 或台灣本土世代）、收案年份、總樣本數 ($N$) 與敗血症盛行率 (Prevalence / Event Rate)。
4. **💊 臨床介入與終點指標 (PICO & Clinical Endpoints)**：
   - 針對臨床研究或落地試驗：詳列 **P (Patient)**、**I (Intervention)**、**C (Comparison)**、**O (Outcome)**（28天/90天/院內死亡率、ICU 住院天數、無呼吸器/無升壓藥天數、器官衰竭改善、抗生素給藥時間縮短等）。
   - **統計嚴謹度**：所有效果量 (OR, HR, RR, Risk Difference) **強制同時記錄 95% CI 與 p-value**。
5. **🤖 AI 模型、特徵工程與完整評估指標（針對 AI / 預測論文）**：
   - **輸入特徵与時間窗**：使用哪些 Vital Signs、Lab 檢驗值（如 Lactate, WBC, Creatinine）、文字病歷或波形？資料缺失值 (Missing Data) 如何填補（注意是否使用未來資訊插補）？預測提前時間窗 (**Lead Time**，如發病前 4h / 6h / 12h) 為何？
   - **完整指標強制規範**：**絕對禁止只寫單一 AUROC！** 必須同時記錄或標註缺失：
     - **AUROC (95% CI)** 與 **AUPRC (PR-AUC，對不平衡敗血症資料極關鍵)**
     - 特定閾值下的 **Sensitivity (敏感度)**、**Specificity (特異度)**、**PPV (陽性預測值)**、**NPV (陰性預測值)**、**NNE (Number Needed to Evaluate / 每觸發幾次警報才有 1 位真病人)**。
   - **TRIPOD+AI 臨床成熟度與驗證等級**：僅內部交叉驗證 (Internal CV)？跨時間分割？跨院外部驗證 (External Validation)？或是具備前瞻性臨床部署驗證 (Prospective Silent Trial / Interventional Trial)？
6. **🔍 臨床落地痛點與「汪博士放大鏡方法學審視」**：
   - 檢視是否踩中第 4 節的「五大敗血症方法學陷阱」，以及模型/介入措施對第一線醫護工作流（如警報疲勞 Alert Fatigue、抽血頻率依賴）的真實影響。

---

## 4. 跨文獻矛盾與「五大敗血症方法學陷阱」強制把關 (`wiki/synthesis/`)

LLM 在每次 `Ingest` 與 `Lint` 時，必須像嚴格的重症醫學與醫療 AI 審稿人一樣，主動偵測並跨篇標註以下 **5 大陷阱與證據防線**：

1. **陷阱一：$T_0$ 發病時間點定義不一致與標籤洩漏 (Onset $T_0$ & Data Leakage)**
   - 不同論文對 Sepsis Onset $T_0$ 定義微調，就能讓 AUROC 虛胖 0.10 以上；或者將「醫師已經懷疑敗血症才開立的高頻抽血/乳酸檢驗或抗生素處方」當作預測特徵（Circular Reasoning / Label Leakage）。一旦發現，必須在單篇筆記與 `synthesis/` 中以 `> [!WARNING]` 明確揭露。
2. **陷阱二：內部驗證神話 vs. 跨院外部驗證崩跌 (Internal vs. External Generalizability)**
   - 記錄模型在原始訓練集（如 MIMIC）與跨院外部驗證集上的表現落差（例如 Epic Sepsis Model 在廠商內部宣稱高分，但在外部獨立驗證敏感度與 PPV 大幅下滑的案例）。
3. **陷阱三：觀察性研究 / 回溯性 AI vs. 隨機對照試驗 (RCT) 與臨床指引的安全防線**
   - **【最高醫療安全防線】**：當「回溯性數據分析或強化學習 (RL) 論文」（如 AI Clinician 建議某種輸液或升壓藥劑量）提出的治療結論，與現行 **國際臨床指引（如 Surviving Sepsis Campaign, SSC Guidelines）** 或 **大型前瞻性 RCT（如 CLOVERS, CLASSIC, ANDROMEDA-SHOCK）** 結論衝突時，**強制插入醒目紅色警示框 (`> [!CAUTION] ⚠️ 尚未經前瞻性 RCT 證實 — 與現行臨床指引存在落差`)**，絕不可將回溯性相關性直接寫成臨床治療定論！
4. **陷阱四：高 AUROC 掩蓋的「低 PPV 與警報疲勞 (Alert Fatigue)」**
   - 在急診或一般病房等敗血症盛行率較低的場域，即使 AUROC 達 0.85，PPV 往往低於 10%~15%（每響 10 次警報有 9 次是假警報）。必須主動換算或點出假警報負擔。
5. **陷阱五：病人次族群異質性 (Subgroup Heterogeneity)**
   - 主動比對不同研究在特定次族群（高齡長者、免疫功能低下/腫瘤病人、外科手術後 vs. 內科感染、急診入院 vs. ICU 院內感染、不同感染源如肺部 vs. 腹腔 vs. 泌尿道）上的預測或治療效果差異。

---

## 5. 三大核心操作工作流 (Core Operations)

### 操作一：Ingest（論文消化與跨篇編譯）

#### 📥 步驟 0：原始文獻與關鍵圖表自動歸檔 (`raw/`)
- **提供 PDF / 本地檔案**：Agent 自動將英文原檔複製保存至 `raw/papers/`（期刊論文）或 `raw/guidelines/`（臨床指引/系統性回顧），命名格式為 `YYYY-first_author-short_slug.pdf`。
- **提供網址 (PubMed / PMC / DOI / arXiv)**：Agent 自動下載原始英文 PDF 或全文檔案存入 `raw/papers/`。
- **關鍵圖表擷取**：若論文內含關鍵的 **ROC/PR 曲線、Kaplan-Meier 存活曲線、SHAP 特徵重要度圖、收案 Flowchart 或亞型分群圖**，將其擷取保存至 `raw/assets/<paper-slug>-figX.png` 並在 Wiki 筆記中嵌入解說。

#### 🔄 步驟 1 ~ 6：消化與跨頁編譯（預設全自動，可指定 `--discuss` 半自動討論）
1. **建立單篇深度筆記 (`wiki/papers/<slug>.md`)**：依第 3 節的 6 大必抓欄位完整撰寫。
2. **跨篇主題專頁編譯（Compounding Update — 每次觸及 5~15 個頁面）**：
   - 更新 [`wiki/scoring-and-criteria/`](wiki/scoring-and-criteria/)（若涉及 SOFA, qSOFA, NEWS2, SIRS 等表現數據）。
   - 更新 [`wiki/biomarkers-and-features/`](wiki/biomarkers-and-features/)（更新 Lactate, PCT, HRV 等特徵在該論文中的預測權重或切點閾值）。
   - 更新 [`wiki/cohorts-and-databases/`](wiki/cohorts-and-databases/)（在 MIMIC-IV, eICU 等資料庫專頁下追加此論文的收案篩選邏輯與樣本數）。
   - 更新 [`wiki/ai-tasks-and-models/`](wiki/ai-tasks-and-models/)（更新早期預警、RL 決策或亞型分群的跨論文演進）。
   - 更新 [`wiki/clinical-management/`](wiki/clinical-management/)（更新輸液、抗生素、升壓藥、類固醇等臨床處置證據）。
   - 更新 [`wiki/glossary/sepsis-abbreviations.md`](wiki/glossary/sepsis-abbreviations.md)（補充新縮寫與術語）。
3. **更新跨文獻比較大表與矛盾紀錄 (`wiki/synthesis/sepsis-evidence-matrix.md`)**。
4. **提煉研究靈感 (`wiki/ideas/`)**：將該論文的 Limitation 或未解臨床痛點轉化為潛在研究假設。
5. **更新總目錄 (`wiki/index.md`)** 與 **時間軸紀錄 (`wiki/log.md`，格式：`## [YYYY-MM-DD] ingest | <Citation> - <標題>`)**。

---

### 操作二：Query（知識檢索、論文草稿輔助與汪博士視覺化產出）

1. **檢索流程**：先讀取 [`wiki/index.md`](wiki/index.md) 與 [`wiki/synthesis/sepsis-evidence-matrix.md`](wiki/synthesis/sepsis-evidence-matrix.md) 定位知識節點，再深入對應專頁與個人筆記 (`raw/my-notes/`)。
2. **四大標準產出模式**：
   - **模式 A：Journal Club 簡報 (`outputs/slides/`) 與互動式 HTML (`outputs/html/`)** — 由「汪博士」全程導讀，穿插 5 款情緒插圖、架構圖解與方法學吐槽框。
   - **模式 B：論文段落草稿 (`outputs/drafts/`)** — 協助撰寫使用者自身論文的 `Introduction`、`Related Work` 或 `Discussion`，自動串聯文獻脈絡、標註標準 Citation (`Author et al., YYYY [Journal]`) 並突顯研究缺口 (Research Gap)。
   - **模式 C：跨文獻比較大表 (Evidence Table / Benchmark Matrix)** — 產出可直接貼入 Systematic Review、研究計畫書 (Grant Proposal) 或 IRB 申請書的結構化對照表。
   - **模式 D：知識回存 (File Back to Wiki)** — 將對話中產生的高價值跨篇比較或新假設直接存回 `wiki/synthesis/` 或 `wiki/ideas/`，並記錄於 `wiki/log.md` (`## [YYYY-MM-DD] query | <主題>`)。

---

### 操作三：Lint（定期健檢、經典文獻缺口盤點與新題目合成）

當使用者下達 `lint` 或健檢指令時，執行以下 4 項任務並記錄於 `wiki/log.md` (`## [YYYY-MM-DD] lint | 健檢報告`)：
1. **斷鏈、孤島頁面與未建檔高頻概念檢查**：確保所有雙向連結有效，為反覆出現卻尚未獨立成頁的生物標記、評分或模型建立專頁。
2. **方法學矛盾與安全防線複查**：確認所有回溯性 AI 治療建議皆已加上與臨床指引對照之警示框。
3. **📚 敗血症經典文獻缺口盤點 (Missing Landmark Papers Check)**：
   - 主動檢查目前知識庫是否缺少敗血症領域的必讀基石文獻（例如：*Sepsis-3 Consensus [Singer et al., 2016 JAMA]*、*Surviving Sepsis Campaign 2021 Guidelines*、*Epic Sepsis Model External Validation [Wong et al., 2021 JAMA Intern Med]*、*TREWS Prospective Trial [Adams et al., 2022 Nat Med]*、*Seymour et al., 2019 JAMA 敗血症四大亞型 SENECA*、*AI Clinician [Komorowski et al., 2018 Nat Med]* 等），列出推薦補齊清單。
4. **💡 自動合成 3~5 個具發表潛力的新研究題目 (`wiki/ideas/`)**：
   - 交叉比對已讀論文的 Limitations 與臨床未滿足需求（例如：特定次族群外部驗證、結合新生物標記與動態 EHR 特徵、降低假警報率的決策閾值設計），合成具體可執行的 Research Ideas（含研究假設、預計使用資料庫、方法設計與預期臨床價值）。

---

## 6. 「汪博士 (Dr. Wang)」視覺整合規範

詳見 [`圖片/dr-wang-catalog.md`](圖片/dr-wang-catalog.md)。在日常 Wiki 筆記、簡報與 HTML 中，依情境自動嵌入 5 款汪博士插圖：
- **01 揮手微汗 (`dr_wang_01_greeting_sweat.jpg`)**：開場導讀、或溫和點出臨床推行困難与高昂資源消耗。
- **02 拿教鞭講解 (`dr_wang_02_explaining_pointer.jpg`)**：拆解 AI 模型架構、特徵工程與病理生理機轉。
- **03 星星眼驚嘆 (`dr_wang_03_starry_eyed_breakthrough.jpg`)**：標示臨床珍珠 (Clinical Pearls)、大型 RCT 顯著改善存活率或前瞻性驗證重大突破。
- **04 拿放大鏡思考 (`dr_wang_04_thinking_magnifying_glass.jpg`)**：審視五大方法學陷阱（$T_0$ 標籤洩漏、低 PPV、外部驗證崩跌、回溯性偏差）與提出研究假設。
- **05 笑眼比讚 (`dr_wang_05_thumbs_up_approved.jpg`)**：單篇 TL;DR 總結、經過高等級證據驗證的臨床最佳實踐 (Best Practice)。


---

## 7. 四階段 Graph 導向教學簡報製作與檢查工作流 (Graph-Driven Slide Pipeline)

當需要將 Wiki 知識編譯為深度教學 HTML 簡報時，嚴格禁止「從論文直接一步跳到寫 HTML 程式碼」，必須遵循以下 **四階段 Graph 藍圖與檢查點（Gate Check）流程**：

1. **【Stage 1】全域知識圖譜與大綱規劃 (`stage1_macro_graph_and_outline.md`)**
   - 繪製跨文獻「臨床痛點 ➔ 演算法突破」演進網路圖（Tier-1 Macro Graph）與全簡報逐頁大綱。
   - 🔍 **Gate 1 檢查點**：確認跨論文演進連線、頁數配重與教學主軸後，才可進入下一階段。
2. **【Stage 2】逐頁 Graph 節點與連線藍圖 (`stage2_slide_by_slide_graph_blueprint.md`)**
   - 以 Markdown + Mermaid 完整定義每一頁的節點集合（特徵、編碼器、RL 網路、處方動作、因果偏誤）與有向邊（資料流、臨床因果、獎勵回饋、偏誤攔截），並填入精確數據（樣本數 $N$、MDP 五元組、OPE 指標）。
   - 🔍 **Gate 2 檢查點**：核對科學數據與原文一致性、檢驗箭頭是否混淆「相關性與因果性」、確保單頁節點數不造成認知超載。
3. **【Stage 3】日式清新視覺語意規範檢查 (Visual Semantics Spec)**
   - 統一五色節點語意（🟢 臨床觀測、🟠 AI 模型編碼、🔵 處方與評估、🟣 獎勵與護欄、🔴 因果偏誤陷阱）與四種有向邊線型。
   - 🔍 **Gate 3 檢查點**：確保全簡報 30+ 頁圖例完全一致。
4. **【Stage 4】最終一次性編譯與自動化結構驗收 (Final Compilation & Gate 4 QA)**
   - 編譯生成互動式 HTML 投影片，並用腳本自動檢查總頁數、跨論文定位麵包屑（`.graph-breadcrumb`）、語意節點數（`.g-node`）與汪博士教學框完整性。

---

## 8. Git 版本控制與 GitHub 自動同步規範 (Git & GitHub Sync SOP)

本知識庫使用 Git 進行版本控制。每當使用者下達 **「幫我更新到 GitHub」**、**「同步 Git」**，或在完成重大 `Ingest` / `HTML 簡報生成` 後，Agent 應依循以下標準流程：

1. **檢查變更狀態**：執行 `git status -s` 確認新增或修改之 `raw/`、`wiki/` 與 `outputs/` 檔案。
2. **更新日誌確認**：確認本次變更已記錄於 `wiki/log.md` 與 `wiki/index.md`。
3. **階段性提交 (Semantic Commit)**：
   - 消化新論文：`git add . && git commit -m "ingest: 新增 <Author YYYY> 論文解析與跨頁知識圖譜更新"`
   - 更新簡報或網頁：`git add . && git commit -m "slides: 更新 <日期/主題> 互動式教學簡報與 Graph 藍圖"`
   - 知識庫健檢：`git add . && git commit -m "lint: 執行知識庫跨篇矛盾檢查與研究靈感更新"`
4. **推送至遠端 GitHub**：執行 `git push origin main`（若需網路權限則開啟 `BypassSandbox: true` 執行 `git push`）。
