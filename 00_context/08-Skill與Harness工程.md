---
title: Skill 設計與 Harness 工程
status: stable
last_review: 2026-04-26
sources:
  - 02_精選文章/20260424_agent_skills_analysis.md
  - 02_精選文章/20260425_AgentCoding進階用法_pattern整理.md
  - （原稿已合併刪除：20260309_Repo專家擴散方式）
tldr: Skill = 把工程紀律編譯成「Agent 可執行的狀態機」，三層正交（Skill 怎麼做 × Persona 誰看 × Slash Command 何時觸發）；Harness Engineering 是「設計讓 Agent 穩定產出的生產線」；Template Repo + init.sh 是擴散最小單位。
---

# Skill 設計與 Harness 工程

## TL;DR

- **Skill 不是 prompt pack，是「Agent 可執行的狀態機」**：觸發 → 階段推進 → 證據產出 → Red Flags 自檢 → 驗收 → 下一階段
- **三層正交**：Skill（*how*）× Persona（*who*）× Slash Command（*when*），可獨立演進、獨立審計、獨立替換
- **Anti-Rationalization Table** 是這個範式的招牌：列 Agent 偷懶藉口 + 反駁話術，把「規則」變成「可執行護欄」
- **Persona 不能呼叫 persona**，只有 user/command 能 orchestrate（避免無限遞迴 + 多 Agent 失控）
- **唯一允許的 multi-persona** = `/ship` 的 parallel fan-out + merge（同時跑 code-reviewer / security-auditor / test-engineer，最後合併報告）
- **Progressive Disclosure**：SKILL.md < 500 行；reference 拆出去；scripts 不算 context
- **擴散最小單位**：Template Repo + `init.sh` + 可替換 context 範本，三步驟（Use template / clone / 改 context）
- **Harness Engineering** = 把 workspace / terminal / git / file / plan / permission / context 整合進可控工作環境（BAT 是代表）

---

## 為什麼這篇是進階參考

當你準備：
- 寫自己的 skill 庫
- 評估要不要 fork `addyosmani/agent-skills`
- 設計 PM 流程的 anti-rationalization
- 規劃跨團隊 Skill 擴散
- 思考要不要做自己的 Agent 工作台

這篇給你結構規範與抄作業對象。

---

## 一、Skill 範式：三層拆解

```
Slash Command（when）
        │
        ↓
Persona（who）
        │
        ↓
Skill（how）
```

### Skills（怎麼做 / how）
- 工作流 + mandatory hops + 退場條件
- 例：`spec-driven-development`、`test-driven-development`、`code-review-and-quality`

### Personas（誰看 / who）
- 角色視角 + 輸出格式
- 例：code-reviewer / security-auditor / test-engineer

### Slash Commands（何時觸發 / when）
- 使用者入口 + orchestration layer
- 例：`/spec` → `/plan` → `/build` → `/test` → `/review` → `/ship`

### 為什麼這個三層拆解很關鍵
比一坨 system prompt 強得多 —— 它讓「**流程 / 角色 / 觸發時機**」可以獨立演進、獨立審計、獨立替換，等於把 agent 的行為治理從藝術變成工程。

### 三條 orchestration 規則
1. **Persona 不能呼叫其他 persona**，只有 user/command 是 orchestrator（避免無限遞迴 + multi-agent 失控，呼應 [`06-AgentCoding進階Pattern.md`](./06-AgentCoding進階Pattern.md) 的 Pattern C）
2. **唯一允許的 multi-persona** = `/ship` 的 parallel fan-out + merge（同時跑 code-reviewer / security-auditor / test-engineer，最後合併報告）
3. **明確拒絕「router persona 決定要呼叫誰」這種 anti-pattern**

---

## 二、SKILL Anatomy（6 個必備 section）

每份 SKILL.md 至少包含：

```markdown
---
name: <skill-id>
description: Use when <trigger condition>
---

## Overview
（一句話本質）

## When to Use
（什麼情境觸發；什麼情境不要用）

## Process
（步驟 + checkpoint + exit criteria）

## Common Rationalizations
（Agent 常用偷懶藉口 + 反駁）

## Red Flags
（這是出問題的徵兆）

## Verification
（驗收證據：tests passing / build output / runtime data）
```

### 為什麼 Verification 不可或缺
Skill 結尾必須要驗收證據。沒這條，「跑完 skill」沒有客觀證據，Agent 會用「看起來對」通過。

