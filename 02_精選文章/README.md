---
title: 精選文章說明
last_review: 2026-04-26
---

# 02_精選文章 — 值得保留的外部觀察與深度整理

## 是什麼

這裡放的是**有獨立保留價值的外部觀察、深度研究、社群整理**。不是隨手 dump 的封存區，是經過篩選的精選素材。

判斷「值得精選」的條件（任一即可）：
- 外部來源（研究報告、社群觀察）有原文價值，無法用萃取覆蓋
- 含完整引用鏈或工具表格，萃取會損失細節
- 近期（2026-04-24 之後）的素材，還在消化中
- 有獨特角度或論證，可作為未來爭議時的回溯依據

## 為什麼不再叫 archive

`archive` 是「丟掉但不刪」的概念，會無限堆積。`精選文章`是「值得反覆看」的概念，**有上限、要篩選**。每加一份就要問「這值得長期保留嗎」。

## 怎麼用

- **Agent 載入 context 時**：請只讀 `00_context/`，**不要載入 02_精選文章/**
- **想看原文 / 找引用**：可以查這裡
- **新增**：只放符合「精選條件」的，不要當垃圾桶
- **過期判斷**：每季 review，超過 1 年沒被引用且內容已完全反映在 context 的，可以刪

## 現有清單（6 篇）

### 4/24 之後（保留中，可能還會被消化或升級）

#### `20260424_agent_skills_analysis.md`
- 來源：addyosmani/agent-skills 評估 + 40 人部門三階段 rollout 計畫
- 為什麼保留：完整的導入策略 + 21 個 skill 拆解，是當前最有實作價值的素材
- 對應 context：`08-Skill與Harness工程.md`
- 後續：可能升級為 OPP / Backlog

#### `20260425_AgengCoding進階用法.md`
- 來源：Tony Wang / 高健龍 / wchung 的 FB / 部落格觀察原文
- 為什麼保留：原文有獨特語感（context 戰爭迷霧、相見恨晚、模型還在童年），萃取會失真
- 對應 context：`06-AgentCoding進階Pattern.md`、`09-外部資源與閱讀清單.md`

#### `20260425_AgentCoding進階用法_pattern整理.md`
- 來源：上面那份的 pattern 萃取 + Repo 深入解析（BAT / Spectra / Agent Skills）
- 為什麼保留：含 7.1 / 7.2 / 7.3 三個 Repo 的深度技術反推，是判斷「自己做工具 vs 用既有工具」的決策素材
- 對應 context：`06-AgentCoding進階Pattern.md`、`07-工具地圖與防線設計.md`、`08-Skill與Harness工程.md`

### 4/24 之前（外部高價值素材，已篩選保留）

#### `20260314_2026年 AI Agent 軟體開發生命週期與工程實務深度解析報告.md`
- 來源：35KB 外部深度研究（含 Codegen / Cursor / Claude Code / Devin / Cline / Roo Code / Aider 完整對比表）
- 為什麼保留：含完整廠商對比表（功能、定價、自主性架構），萃取進 `07` 時只保留結論，原文是工具選型的權威來源
- 對應 context：`07-工具地圖與防線設計.md`

#### `20260314_ai_code_review_search.md`
- 來源：AI Code Review 搜尋整理（2026-03 時點）
- 為什麼保留：完整的 6 層防線論述 + 14 條外部引用，可追溯每個工具的判斷依據
- 對應 context：`07-工具地圖與防線設計.md`

#### `20260302_learning_resource.md`
- 來源：學習資源彙整（Stanford CS146S 完整 15 週課綱、台大 Web Programming、AI 編程上下文管理演講、Anthropic effective harnesses）
- 為什麼保留：Stanford 與台大的詳細課綱在 `09` 只列連結，原文有完整課程內容可挑章節推薦
- 對應 context：`09-外部資源與閱讀清單.md`

## 已刪除清單（已完整萃取進 context）

下列 14 份原稿在 2026-04-26 重整時刪除，內容已 100% 反映在對應 context：

| 原檔 | 對應 context |
|------|------------|
| `20260302_0.ai_coding_main_role.md` | `01-定位與判斷權.md` |
| `20260302_0.full_ai_coding_keypoint.md` | `01-定位與判斷權.md` |
| `20260302_工程師新素養.md` | `01-定位與判斷權.md` |
| `20260307_系統動力學來看AgentCoding新瓶頸.md` | `02-瓶頸遷移與量測指標.md` |
| `20260309_Agent coding 之後...需求到驗證通過的速度.md` | `02-瓶頸遷移與量測指標.md` |
| `20260307_AgentCoding等級.md` | `03-成熟度等級_L0至L4.md` |
| `20260302_1.executable_requirements.md` | `04-輕量開發流程.md` |
| `20260307_CoreNote_小團隊全流程AgentCoding.md` | `04-輕量開發流程.md` |
| `20260317_agent_dev_docs_flow.md` | `04-輕量開發流程.md` |
| `20260302_2.quality_gates.md` | `05-風險分層與五大品質門檻.md` |
| `20260407_CodeReview.md` | `05-風險分層與五大品質門檻.md` |
| `20260309_Repo專家擴散方式.md` | `08-Skill與Harness工程.md` |
| `20260302_copilot-instructions-vs-claude-md.md` | `09-外部資源與閱讀清單.md` |
| `20260401_pm_head_dispatch.md` | （任務派發，已執行：建立 AI Coding Review 流程→產出 0407 CodeReview） |

刪除原則：**自己的思考紀錄已被改寫進 context** + **沒有外部引用價值** = 不需要保留原稿。

## 不要做的事

- ❌ 不要把這裡當作 inbox 的下一站（inbox 升級後優先改寫進 `00_context/`，原稿如無外部引用價值就刪）
- ❌ 不要在這裡修改檔案內容（要改就在 `00_context/` 改）
- ❌ 不要為了「看起來有東西」保留沒外部引用價值的舊思考紀錄
