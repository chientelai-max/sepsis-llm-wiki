---
title: "敗血症 LLM Wiki 知識庫操作時間軸紀錄 (Chronological Log)"
type: log
tags: [sepsis, log, changelog, history]
created: 2026-11-13
updated: 2026-11-13
status: evergreen
---

# 敗血症 LLM Wiki 知識庫操作時間軸紀錄 (Log)

本檔案為 Append-only 的時間軸紀錄，記載每一次的 `ingest`（消化論文/指引）、`query`（查詢與知識回存）與 `lint`（知識庫健檢、經典文獻補齊與新題目合成）。
每筆紀錄標題統一使用 `## [YYYY-MM-DD] <action> | <標題>` 格式，可使用 `grep "^## \[" wiki/log.md` 快速檢索。

---

## [2026-11-13] init | 完成 9 題深度需求訪談並建立 `sepsis-llm-wiki` 知識庫架構

- **核心規範建立 ([`AGENTS.md`](../AGENTS.md))**：
  - 鎖定「臨床醫學與治療指引」×「醫療 AI 與臨床預測模型」雙主軸。
  - 定義單篇敗血症論文 6 大必抓欄位（證據等級標籤、Sepsis-3 與 $T_0$ 定義、場域與資料庫、PICO 與 95% CI、完整 AI 指標 AUROC/AUPRC/PPV/NPV 與 TRIPOD+AI 檢核、臨床落地痛點）。
  - 建立「五大敗血症方法學陷阱」強制把關機制與「回溯性 AI vs. 臨床指引 (SSC) 安全防線」。
- **視覺素材庫建立 ([`圖片/dr-wang-catalog.md`](../圖片/dr-wang-catalog.md))**：
  - 收錄 01～05 號「汪博士 (Dr. Wang)」插圖，配置「臨床珍珠 (Clinical Pearls)」、「方法學陷阱審視 (Methodological Pitfalls)」與「醫療證據安全防線」專屬 Callout 範本。
- **初始知識庫頁面建立**：
  - [`wiki/index.md`](./index.md)（含敗血症 7 大必讀基石文獻雷達清單）
  - [`wiki/synthesis/sepsis-evidence-matrix.md`](./synthesis/sepsis-evidence-matrix.md)（含 AI 預測模型大表、臨床試驗/指引大表、五大陷阱追蹤區）
  - [`wiki/glossary/sepsis-abbreviations.md`](./glossary/sepsis-abbreviations.md)（收錄 Sepsis-3, SOFA, qSOFA, Lactate, SSC, AUROC, AUPRC, PPV, NNE, TRIPOD+AI 等核心縮寫）

---

## [2026-11-13] ingest | 批次消化 9 篇敗血症強化學習 (RL) 輸液與升壓藥決策論文 (2017–2024)

- **步驟 0：原始英文 PDF 歸檔 (`raw/papers/`)**：
  1. [`2017-raghu-continuous_state_space_drl_mlhc.pdf`](../raw/papers/2017-raghu-continuous_state_space_drl_mlhc.pdf) (`Raghu et al., 2017a [MLHC]`)
  2. [`2017-raghu-drl_sepsis_treatment_nips.pdf`](../raw/papers/2017-raghu-drl_sepsis_treatment_nips.pdf) (`Raghu et al., 2017b [NeurIPS ML4H]`)
  3. [`2018-komorowski-ai_clinician_nature_medicine.pdf`](../raw/papers/2018-komorowski-ai_clinician_nature_medicine.pdf) (`Komorowski et al., 2018 [Nature Medicine]`) ⭐ **解鎖必讀基石文獻！**
  4. [`2018-peng-mixture_of_experts_kernel_drl_amia.pdf`](../raw/papers/2018-peng-mixture_of_experts_kernel_drl_amia.pdf) (`Peng et al., 2018 [AMIA]`)
  5. [`2022-nanayakkara-cardiovascular_uncertainty_drl_plos.pdf`](../raw/papers/2022-nanayakkara-cardiovascular_uncertainty_drl_plos.pdf) (`Nanayakkara et al., 2022 [PLOS Digital Health]`)
  6. [`2023-yu-dirl_mt_dueling_weight_ope_bmc.pdf`](../raw/papers/2023-yu-dirl_mt_dueling_weight_ope_bmc.pdf) (`Yu & Huang, 2023 [BMC Med Inform Decis Mak]`)
  7. [`2023-lin-ddpg_dosing_strategy_bmc.pdf`](../raw/papers/2023-lin-ddpg_dosing_strategy_bmc.pdf) (`Lin et al., 2023 [BMC Med Inform Decis Mak]`)
  8. [`2023-wu-wd3qne_human_expertise_npj_digit_med.pdf`](../raw/papers/2023-wu-wd3qne_human_expertise_npj_digit_med.pdf) (`Wu et al., 2023 [npj Digital Medicine]`)
  9. [`2024-choi-highlight_dddqn_sepsis_comm_med.pdf`](../raw/papers/2024-choi-highlight_dddqn_sepsis_comm_med.pdf) (`Choi et al., 2024 [Communications Medicine]`)
