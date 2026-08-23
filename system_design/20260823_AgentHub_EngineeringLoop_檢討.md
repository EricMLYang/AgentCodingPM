# AgentHub 需求計劃 v0.2 — Engineering Loop 檢討

> **文件狀態**：檢討草稿（2026-08-23）。針對 `20260823_AgentHub_需求計劃_v0.2.md` 的需求 4「網站本身用 agent loop 持續精進」做檢討：
> v0.2 承諾了「任何一次 agent session 都能安全接手改進」，但沒有定義 loop 具體長什麼樣——Agent 靠什麼 review、測試、被驗收。
> 本文件不改 v0.2 本身；檢討結論供 §9 的「技術規格」與「開發計劃」兩份文件吸收。

---

## 1. 檢討結論（一段話）

v0.2 把「用 agent loop 開發」寫成了需求（需求 4）和場景（場景 3：讀說明 → 改 → 跑驗證 → 過了才合併），方向正確；但**「文件、測試、驗證方式齊備」的「齊備」沒有定義**，四條成功判準也沒有一條在驗證需求 4。換句話說：合併時的驗收體系（R0–R3、Gate A–E）已由 `00_context/05` 接住，**缺的是「Agent 工作中怎麼自己拿到『我做對了嗎』的證據」**——這正是開發計劃動工前要先佈置好的東西。

## 2. v0.2 已寫到 vs 沒寫到


| 面向     | v0.2 已寫到                         | 沒寫到（本檢討要補的）                           |
| ------ | -------------------------------- | ------------------------------------- |
| 開發紀律   | §2 需求 4、§7：沿用 R0–R3 與 Gate 門檻    | 這個系統裡**哪些部分天生是 R2/R3**、checkpoint 設在哪 |
| 驗證     | 場景 3：「改完跑驗證、過了才合併」               | 驗證**是什麼**：跑哪些命令、怎樣算過、Agent 怎麼自己跑      |
| 可接手性   | 需求 4：「任何一次 agent session 都能安全接手」 | 「安全接手」的**可測定義**與對應的成功判準               |
| MCP 品質 | §8.2 鐵律：帶出處、查不到就說查不到             | 鐵律**如何變成可執行的測試**，讓 Agent 在 loop 內自己驗  |
| 觀看體驗   | 需求 1：乾淨、快、繁中舒服、手機能看              | 體驗性需求**哪些能自動驗、哪些明定留給人工**驗收            |
| 內容維護   | 需求 2：agent 一句話上架、留記錄             | 維護動作的**閉環確認**：改完 Agent 怎麼確認真的生效       |


## 3. 缺口逐條與建議補法

### 缺口 1：「齊備」沒有定義 → 給一張最小佈置清單

需求 4 要求「文件、測試、驗證方式齊備」。建議在開發計劃中把「齊備」定義為 repo 必備的六件事，**在第一行功能程式碼之前先佈好**（先搭驗證環境，再開始做功能）：

- [ ] AGENTS.md（或 CLAUDE.md）有「驗證命令」區塊：跑測試、lint、type check 的確切指令＋怎樣算通過，Agent 不用猜
- [ ] 存在**秒級可跑的測試子集**（loop 內反覆跑）；全量與整合測試放 CI
- [ ] 每個開發任務有需求卡＋三句驗收（成功長什麼樣／怎麼驗證／失敗最可能在哪，見 `00_context/04`）
- [ ] 任務動工前判 R0–R3
- [ ] 停止與重試規則寫進指令檔（見缺口 5）
- [ ] Agent 操作記錄可回看（改了什麼、跑了什麼、結果如何）

### 缺口 2：需求 4 沒有成功判準 → 建議補「判準 5」

v0.2 §5 的四條判準驗的是需求 1–3 和 casebook，唯獨需求 4 沒有可驗證的判準（「整理出 casebook 案例」不等於 loop 真的跑得起來）。建議補：

> **判準 5**：開一個**全新的 agent session**，只靠 repo 自帶的文件（不靠 Eric 口頭補充脈絡），完成一次 R0/R1 等級的修改並通過驗證合併。做得到，才算「任何一次 agent session 都能安全接手」。

