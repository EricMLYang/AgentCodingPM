---
name: prd-write
description: Write structured Product Requirements Document. Use when user says "幫我寫一份 PRD" or "這個功能的需求文件".
---

# Skill: prd-write — PRD 撰寫

## 用途（Purpose）

當需要為一個功能或專案撰寫產品需求文件（PRD）時，引導完成結構化撰寫流程。

- **輸入**：功能描述或需求概要
- **輸出**：完整 PRD（含 User Story、Acceptance Criteria、成功指標、風險等級）

## 何時使用（When to Use）

- 「幫我寫一份 PRD」
- 「這個功能的需求文件」

## 前置條件（Prerequisites）

- 有初步的功能構想或問題描述
- 知道目標使用者是誰

## 流程（Process）

### Step 1: 需求釐清 [interactive]

確認以下資訊：
1. **問題定義** — 要解決什麼問題？影響誰？
2. **目標使用者** — 主要使用者是誰？使用場景？
3. **風險等級** — L0/L1/L2？對 Agent 執行有什麼影響？
4. **成功定義** — 怎麼知道做成功了？可量化的指標？
5. **範圍限制** — 這次「不做」什麼？

### Step 2: 結構化撰寫 [autonomous]

按標準 PRD 結構產出：
1. 背景與動機（Why）
2. 目標使用者與場景（Who & When）
3. User Stories（含 Acceptance Criteria，需可轉為自動測試）
4. 非功能需求（效能、安全、相容性）
5. 風險等級與對應品質門檻（Gate A-E）
6. 成功指標（KPI / OKR alignment）
7. 開放問題與風險

### Step 3: 審查與迭代 [interactive]

逐項確認：
- AC 是否可測試且可被 Agent 執行？
- 範圍是否明確？
- 風險等級與品質門檻是否合適？

## 品質標準（Quality Gates）

- 每個 User Story 有至少 2 條 Acceptance Criteria
- AC 可直接轉為自動測試
- 有明確的風險等級與對應 Gate 檢查項
- 有明確的 out of scope 段落
- 有可量化的成功指標

## 已知限制（Limitations）

- 無法自動驗證技術可行性，需架構師審查
- 成功指標的目標值需要業務方確認