- **步驟 1：建立 9 篇單篇論文深度解析筆記 (`wiki/papers/`)**：
  - [2017-raghu-continuous-state-drl-mlhc.md](./papers/2017-raghu-continuous-state-drl-mlhc.md)
  - [2017-raghu-shaped-reward-drl-nips.md](./papers/2017-raghu-shaped-reward-drl-nips.md)
  - [2018-komorowski-ai-clinician.md](./papers/2018-komorowski-ai-clinician.md)
  - [2018-peng-moe-kernel-drl.md](./papers/2018-peng-moe-kernel-drl.md)
  - [2022-nanayakkara-cardiovascular-uncertainty-drl.md](./papers/2022-nanayakkara-cardiovascular-uncertainty-drl.md)
  - [2023-yu-dirl-mt-dueling-weight-ope.md](./papers/2023-yu-dirl-mt-dueling-weight-ope.md)
  - [2023-lin-ddpg-dosing-strategy.md](./papers/2023-lin-ddpg-dosing-strategy.md)
  - [2023-wu-wd3qne-human-expertise.md](./papers/2023-wu-wd3qne-human-expertise.md)
  - [2024-choi-highlight-dddqn.md](./papers/2024-choi-highlight-dddqn.md)
- **步驟 2 ~ 4：跨篇主題專頁編譯與研究靈感提煉（共新增/更新 9 個核心樞紐頁面）**：
  - AI 演算法專頁：[rl-fluid-vasopressor-dosing.md](./ai-tasks-and-models/rl-fluid-vasopressor-dosing.md)
  - 臨床治療專頁：[fluid-and-vasopressor-strategy.md](./clinical-management/fluid-and-vasopressor-strategy.md)
  - 資料庫世代專頁：[mimic-iii-and-eicu-sepsis-cohorts.md](./cohorts-and-databases/mimic-iii-and-eicu-sepsis-cohorts.md)
  - 臨床評分專頁：[sofa-and-sepsis3-in-rl.md](./scoring-and-criteria/sofa-and-sepsis3-in-rl.md)
  - 生物標記專頁：[key-rl-features-bun-lactate-pao2-pt.md](./biomarkers-and-features/key-rl-features-bun-lactate-pao2-pt.md)
  - 研究靈感專頁：[rl-sepsis-research-gaps-and-ideas.md](./ideas/rl-sepsis-research-gaps-and-ideas.md)
  - 跨篇證據大表：[sepsis-evidence-matrix.md](./synthesis/sepsis-evidence-matrix.md)
  - 縮寫字典與總目錄：[sepsis-abbreviations.md](./glossary/sepsis-abbreviations.md)、[index.md](./index.md)


---

## [2026-11-13] query | 探討「LLM 與醫療 AI 在教學上的注意事項」並產出 12 頁互動式教學簡報 HTML

- **知識回存 (`wiki/synthesis/`)**：
  - 建立教學指南專頁：[llm-and-rl-teaching-guide.md](./synthesis/llm-and-rl-teaching-guide.md)（彙整傳統 RAG 順從偏差風險與醫療 AI 教學 6 大守則）。
- **視覺化教學投影片產出 (`outputs/html/2026-11-13-sepsis-rl-teaching-slides/`)**：
  - 建立依日期命名之專屬簡報資料夾：`outputs/html/2026-11-13-sepsis-rl-teaching-slides/`（內含完整 HTML 投影片與 5 款汪博士插圖素材）。
  - 互動式投影片主檔：[2026-11-13-sepsis-rl-and-llm-teaching-slides.html](../outputs/html/2026-11-13-sepsis-rl-teaching-slides/2026-11-13-sepsis-rl-and-llm-teaching-slides.html)（含 12 頁投影片、鍵盤左右鍵切換、目錄跳轉、汪博士課堂講稿提示、9 篇論文互動式檢索器與 3 題隨堂互動小測驗）。

## [2026-11-13] query | 升級為 32 頁「日式清新（和紙暖白 × 抹茶竹綠 × 柴犬暖杏）」多流程圖深度教學簡報
- **Action**: 根據使用者確認之 4 項設計決策（和紙抹茶柴犬杏配色、每篇論文固定 3 頁深度拆解、4 大視覺化流程圖類型、85% 篇幅聚焦 9 篇論文逐篇圖解並將 LLM 教學守則融入每篇第 3 頁汪博士提醒框），重新生成完整 32 頁互動式 HTML 投影片：`outputs/html/2026-11-13-sepsis-rl-teaching-slides/2026-11-13-sepsis-rl-and-llm-teaching-slides.html`。

## [2026-11-13] query | 完成四階段 Graph 導向 34 頁互動式知識圖譜投影片（Stage 1–4 & Gate 1–4 驗收）
- **Action**: 依序完成：
  1. **Stage 1（全域知識圖譜與 34 頁大綱藍圖）**：建立 `stage1_macro_graph_and_outline.md` 並通過 Gate 1 檢查。
  2. **Stage 2 & 3（34 頁逐頁 Graph 節點與連線藍圖 ＋ 日式清新 5 色節點 / 4 線型視覺語意規範）**：建立 `stage2_slide_by_slide_graph_blueprint.md` 並通過 Gate 2/3 檢查。
  3. **Stage 4（最終一次性編譯與 Gate 4 驗收）**：生成完整 34 頁 `2026-11-13-sepsis-rl-and-llm-teaching-slides.html`，包含 Tier-1 雙互動知識圖譜導覽頁（Slides 02–03）、Tier-2 跨論文定位麵包屑（9 個）、Tier-3 單頁內 110+ 個語意節點與有向邊，以及汪博士隨堂翻卡測驗（Slide 34）。
