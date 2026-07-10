---
title: agent-skills 中與品質／維護／擴充相關的 skills 整理
date: 2026-04-29
status: raw
type: external-obs
source: tool_experiments/agent-skills/skills/
next-action: 評估吸收進 00_context/05-風險分層與五大品質門檻.md 與 .github/skills/ 模板
---

# agent-skills：品質／維護／擴充 速覽

掃 21 個 skills，篩出 7 個。共通骨架：`Overview → When to Use → Process → Common Rationalizations → Red Flags → Verification`。

## 七個目標 skill

- **code-review-and-quality**（品質）—— 五軸 Review + 嚴重度 prefix
- **code-simplification**（維護）—— 不改行為下降複雜度（Chesterton's Fence）
- **incremental-implementation**（品質+維護）—— 薄垂直切片、可編譯可回滾
- **api-and-interface-design**（擴充）—— 接口先行、加法優於改法（Hyrum's Law）
- **deprecation-and-migration**（維護）—— 程式碼是負債；Strangler/Adapter/FeatureFlag
- **documentation-and-adrs**（維護）—— 紀錄「為什麼」
- **test-driven-development**（品質）—— RED→GREEN→REFACTOR + Prove-It

## 共通優點與特別寫法（值得抄）

骨架優點：觸發條件機械化（`Use when…`）／Common Rationalizations 表內建糾偏／Red Flags + Verification 可當 PR 自檢／每 step 配範例。

特別寫法：**Hyrum's Law**（可觀測行為終將被依賴）／**嚴重度 prefix**（`Critical/Nit/Optional/FYI`）／**Approval standard**（只擋會降低 code health 的）／**Change Sizing 100/300/1000** + Stack/By-file/Horizontal/Vertical 拆分／**Chesterton's Fence**（反 LLM 直覺）／**Rule of 500**（重構改用 codemod/AST）／**Prove-It**（bug 先寫重現測試）／**DAMP over DRY in tests**／**Test Pyramid + Resource Model 雙軸**／**Code Is a Liability / Churn Rule / Zombie Code 6 月底線**／**One-Version Rule / Branded Types**／**ADR 強制 Alternatives Considered**。

## 吸收建議

1. 五軸 Review + 嚴重度 → `00_context/05`。
2. Rationalizations/RedFlags/Verification 三段 → `.github/skills/` 模板。
3. Change Sizing + Rule of 500 → Backlog 移交與 Review checklist。
4. Hyrum's Law / Code Is a Liability / Churn Rule → `.ai/principles.md`。
5. Prove-It Pattern → R1/R2 bug 修復寫進 Gate C。

風險：skills 交叉引用，抄單一要連帶看生態；轉中文要語感轉譯。

---

## 補充：對應書籍與 Agent 時代適用度

### 仍完全成立（Agent 時代更重要）

- **SWE at Google**（Winters 2020）—— Hyrum's Law / Test Sizes / One-Version Rule；agent 量產 code 行為依賴更爆炸
- **Chesterton 1929** —— Chesterton's Fence；反 LLM 直覺
- **Kent Beck** + **Feathers《Working Effectively with Legacy Code》** —— TDD / Prove-It
- **Nygard 2011** —— ADR；把「為什麼」灌回 agent 的主管道
- **Fowler** —— Strangler / Adapter / Feature Flag
- **Tidy First?**（Beck 2024）／**Continuous Delivery**（Humble 2010）—— 整理與行為變更分離
- **Domain Modeling Made Functional**（Wlaschin 2018）—— Branded Types；agent 時代翻倍
- **Checklist Manifesto**（Gawande 2009）—— Verification 範式

### 仍成立但要打折扣

- **Clean Code**（Martin 2008）—— SOLID 教條化已被批；改用 **Ousterhout《A Philosophy of Software Design》**（2nd 2021），deep modules 更貼近 agent 維運。
- **Pragmatic Programmer**（1999）—— 看 **2019 20 週年版**（已重寫）。
- **Refactoring**（Fowler 1999）—— 看 **2018 第 2 版**；Rule of 500 補 codemod/jscodeshift/AST。
- **PoEAA**（2002）—— 只挑 Strangler 等個別 pattern。
- **xUnit Test Patterns**（2007）—— 當術語字典。
- **Mocks Aren't Stubs**（Fowler 2007）—— 業界傾向 Detroit school（少 mock）；agent 時代 over-mock 會讓測試自我欺騙。

### 已過時或替換