這條同時是對「未來開發 agent」這個角色（§4 表格第四列）的驗收。

### 缺口 3：風險熱點沒預判 → 先標出天生 R2/R3 的部位

照 `00_context/05` 的判準，這個系統有幾塊**天生就不是 R0/R1**，開發計劃排序時要預先標記 checkpoint，不要等 PR 出來才發現要走嚴格流程：


| 部位                    | 預判等級           | 理由（對照 05）                                                |
| --------------------- | -------------- | -------------------------------------------------------- |
| MCP server 的工具介面定義與權限 | R3             | Agent 工具清單／權限範圍變更；MCP server 本身是攻擊面（Gate B，§8.2 鐵律也這麼要求） |
| 寫入 API（Eric 編輯通道）與認證  | R3             | 授權／認證邏輯                                                  |
| DB schema 設計與後續變更     | R2 起跳；破壞性變更 R3 | Schema／合約變更                                              |
| 部署（AWS VM、IaC、CI/CD）  | R3             | v0.2 §3.4 已預告「照 R3 紀律走」，一致                               |
| 網站頁面呈現、樣式、文案          | R0/R1          | 單服務內部、可快速回滾——這是 loop 可以放心全速跑的區域                          |


順帶的好處：這張表本身就是「A×R 矩陣用在真實專案」的示範素材。

### 缺口 4：MCP 鐵律是紙上規則 → 轉成 Agent 可自跑的測試

§8.2 三條鐵律其實都可測，建議技術規格直接把它們定義成測試案例，進入秒級子集或 CI，讓 Agent 在 loop 內就能驗，而不是靠人在 review 時用眼睛守：

1. 「回答帶出處」→ 對一組固定查詢，斷言回傳內容必含來源欄位且指向系統內存在的條目
2. 「查不到就說查不到」→ 準備一組系統內不存在的查詢，斷言回傳「查無」而非模型生成的一般知識
3. 「唯讀開放、寫入管制」→ 斷言未授權來源呼叫寫入工具必被拒絕

這是整份計劃裡「把品質門檻編譯成 Agent 可執行驗證」最划算的一塊。

### 缺口 5：loop 沒有煞車 → 定停止與重試規則

寫進 AGENTS.md，屬於缺口 1 清單的一項，內容獨立列出：

1. **重試預算**：同一個測試失敗修 3 次還不過 → 停止，輸出「試了什麼／為何失敗／卡在哪」交還給人（3 這個數字待實測校正）
2. **明文禁止改弱測試來過關**（刪測試、放寬斷言、跳過案例都算）
3. **強制 checkpoint**：觸及缺口 3 表格中 R2/R3 部位 → 暫停等人確認（Gate E），不因前面順利而放行
4. **範圍偏移即停**：要動需求卡範圍外的東西（新依賴、別的模組、既有介面）→ 先停下來問

### 缺口 6：體驗性需求的驗證歸屬沒講 → 明定「自動驗什麼、人工驗什麼」

需求 1 的「乾淨、快、繁中閱讀舒服、手機能看」不能全交給 Agent 自評（「看起來對」正是 05 警告的 illusion of correctness）。建議技術規格明定分工：

- **可自動**（進 loop）：頁面載入時間門檻、RWD 斷點檢查、連結不失效、瀏覽器 console 無錯誤——必要時用 browser 驗證工具讓 Agent 自己看渲染結果
- **明定人工**（PM 業務驗證層）：「舒服」「一眼 get 到」這類主觀判斷，由 Eric 驗收；寫明白它是人工項，Agent 就不會拿截圖自我宣告通過

### 缺口 7：內容維護（需求 2）的 loop 沒有閉環確認

Eric 對 agent 說「加進 Hub」之後，agent 要能**自己確認生效**才算完成：寫入後立即用查詢工具讀回、比對內容一致、確認修改記錄已落，三者都成立才回報成功。這條應寫進 MCP 寫入工具的驗收標準（技術規格），否則場景 1 的「幾秒後網站上線」只是樂觀假設。