---

## 三、Anti-Rationalization Table（這個範式的招牌）

把「規則」變成「Agent 看完都不能繼續偷懶」的可執行護欄。

### 範例

| Rationalization | Reality |
|----------------|---------|
| "I'll write tests after the code works" | You won't. Tests after the fact test implementation, not behavior. |
| "I tested it manually" | Manual testing doesn't persist. Tomorrow's change might break it. |
| "This is too small for a skill" | Size isn't the criterion; risk is. |
| "I'll add error handling later" | Error handling added later catches symptoms, not root causes. |
| "It works on my machine" | Then ship your machine. Otherwise build a reproducible env. |

### 為什麼這條對 PM 流程也適用
我們的 PM 流程也有偷懶藉口：
- 「這個 OPP 太小不用驗證」
- 「這個風險等級先當 L0 跑跑看」
- 「使用者反映就一個，先不做」
- 「這個 metric 不重要，先不量」

寫成表逐條反駁，是把「資深 PM 的直覺」做成「Agent / 新人能執行的規則」。

---

## 四、Progressive Disclosure（Context 經濟學）

### 規則
- **SKILL.md 控制在 500 行內**
- supporting refs（testing-checklist、security-checklist 等）只在 skill 內部 reference 時才載入
- scripts 不算 context

### 為什麼
對應 [`06-AgentCoding進階Pattern.md`](./06-AgentCoding進階Pattern.md) Pattern H 的 Context 經濟學：read / think / write 都吃 token，不能塞越多越好。

### 對照
- 不好：把整個 500 行 test output 貼進 prompt
- 好：只貼關鍵 stack trace
- 不好：SKILL.md 寫 2000 行什麼都包
- 好：SKILL.md 500 行核心，reference checklist 拆出去按需載入

---

## 五、人類紀律 → Agent 可執行約束的 8 種轉化

| 人類的工程紀律 | 傳統做法（易繞過） | 編碼方式 |
|--------------|-----------------|---------|
| 「寫 code 前先寫 spec」 | 口頭提醒、Wiki 文件 | Frontmatter `description: Use when starting a new feature…` → 依 intent 自動觸發；未完成 Specify phase 不能進 Plan phase（gated workflow） |
| 「先寫失敗測試」 | Code Review 時才發現沒測試 | TDD skill 明確要求「test must fail first」；提供反例 |
| 「每個 PR 限 ~100 行」 | Review 時才發現太大 | git-workflow / code-review 兩個 skill 都把 100 行寫進 Process 步驟，主動觸發 split |
| 「不要事後合理化」 | 工程師自欺欺人 | **每個 skill 都有 Rationalizations 反駁表** |
| 「看到異常先停下來」 | 繼續堆功能直到壞更多 | Stop-the-Line Rule 寫進 debugging skill 的第一步 |
| 「證據本位決定 done」 | 用「看起來對」通過 | 每個 skill 的最後一節是 **Verification** |
| 「資深工程師 review」 | 人力瓶頸 | 3 個 agent personas（code-reviewer / security-auditor / test-engineer）+ `/ship` parallel fan-out |
| 「偏好特定 commit / PR 格式」 | 文件上寫、沒人讀 | Hooks（session-start.sh、sdd-cache-pre.sh、simplify-ignore.sh）在 session 生命週期強制執行 |

---

## 六、agent-skills 範式的 5 個最有設計感機制

> 來源：addyosmani/agent-skills（20 skills + 3 personas + 7 slash commands + hooks）

### 1. Anti-Rationalization Tables —— 招牌
針對 Agent 最常見的偷懶藉口逐條反駁。

### 2. Lifecycle Mapping
七個 slash command 對應軟體生命週期六階段：
`/spec` → `/plan` → `/build` → `/test` → `/review` → `/ship`（加 `/code-simplify`）

### 3. Personas vs Skills vs Commands 分層（明文寫進 AGENTS.md）
- **Skills** = 工作流（*how*），mandatory hops
- **Personas** = 角色 + 觀點（*who*）
- **Slash commands** = 使用者入口（*when*），orchestration layer

### 4. 唯一允許的 multi-persona
`/ship` 的 **parallel fan-out + merge** —— 同時跑 code-reviewer / security-auditor / test-engineer，最後合併報告。明確拒絕「router persona 決定要呼叫誰」。

### 5. Progressive disclosure
SKILL.md < 500 行；reference checklist 只在 skill 內部 reference 時才載入。