- DDD Blue Book → **Khononov《Learning Domain-Driven Design》**（2021）
- Cohn Test Pyramid → 補 **Kent C. Dodds「Testing Trophy」**（2018）
- 早期 SOLID 教條 → **Ousterhout** + **Hickey「Simple Made Easy」**

### 當代文獻（agent-skills 顯然受影響）

- **Tidy First?**（Beck 2024）／**APOSD**（Ousterhout 2nd 2021）／**SWE at Google**（2020）
- **Will Larson《Staff Engineer》《An Elegant Puzzle》**
- **Anthropic 官方文件 + Simon Willison + Geoffrey Huntley 部落格（2024–2026）**

### 最濃縮對照

> agent-skills = **SWE at Google**（骨架）+ **Tidy First?**（小步整理）+ **APOSD**（複雜度）+ **Domain Modeling Made Functional**（型別）+ **Refactoring 2nd**（手法）+ **Checklist Manifesto**（封裝）+ **Anthropic/Willison/Huntley**（agent 實踐）。

Clean Code、PoEAA、DDD 藍書、Cohn Pyramid 降次要；**Tidy First? 與 Ousterhout** 才是 Agent 時代主軸。

---

## 補充 2：mabl 案例（見 `20260425_團隊創新推廣任務.md`）呼應對照

### 呼應 agent-skills

- **CLAUDE.md per repo + Cross Repo Base** ← `documentation-and-adrs`（把「為什麼」灌回 agent；context drift 40%→<5% 是直接驗證）
- **Repo Coordination Graph 850 行** ← `api-and-interface-design` 的 contract-first + Hyrum's Law（跨 repo 介面顯性化）
- **Validation skills 分技術棧 + router** ← `test-driven-development` 的 Pyramid + Resource Model 雙軸營運化
- **AI 不能 approve PR、只能 REQUEST_CHANGES/COMMENT** ← `code-review-and-quality` 的 Approval standard + 嚴重度 prefix；最終裁決留人
- **Auto-Fix 只處理 lint/import/型別 + circuit breaker** ← `incremental-implementation` 的 rollback-friendly + safe defaults
- **35+ Skills 分五類** ← agent-skills 21-skill 目錄的同範式，證明 skill-as-code 是團隊資產
- **Assumption drift 警告**（測試以為對的方式通過）← `test-driven-development` 的 "Test state, not interactions" + Beyonce Rule
- **PR 量級提高一個 order of magnitude** ← `code-review-and-quality` Change Sizing 反向證明：不拆 review 系統就崩

### 呼應書籍觀念

- Cross Repo Base + per-repo CLAUDE.md ← **SWE at Google** 的 monorepo + OWNERS 雙層治理
- Repo Coordination Graph ← **Hyrum's Law**：可觀測介面終將被依賴，寫成契約而非默契
- 用 production session 重構 agent ← **Tidy First?**：measurement, not guesswork
- AI 不能 approve PR ← **Checklist Manifesto**：critical decisions 必須留 human checkpoint
- AI healer 優化通過率而非商業意圖 ← **Domain Modeling Made Functional**：illegal states unrepresentable，型別鎖意圖
- Auto-fix circuit breaker ← **APOSD（Ousterhout）**：define errors out of existence，用機制取代信任
- Operations & Governance 為獨立 Layer 3 ← **Continuous Delivery**：ops 與 build 同層
- 開發流程整體 agent 化（不只寫程式） ← **Pragmatic Programmer 2019** + Huntley/Willison 一手實踐

### mabl 補上、書／skill 沒明說的三件原創

1. **Cross-repo coordination as graph-as-code**——異質 polyrepo + 多技術棧的 release order 顯性化藍圖，書與 skill 都沒給。
2. **Auto-fix circuit breaker 模式**——agent 自主修錯如何自我設限的具體門檻（連續兩次 / revert 過就停）。
3. **Confidence-based gating**（Part 2 預告）——混合 confidence 決定 agent 何時自主、何時停下問人。

### 最濃縮

> mabl = agent-skills 七件全套 + SWE at Google 治理骨架 + Tidy First? measurement-first 紀律 + Checklist Manifesto human-checkpoint 紅線 + 三件原創（Coordination Graph / Circuit Breaker / Confidence Gating）。

mabl 不是發明新觀念，而是把 agent-skills 與經典工程紀律**成建制落地**到 25 人 + 75 repos 規模——這正是「個人 Copilot → 處級 AI 工作方式」要證明可行的那一步。