## 4. 檢討結論的落點（給 §9 兩份文件的分工）


| 檢討項                               | 落進哪份文件                     |
| --------------------------------- | -------------------------- |
| 缺口 4（鐵律測試化）、缺口 6（驗證分工）、缺口 7（寫入閉環） | **技術規格**                   |
| 缺口 1（佈置清單）、缺口 3（風險熱點表）、缺口 5（停止規則） | **開發計劃**（且清單是動工前置條件）       |
| 缺口 2（判準 5）                        | 建議下次修訂 v0.2 §5 時補入（本文件不代改） |


## 5. 待實測校正的預設值

1. 重試預算 3 次：太緊會頻繁打斷、太鬆會空轉
2. 「秒級測試子集」在網站＋MCP 的實際切法
3. 判準 5 的「全新 session 完成 R0/R1 修改」要不要限時（例如 30 分鐘內）

跑完第一輪開發後：修訂本檢討 → 過程寫成 casebook 案例（呼應 v0.2 判準 4）→ 可再抽成部門 repo 的 skill（例如 loop 佈置 checklist）。

## 與其他文件的連結

- 檢討對象 → `20260823_AgentHub_需求計劃_v0.2.md`
- 需求卡、三句驗收、三層驗收 → `../00_context/04-輕量開發流程.md`
- R0–R3、Gate A–E、Code Review L1–L3 → `../00_context/05-風險分層與五大品質門檻.md`
- Skill 的 Process／Verification、Prove-It Pattern → `../00_context/08-Skill與Harness工程.md`



## 補充 Agent skills

有，而且目前已經有幾套很接近你剛剛講的 **Engineering Loop「流程設計」**。它們不是單純教 Agent 寫 Code，而是直接規定 Agent **每一階段該做什麼、怎麼驗證、什麼條件才能往下走**。

我會先看這 4 套：


| Skills                         | 核心 Loop                                             | 我怎麼看                           |
| ------------------------------ | --------------------------------------------------- | ------------------------------ |
| **Addy Osmani / agent-skills** | `Spec → Plan → Build → Test → Review → Ship`        | **最完整的 SDLC Engineering Loop** |
| **Superpowers**                | `Brainstorm → Plan → Implement → Review → Verify`   | **最強調 Agent 自主閉環**             |
| **Matt Pocock / skills**       | TDD、Debug、Code Review 等獨立 loops                     | 比較像「高手的局部工程招式」                 |
| **sdlc-skills**                | `Plan → Design → Test → Implement → Review → Learn` | **最像刻意設計 Loop 的框架**            |


### ① Addy Osmani：目前最接近你問的東西

你前幾天看的 `addyosmani/agent-skills`，其實就是在做這件事。

它把整個 Engineering Loop 拆成：

```text
/spec
  ↓
/plan
  ↓
/build
  ↓
/test
  ↓
/review
  ↓
/ship
```