---

## 七、20 個 skills 涵蓋的工程紀律一覽

| 工程領域 | 核心紀律 | Skill |
|---------|---------|------|
| **需求與規格** | 先 Spec 後 Code；六大面向（Objective / Commands / Structure / Style / Testing / Boundaries）；Assumptions 明說 | `spec-driven-development`, `idea-refine` |
| **任務拆解** | 依相依圖排序；小到一 session 能驗證 | `planning-and-task-breakdown` |
| **實作節奏** | Vertical slice > horizontal；~100 行上限；implement→test→verify→commit | `incremental-implementation` |
| **測試** | TDD Red-Green-Refactor；80/15/5 金字塔；Prove-It Pattern；DAMP > DRY；Beyoncé Rule | `test-driven-development` |
| **API 設計** | 契約優先；Hyrum's Law；One-Version Rule；邊界驗證；錯誤語意一致 | `api-and-interface-design` |
| **Code Review** | 五軸（Correctness / Readability / Architecture / Security / Performance）；~100 行；Nit/Optional/FYI 標籤；「改善 > 完美」 | `code-review-and-quality` |
| **重構** | Chesterton's Fence；Rule of 500；行為不變前提下簡化 | `code-simplification` |
| **資安** | OWASP Top 10；Three-tier Boundary（Always/Ask First/Never）；參數化查詢 | `security-and-hardening` |
| **效能** | Measure-first；Core Web Vitals；anti-pattern 清單（N+1、同步阻塞、未分頁） | `performance-optimization` |
| **除錯** | Stop-the-Line；五步驟（Reproduce/Localize/Reduce/Fix/Guard） | `debugging-and-error-recovery` |
| **Git** | Trunk-based；atomic commits；commit as save-point；short-lived branches（1-3 天） | `git-workflow-and-versioning` |
| **CI/CD** | Shift Left；Faster is Safer；feature flags；staged rollouts | `ci-cd-and-automation`, `shipping-and-launch` |
| **汰除** | Code as liability；compulsory vs advisory deprecation；zombie code 移除 | `deprecation-and-migration` |
| **文件** | ADR 記錄「為什麼」而非「是什麼」；inline doc 只寫非顯而易見資訊 | `documentation-and-adrs` |
| **Context 管理** | Rules > Spec > Source > Error > History 五層 progressive disclosure | `context-engineering` |
| **來源驗證** | 所有框架決策必須引用官方文件 | `source-driven-development` |
| **前端** | Component architecture、design system、WCAG 2.1 AA | `frontend-ui-engineering` |

---

## 八、最值得學的 10 個 skill 技巧

### 1. `using-agent-skills` — Surface Assumptions 硬規則
Agent 動手前必須先列出預設的 4-5 點，用 `Correct me now or I'll proceed with these.` 句式強迫對齊 —— 把「隱性假設」變成「顯性契約」。

### 2. `idea-refine` — 7 種 Lens 發散法
Inversion / Constraint Removal / Audience Shift / Combination / Simplification / 10x / Expert Lens —— 強迫用不同角度各生一個替代案，避開 agent 只會「往顯而易見方向延伸」的通病。

### 3. `spec-driven-development` — Reframe Instructions as Success Criteria
把模糊需求（`Make the dashboard faster`）改寫成可測條件（`p95 < 500ms, LCP < 2s`）—— 把 PM 話術翻譯成工程契約的 SOP。

### 4. `planning-and-task-breakdown` — Plan Mode 禁寫 code
Planning 階段明文唯讀，避免 agent 邊想邊寫導致計畫被既成事實綁架。

### 5. `incremental-implementation` — Three Similar Lines beats a Premature Abstraction
明文禁止第一次看到重複就抽象，必須看到第三個使用場景才能抽 —— 直接反制 agent 的 DRY 強迫症。

### 6. `test-driven-development` — Prove-It Pattern for Bug Fixes
收到 bug 報告先寫測試複現、測試失敗才代表 bug 真實存在，修完才能算修好 —— 徹底斷絕「以為修好了但其實沒複現」的 bug 幽靈。

### 7. `context-engineering` — Trust Levels for loaded files
Source/Tests = Trusted；Config/Fixtures/External docs = Verify；User content/3rd party = Untrusted —— 在讀 context 時就先做防 prompt injection 的分級。

