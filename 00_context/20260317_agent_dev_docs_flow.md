---
# 產品開發流程（AI 時代角色分工版）
---

# **1. 核心探索與規格化 (結合知識庫 RAG)**

```text
負責人：PM
協助角色：設計師 / 架構師 / SA (早期參與)

(知識庫 / 歷史脈絡 / 數據輸入) ──┐
                                  ▼
Inbox ──► Outcomes ──► OPP + Experiments ──► Backlog ──► User Stories ──► 初版 PRD
```

### PM 的核心責任

AI 可以幫忙整理資料，但 **PM 負責回答三個問題**

1️⃣ **這是不是值得解的問題**

* 使用者痛點
* 商業價值
* 優先順序

2️⃣ **成功長什麼樣**

* Outcomes
* 成功指標
* 使用情境

3️⃣ **範圍在哪裡**

* 做什麼
* 不做什麼

### AI 在這一段的角色

AI 可以幫助：

* 整理 RAG 歷史案例
* 生成初版 PRD
* 找需求矛盾
* 找漏掉的場景

但**AI 不負責決策優先順序**。

---

# **2. 專業領域驗證與文件擴充**

```text
負責人：架構師 / 設計師 / SA

初版 PRD
     │
     ├──► [技術高不確定性]
     │         └─► 架構師
     │               └─► Architecture / Prototype
     │
     ├──► [體驗 / 介面需求]
     │         └─► 設計師
     │               └─► Wireframe / UX Prototype
     │
     └──► [系統流程 / 資料 / 整合]
               └─► SA
                     └─► 流程 / 資料 / API / 商業規則

(Agent) ──► 檢查規格衝突 / 遺漏需求
```

### 架構師的核心責任

**技術可行性與系統邊界**

* 系統架構
* 技術選型
* scalability
* reliability

AI 可以幫忙生成架構草稿，但

**架構師負責判斷 trade-off**

---

### 設計師的核心責任

**體驗是否成立**

* 使用流程
* UX 邏輯
* 介面原型

AI 可以生成 UI
但 **不能替代使用者理解**

---

### SA 的核心責任

SA 在 AI 時代的定位會更清楚：

> **SA 是需求 → 系統 的翻譯者**

SA 主要負責：

* 系統流程
* 商業規則
* 資料結構
* API
* 系統整合
* 例外情境
* 權限 / workflow

SA 的工作本質是：

**讓需求變成可以被工程實作的系統描述**

---

# **3. 實作與知識回饋 (Closed Loop)**

```text
負責人：工程團隊 / QA / SA

定稿 PRD + 架構 + UX + 系統規格
        │
        └─► 資深工程師 / SA
                └─► 技術規格展開
                        └─► API / Data Model / 系統設計
                        
        └─► QA / Agent
                └─► 測試案例生成 / 驗收條件

                      ▼
                 工程實作與上線
                      ▼
Data Outcomes
                      ▼
Retrospective
                      ▼
知識庫更新 / Agent 規則更新
                      ▼
回流 Step 1
```

### 資深工程師的核心責任

AI 可以寫很多 code
但資深工程師負責：

* 系統品質
* 技術決策
* code review
* 風險控制

---

### QA 的核心責任

AI 可以生成測試案例，但 QA 負責：

* 測試策略
* 風險優先順序
* 系統驗收

---

### Agent 在這一段的角色

Agent 可以幫忙：

* 生成測試案例
* Code review
* 安全掃描
* 規格一致性檢查

但**最終驗收仍是人負責**

---

# AI 時代角色總結

整個團隊其實是在做一件事情：

**逐步降低不確定性**

```text
PM
降低「產品價值」的不確定性

架構師
降低「技術可行性」的不確定性

設計師
降低「使用體驗」的不確定性

SA
降低「系統邏輯」的不確定性

工程師
降低「實作品質」的不確定性

QA
降低「系統風險」的不確定性
```

AI 的作用：

**加速產出，但不負責收斂風險。**

---

# 一句話版本（給團隊對齊）

> 我們的開發流程不是為了寫更多文件，而是讓需求逐步收斂成「可設計、可開發、可驗證」的系統。
> AI 可以加速產出，但每一個角色仍負責收斂不同類型的不確定性。

------
------


