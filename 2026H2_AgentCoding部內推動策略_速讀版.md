---
title: Agent Coding 部內推動策略（速讀版）
version: v1
created: 2026-07-02
status: draft
full_version: 2026H2_AgentCoding部內推動策略_實驗與推廣.md
---

# Agent Coding 部內推動策略——速讀版

## 一句話

**半年跑通 4 個有量測、有閉環的實驗，驗證過就 skill 化，部門基線衝到 L2 受控代駕——用數字推廣，不用口號。**

## 缺什麼（診斷）

1. **沒閉環**：4 月資安 Review 做完沒人追採納
2. **能力停在個人**：沒 skill 化、不可複製
3. **沒基線**：一條 lead time 數字都沒有，「AI 有幫助」全是體感

## 四個實驗

| # | 實驗 | 時程 | 怎麼做 | 成功判準 |
|---|---|---|---|---|
| 0 | 資安 Review 閉環補完 | 本週 | 找受測團隊真人確認 Critical 修了沒 | 採納率**有數字** |
| 1 | 資安 Review 常態化 | M1-M2 | `/security-review` 模式掛 1-2 個 R0/R1 repo；AI 只找問題不改 code | 攔到真問題 + 誤報率低到沒人想關它 |
| 2 | Loop Engineering 長任務 | M1-M3 | 真實遷移任務套 Initializer + JSON progress harness | 交付 + 第一條 lead time 基線 |
| 3 | 背景 Agent（數據線） | M2-M5 | 最小動作集（只能讀/改檔/呼叫 verifier）+ 雙層驗證（決定性 verifier → LLM judge）；**Databricks verifier 業界真空，做出來就是獨門能力** | 1 條 pipeline 任務 merge + verifier 有攔截實績 |
| 4 | PM/SA 自產案例 | M2-M6 | 用現成 skill 跑真實需求，記錄前後差異 | 產出時間/返工次數有對照 |

## 同一把尺

北極星 = **需求到驗證通過的 lead time**。每實驗回報三數字：lead time／採納率／攔截數 → 集中一頁**證據看板**，每月更新。

## 三條紅線（永不放寬）

1. AI 只能建議，**人類永遠 merge**（業界 61% AI PR 沒人 review——別學）
2. Credential **架構上**不進 agent 環境（Replit 9 秒刪庫的教訓）
3. 外部觸發的 AI workflow 一律人工先核准

## 怎麼推廣

- **證據看板**：只講量到的數字，不講願景
- **Skill 化**：驗證過才進 skill 庫；下個 repo 接入成本 < 半天
- **志願者梯次**：2-3 人起跑 → 每完成一實驗開 30 分 show-and-tell → 現場招下一梯，不指派
- **PR template AI 揭露**：讓採用可見，可見度本身就是推廣

**M6 目標**：2-3 repo 達 L2、6 人能獨立跑、看板連續 4 個月有數字。

## 避坑

- pilot 只挑 R0/R1 repo，別想順便解大問題
- 「做過一次」≠「skill 穩定」，沒數字不准複製
- 別引用已被推翻的數字（「Spotify 一半 PR 自動化」「HITL/HOTL/AWM 三層框架」）；可用：1,500+ merged PR、60-90% 時間節省

## 現在拍板

1. 實驗 0 **這週就動**，不需核准
2. 第一梯志願者 + pilot repo 名單（實驗 1、2 共用）
3. 實驗 2 挑一個**本來就要做**的遷移任務，不造題