### 8. `source-driven-development` — Surface the Conflict
當 docs 與既有 codebase 衝突時，明文要求給用戶 A/B 選項而非靜默決定 —— 避免 agent 出現「我知道但我不說」的行為。

### 9. `code-review-and-quality` — Severity 三級標籤
Nit / Optional / FYI —— reviewer 明確區分「必改」「可改」「FYI」，避免 agent 把雞毛蒜皮當 blocker。

### 10. `shipping-and-launch` — Every Feature Flag has Owner and Expiration
Feature flag 不是永久 config，2 週內清理 —— 避免 flag 墳場。

---

## 九、按「場景痛點」反查 Skill

| 痛點 | 該開的 skill |
|------|------------|
| Agent 亂猜需求 | `spec-driven-development` + `using-agent-skills`（Surface Assumptions） |
| PR 太大 reviewer 爆氣 | `code-review-and-quality`（Change Sizing）+ `git-workflow`（Atomic Commits） |
| Agent 寫了一堆沒人會維護的 abstraction | `code-simplification` + `incremental-implementation`（Rule 0 Simplicity） |
| Agent 編造不存在的 API | `source-driven-development` + `context-engineering`（Trust Levels） |
| 修 bug 反覆復發 | `test-driven-development`（Prove-It Pattern） |
| UI 看起來很 AI | `frontend-ui-engineering`（Avoid AI Aesthetic） |
| Prompt injection 疑慮 | `api-and-interface-design` + `browser-testing-with-devtools`（Untrusted Data 規則） |
| Deploy 後才爆炸 | `shipping-and-launch`（Staged Rollout + Rollback）+ `ci-cd-and-automation`（Quality Gates） |
| 舊系統想不開拖垮團隊 | `deprecation-and-migration`（Code is Liability） |
| 半年後沒人記得當初為什麼這樣設計 | `documentation-and-adrs`（ADR supersede chain） |

---

## 十、評估 fork upstream agent-skills 的決策矩陣

### 直接 plugin install 的優點
- ✅ 20 個工程 skill 直接拿來用
- ✅ 自己只要寫 PM 專屬 skill（triage-inbox / move-inbox-to-opp / portfolio-report）
- ✅ 跟著 upstream 演進

### 需要本地化的部分
- ⚠️ 英文（團隊主語繁中）→ 評估翻譯成本 vs 直接英文
- ⚠️ Anti-rationalization 是針對「工程師偷懶」，PM 痛點是「PM 流程偷懶」要自己寫
- ⚠️ Databricks / 影像分析慣例需要本地 skill

### 不該抄的部分
- ❌ 不要做 plugin marketplace（我們是 PM Repo，不是 skill 發行商）
- ❌ 不要學 Addy 那麼大規模（20 skill × 6 section × 5 anti-rationalization = 維護成本爆表）—— 挑前 8 個關鍵 skill 強化即可
- ❌ 不要 GUI / RAG / 閉源產品化

### 我們可以直接抄的 5 個 pattern
1. **採用 SKILL anatomy 6 section**
2. **每個 skill 加 Anti-Rationalization table**（對應 PM 流程偷懶藉口）
3. **跑 `/spec`、`/plan`、`/build`、`/test`、`/review`、`/ship` 跟我們 skill 對標**，看缺哪些 phase
4. **Progressive disclosure**：SKILL.md < 500 行，reference 拆出去
5. **Personas vs Skills vs Commands 分層原則**寫進 skill index

---

## 十一、Harness Engineering：超越 Skill 的工作環境設計

### 三個代表
| 工具 | 規模 | 核心 IP | 對應 Pattern |
|------|------|--------|------------|
| **BAT (Better Agent Terminal)** | ~38k 行 TS/TSX | 程式架構（PTY/IPC/Worktree）| A + G + H |
| **Spectra** | binary only | prompt/skill 設計（assumptions mode） | A + C + E |
| **Agent Skills** | ~6k 行 Markdown | SKILL anatomy + anti-rationalization | C + E + J |

### Harness 解決的核心問題

> 不只是叫 Agent 幫我寫 code，而是設計一個**更可控、更安全、更有效率的 Agent 工作環境**。

進階使用者的工作流：
```
我 → 自己的 Agent 工作台
   → 控制 workspace / terminal / git / file / plan / permission / context
   → 再去驅動 Claude Code / Codex / terminal
```