下面這個是我幫你整理的 **AI 時代軟體團隊能力金字塔**。
它其實是很多公司在 AI coding agent 普及之後逐漸形成的一個共識：

> **越往上層，越接近「定義問題與系統結構」；越往下層，越接近「生成與實作」。**

AI 主要衝擊的是 **底層能力**，而不是頂層能力。

---

# AI 時代軟體團隊能力金字塔

```text
                ▲
                │
        產品與系統定義能力
                │
        (Problem & System Definition)
                │
        PM / Architect / Senior SA
                │
────────────────────────────────

        系統設計與架構能力
                │
        (System Architecture)
                │
        Architect / Senior Engineer / SA
                │
────────────────────────────────

        技術實作與工程能力
                │
        (Engineering Implementation)
                │
        Software Engineer
                │
────────────────────────────────

        程式生成與輔助能力
                │
        (AI Coding / Code Generation)
                │
        AI Agents / Coding Assistants
                ▼
```

---

# 每一層在 AI 時代的變化

## 1️⃣ 最底層：程式生成

角色

* AI coding agent
* Copilot
* Claude Code
* Codex
* Cursor
* Gemini Code Assist

能力

* code generation
* unit test generation
* code review
* refactoring

這一層正在 **高度自動化**。

所以未來很多人說：

> Junior engineer 的價值會下降。

因為 AI 可以做大量這一層的工作。

---

## 2️⃣ 技術實作層

角色

* Software Engineer
* Data Engineer
* Backend / Frontend

工作

* 系統實作
* debugging
* 效能優化
* integration

AI 可以 **加速 3–10 倍**

但這一層仍需要：

* 技術理解
* debugging
* 系統思維

所以工程師不會消失，但 **人數需求可能下降**。

---

## 3️⃣ 系統設計層

角色

* Architect
* Senior Engineer
* SA

能力

* system design
* data model
* distributed system
* API design
* integration
* reliability

AI 可以幫助：

* 生成架構草稿
* 找 trade-off
* 建議設計

但 AI 很難完全取代 **系統責任人**。

因為這一層涉及：

* 技術 trade-off
* 風險管理
* business constraint

---

## 4️⃣ 最上層：產品與系統定義

角色

* PM
* Architect
* Senior SA
* Tech Lead

能力

* problem framing
* product strategy
* system boundary
* business logic
* prioritization

這一層 AI **最難取代**。

因為這一層的本質是：

**決策與責任**

例如：

* 哪個需求值得做
* 哪個系統應該存在
* 哪個 trade-off 可以接受
* 哪個風險可以承擔

---

# SA 在金字塔中的位置

SA 的位置其實很有意思。

它剛好在 **產品與系統之間的橋樑**。

```text
產品世界
(PM)

    │

需求轉換
(SA)

    │

系統世界
(Architect / Engineer)
```

SA 的核心能力其實是：

**結構化思考**

例如：

* workflow
* business rule
* data flow
* exception case
* integration

AI 可以幫忙生成文件

但**很難完全理解企業流程與系統邏輯**。

所以未來 SA 很可能會變成：

> **System Designer / Product System Architect**

---

# AI 時代最稀缺的能力

如果用一句話總結：

**AI 讓「做事的人」變多
但「定義事情的人」仍然稀缺**

所以未來最有價值的能力其實是：

1️⃣ **問題定義能力**
2️⃣ **系統設計能力**
3️⃣ **跨領域整合能力**

而不是單純 coding。

---

# 為什麼你的流程其實很合理

你剛剛設計的流程其實有一個很好的特點：

你把流程分成三層：

```
探索
↓
驗證
↓
實作
```

這其實正好對應金字塔。

```text
探索
(Problem definition)

驗證
(System design)

實作
(Engineering)
```

所以你的流程其實是：

**AI 時代很合理的產品開發流程**。

---

# 最後給你一個我覺得很重要的觀察

未來 5–10 年

軟體團隊可能會變成：

```text
PM / Architect
        │
Senior Engineer / SA
        │
AI Agents
```

而不是傳統的：

```text
PM
 │
Architect
 │
Engineer
 │
Junior Engineer
```

**Junior engineer 的角色會逐漸被 AI agent 吃掉。**

但：

**SA / Architect / PM 反而會更重要。**

---