而且 `/build auto` 可以在 Plan 核准後，自動逐 task 執行，但**每個 task 仍然要求 test / verification，失敗就停下來處理**。([GitHub](https://github.com/addyosmani/agent-skills/blob/main/README.md?utm_source=chatgpt.com "agent-skills/README.md at main · addyosmani/agent-skills · GitHub"))

甚至 `planning-and-task-breakdown` Skill 裡會要求每個 task 都有：

```text
Task
├─ Acceptance Criteria
├─ Verification
│   ├─ Tests
│   ├─ Build
│   └─ Manual Check
├─ Dependencies
└─ Files touched
```

這已經不是 coding prompt，而是在**設計工程流程**。([GitHub](https://github.com/addyosmani/agent-skills/blob/main/skills/planning-and-task-breakdown/SKILL.md?utm_source=chatgpt.com "agent-skills/skills/planning-and-task-breakdown/SKILL.md at main · addyosmani/agent-skills · GitHub"))

---

### ② Superpowers：更像「自主 Agent Engineering Loop」

它特別強的是：

```text
Plan
 ↓
Task 1
 ↓
Implementer Agent
 ↓
Spec Review
 ↓
Code Quality Review
 ↓
Fix
 ↺
Task 2
 ↓
...
 ↓
Whole-branch Review
```

`subagent-driven-development` 明確要求：

> 每個 task 用新的 implementer agent，完成後做 spec compliance + code quality review，最後再做整體 branch review。([GitHub](https://github.com/obra/superpowers/blob/main/skills/subagent-driven-development/SKILL.md?plain=1&utm_source=chatgpt.com "superpowers/skills/subagent-driven-development/SKILL.md at main · obra/superpowers · GitHub"))

所以如果你想研究：

> **「Agent 自己跑工程流程，人只管 checkpoint」**

Superpowers 特別值得研究。

---

### ③ Matt Pocock：比較像 Engineering Loop 的「零件庫」

Matt 那套不是統一的大流程，而是很多品質很高的小 loop。

例如 Debug：

```text
Build feedback loop
 ↓
Reproduce
 ↓
Minimise
 ↓
Hypothesise
 ↓
Instrument
 ↓
Fix
 ↓
Regression Test
```

甚至他直接強調：

> debugging 最重要的不是猜原因，而是先建立一個會 Red / Green 的 feedback loop。([GitHub](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md?utm_source=chatgpt.com "skills/skills/engineering/diagnosing-bugs/SKILL.md at main · mattpocock/skills · GitHub"))

另外還有：

`TDD → Red → Green → Refactor`

`Code Review → Standards Review + Spec Review`

`Domain Modeling → Challenge → Edge Cases → Update Context/ADR`

所以它非常適合拿來當你自己 Engineering Loop 裡面的 **sub-loop**。([GitHub](https://github.com/mattpocock/skills?utm_source=chatgpt.com "GitHub - mattpocock/skills: Skills for Real Engineers. Straight from my .agents directory. · GitHub"))

---

### ④ 我反而建議你特別看 `sdlc-skills`

它的設計非常直接：

```text
Plan
 ↓
Design
 ↓
Test
 ↓
Implement
 ↓
Review
 ↓
Learn
 └──────────→ 下一輪
```

最後多了一個很有意思的 **Learn**：

> 把失敗記錄聚類 → 找 recurring failure → encode 成新的 rule → 改善下一輪。([GitHub](https://github.com/Jeff-Kazzee/sdlc-skills?utm_source=chatgpt.com "GitHub - Jeff-Kazzee/sdlc-skills: Installable agent skills for disciplined AI-assisted software work — review gates, planning, debugging, and process guardrails. · GitHub"))

這其實已經比一般：

> Code → Test → Fix

更進一步變成：

```text
Engineering Loop

Plan
 ↓
Act
 ↓
Verify
 ↓
Fix
 ↺

      ＋

Meta Loop

觀察 Agent 常犯什麼錯
 ↓
修改 Skill / Rule / Context
 ↓
下一輪 Agent 表現更好
 ↺
```

### 所以我會這樣定位

如果你現在是想研究 **「怎麼設計 Agent Coding 的 Engineering Loop」**，我會用：

> **Addy 當骨架 + Superpowers 看自主執行 + Matt Pocock 看每個 feedback loop 怎麼做深。**

而最值得你注意的一個觀念是：

**Skill 本身就是 Engineering Loop 的流程規格。**

好的 [`SKILL.md`](http://SKILL.md) 不只是「告訴 Agent 知識」，而應該寫：

```text
When to use
↓
Entry criteria
↓
Steps
↓
Tool / Action
↓
Evidence
↓
Failure handling
↓
Retry / escalation
↓
Exit criteria
```

Addy 自己的 Skill 設計文件甚至直接寫出原則：

> **Process over knowledge、Evidence over assumption。** ([GitHub](https://github.com/addyosmani/agent-skills/blob/main/docs/skill-anatomy.md?utm_source=chatgpt.com "agent-skills/docs/skill-anatomy.md at main · addyosmani/agent-skills · GitHub"))

這其實就是你問的「流程設計 Skill」最核心的答案：**不是再找一個更好的 Prompt，而是把工程 SOP + feedback + gate + exit criteria 編碼成 Skill。**

