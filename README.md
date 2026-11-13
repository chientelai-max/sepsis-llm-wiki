# 🐕 敗血症強化學習（Sepsis RL）LLM Wiki 知識庫與三層知識圖譜教學簡報

本專案參考 Andrej Karpathy 的 **LLM Wiki** 架構，建立專注於 **「敗血症（Sepsis）強化學習臨床決策與醫療 AI 方法學審視」** 的結構化知識庫，並由吉祥物 **「汪博士（Dr. Wang）」** 領航導讀，編譯出 **34 頁日式清新（和紙暖白 × 抹茶竹綠 × 柴犬暖杏）三層知識圖譜（Three-Tier Graph）互動式教學簡報**。

---

## 🚀 快速預覽連結

- 📊 **34 頁互動式 HTML 教學簡報主檔**：[`outputs/html/2026-11-13-sepsis-rl-teaching-slides/2026-11-13-sepsis-rl-and-llm-teaching-slides.html`](outputs/html/2026-11-13-sepsis-rl-teaching-slides/2026-11-13-sepsis-rl-and-llm-teaching-slides.html)（若啟用 GitHub Pages，可直接透過首頁 [`index.html`](index.html) 瀏覽）
- 📝 **作業完整報告（含 Loop / Graph 設計與反思）**：[`HOMEWORK_SUBMISSION.md`](HOMEWORK_SUBMISSION.md)
- 🧭 **知識庫總目錄**：[`wiki/index.md`](wiki/index.md)
- 📋 **跨篇證據對照總矩陣**：[`wiki/synthesis/sepsis-evidence-matrix.md`](wiki/synthesis/sepsis-evidence-matrix.md)
- ⚙️ **Agent 核心規範與工作流 Schema**：[`AGENTS.md`](AGENTS.md)

---

## 🗂️ 專案三層架構

1. **`raw/`（不可變原始文獻層）**：收錄 2017–2024 年 9 篇敗血症強化學習代表性英文期刊論文 PDF 原檔。
2. **`wiki/`（持續複利編譯的知識圖譜層）**：包含 9 篇單篇深度拆解（`wiki/papers/`）與 10 篇跨文獻綜合比較、MDP 機制、生物標記、資料庫收案漏斗與 LLM 教學守則專頁。
3. **`outputs/`（高價值視覺化與教學產出層）**：包含 Stage 1 全域知識圖譜大綱藍圖、Stage 2/3 逐頁節點連線藍圖，以及最終 34 頁互動式 HTML 投影片。
