---
title: 精選文章說明
last_review: 2026-07-10
---

# 02_精選文章 — 只讀歸檔：外部深度素材 + 已定版舊計劃

## 是什麼

放**有獨立保留價值**的外部觀察、深度研究，以及**已定版不再更新**的歷史計劃。
不是垃圾桶：有上限、要篩選，每加一份要問「這值得長期保留嗎」。

規則：
- **Agent 載入 context 時只讀 `00_context/`，不要載入這裡**；查原文 / 找引用時才來
- 不在這裡修改內容（定版即只讀）
- 每季 review：超過 1 年沒被引用且內容已完全反映在 context 的，可刪

## 現有清單（2026-07-10 重整後，10 篇）

### 已定版舊計劃（回溯用，被 AGENTS.md「證據解鎖擴圈」原則引用）

| 檔案 | 為什麼保留 |
|------|-----------|
| `2026H2_落地改造計劃書.md` | 2026-06-21 定版。「服務隊」身份與「repo 瓶頸是沒有消費者」的診斷仍有效；管線改造部分已被 new_plan 取代 |
| `2026H2_AgentCoding推動小組_由小到大策略.md` | 2026-07-02 定版。Ring 0–5 證據解鎖擴圈是現行推動節奏的出處；引用同資料夾的 mabl 案例與六大主題研究 |

### 支撐研究（new_plan 與推動策略的證據層）

| 檔案 | 為什麼保留 |
|------|-----------|
| `20260701_六大主題深度研究與推廣策略.md` | Loop Engineering / 雲端 Agent / 資安 / 最小變異開發 / CR 治理 / PM 輔助六主題；附錄含已查核推翻的數字清單（對外溝通防雷） |
| `20260425_團隊創新推廣任務.md` | mabl 案例（25 人 75 repos 制度化）：Coordination Graph / Circuit Breaker / Confidence Gating 三件原創 |
| `20260429_agent-skills-品質維護擴充.md` | 21 個 skill 拆解 + 工程經典書籍對照 + mabl 呼應；部門 repo 資產治理（asset schema / Rationalizations / Red Flags 範式）的直接素材 |

### 外部素材（context 的引用來源）

| 檔案 | 對應 context |
|------|-------------|
| `20260424_agent_skills_analysis.md` | `08-Skill與Harness工程`（addyosmani/agent-skills 評估 + 40 人三階段 rollout） |
| `20260425_AgengCoding進階用法.md` | `06-進階Pattern`（Tony Wang / 高健龍 / wchung 原文，語感無法萃取） |
| `20260425_AgentCoding進階用法_pattern整理.md` | `06`、`07`、`08`（含 BAT / Spectra / Agent Skills 三 repo 技術反推） |
| `20260314_ai_code_review_search.md` | `07-工具地圖與防線設計`（6 層防線論述 + 14 條引用鏈） |
| `20260302_learning_resource.md` | `09-外部資源清單`（Stanford CS146S / 台大課綱完整版） |

## 已刪除（內容已 100% 反映在 context，無外部引用價值）

- 2026-04-26 重整刪 14 份原稿（清單見 git 歷史 `02_精選文章/README.md` 舊版）
- 2026-07-10 重整再刪：`20260314_2026年AI Agent 軟體開發生命週期報告`（工具對比已過時）、
  inbox 殘留的 3 月已萃取原稿 6 份（executable_requirements / quality_gates / learning_resource 重複本 /
  AgentCoding等級 / CoreNote小團隊全流程 / 系統動力學新瓶頸）、
  inbox 管理鷹架 3 份與 `00_context/00-README.md`（索引併入 AGENTS.md）

刪除原則：**自己的思考紀錄已被改寫進 context** + **沒有外部引用價值** = 不保留原稿。