### Harness 提供的能力（以 BAT 為例）
- **Workspace 概念**：每個 workspace 綁一個資料夾，內含多個 terminal + Claude/Codex agent panel
- **Cache 經濟學內建到 UI**：cache read/write 拆 5min vs 1h TTL、按 Opus/Sonnet/Haiku 分價
- **Git worktree 隔離**：agent 在獨立 worktree 跑，不破壞主 working tree
- **Procfile / overmind 整合**：把 stateful process（DB/docker/cron）merge 進主視窗
- **Token / cost / cache 可視化**：自製 statusline，cache TTL 倒數，過期 >150k token 跳預送警告
- **`/auto-continue`**：偵測 codex 停下後自動送 prompt 繼續

### Harness 的核心信念
> **「畫面上每一塊都是我自己掌控的工具，AI 大廠怎麼改版我都能順著吃到，不用被任何單一 IDE/CLI 綁住。」** — Tony Wang

對應 Pattern A（Harness Engineering）：頂尖玩家自己做工具。

---

## 十二、團隊不需要做 BAT，但要學 Harness 思維

不需要從頭做 Better Agent Terminal，但可以馬上升級工作流：

### 1. 建立自己的 Agent 使用規則
在 repo 放 `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` / `.github/copilot-instructions.md`，明寫：
```text
改檔前要先 plan
不要直接動 database schema
不要新增大型套件
要遵守現有架構
做完要列 changed files
要提供測試或手動驗收方式
```

### 2. Plan-first prompt 變預設
不要再只說「幫我做這個功能」，要改成：
```text
請先讀 repo。
不要修改檔案。
先提出 plan、相關檔案、風險、驗收方式。
等我確認後再實作。
```

### 3. Worktree / branch 隔離
- 每個 Agent 任務開一個 branch
- 大改動不要直接在 main working tree 做
- 做完先看 diff 再決定 merge

### 4. 任務記錄
每次任務簡單記：目標、用了什麼工具、改了哪些檔案、遇到什麼問題、哪些 prompt 有效、哪些地方失控、下次怎麼改進。

---

## 十三、擴散：Template Repo 三步驟

### 提供者要做的 3 件事

#### ① 做一個 Template Repo
```
pm-agent-template/
  context/
  agent/
  scripts/init.sh
  README.md
```
然後設成 **GitHub Template Repository**。

#### ② 在 context 放可替換範本
```
context/project.template.md
```
讓別人之後可以改成自己的專案內容。

#### ③ 提供初始化 script
```
scripts/init.sh
```
功能只有一件事：把 template context 變成 project context。

### 使用者只需要 3 步

1. **用 Template 建立自己的 repo** —— GitHub「Use this template」
2. **clone repo**
3. **執行初始化** —— `bash scripts/init.sh`，然後改自己的 `context/project.md`

### 為什麼這版最容易擴散
使用者只需要記住三件事：`Use template / run init.sh / edit context`。不需要記命令、不需要懂結構、不需要看文件。

---

## 十四、給 AgentCodingPM 的具體下一步

### 短期（這季）
1. **採用 SKILL anatomy 6 section**：把現有 `.github/skills/` 升級到包含 Common Rationalizations + Red Flags + Verification
2. **寫 4-6 條 PM 流程的 Anti-Rationalization**：對應 inbox triage / OPP 驗證 / Backlog handoff / 風險分級偷懶藉口
3. **SKILL.md 限制 500 行**：超過的拆出 reference

### 中期（這半年）
1. **Lifecycle Mapping 對齊**：把 `Inbox → Outcomes → OPP → Backlog → PRD → 工程` 對應到 `/triage` → `/promote` → `/validate` → `/handoff` slash command
2. **建 Template Repo**：讓子 PM 三步驟複製這套架構
3. **觀察 Anthropic Plugin Marketplace 演進**：決定 fork 還是 plugin install agent-skills

### 長期（觀察 trigger）
- Multi-Agent 何時值得投入？等模型能 reliably 切 context 不變笨
- 自己做 Harness 工作台？等團隊規模超過 20 人 + 有跨 Squad 一致性需求

## 與其他主題的連結

- 進階 Pattern 與 Skill 的關係 → [`06-AgentCoding進階Pattern.md`](./06-AgentCoding進階Pattern.md)
- 工具選擇支撐 Skill 執行 → [`07-工具地圖與防線設計.md`](./07-工具地圖與防線設計.md)
- 風險分層怎麼跟 Skill 觸發條件對應 → [`05-風險分層與五大品質門檻.md`](./05-風險分層與五大品質門檻.md)
