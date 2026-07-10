我把時間範圍限定在 **2026 年 1 月 10 日到 2026 年 7 月 10 日**，優先保留：

* 約 5～100 人的公司、工程團隊或明確 Pilot Cohort
* 第一方團隊文章或直接訪談
* 不只說「用了什麼工具」，而是交代推動方式、工作流程、基礎設施、治理、成效或失敗
* 能對你規劃的 60 人部門提供具體借鏡

最後保留 **10 個案例**。其中前 6 個最值得完整閱讀。

# 最值得優先閱讀的 6 個案例

| 優先 | 團隊                     |             規模 | 推動模式                                                | 文章完整度 |
| -- | ---------------------- | -------------: | --------------------------------------------------- | ----: |
| 1  | Speak                  |        60 位工程師 | Hack Week、能力轉型、共用 Skills、組織與招募調整                    |   5/5 |
| 2  | Kapwing                | 25 人／約 12 位工程師 | 五個月全員推廣，做到所有職能都提交程式碼                                |   5/5 |
| 3  | MetalBear              |   34 人／15 位工程師 | 技術複雜系統下的保守、務實導入                                     |   5/5 |
| 4  | Warmly                 |         8 位工程師 | Skills、Subagents、MCP、Memory、內部 Plugin Marketplace   |   5/5 |
| 5  | Cisco Pilot            |        70 位使用者 | 多 Agent 跨團隊工作流與量化 Pilot                             | 4.5/5 |
| 6  | GitHub Applied Science |         5 人專案群 | 以 Coding Agent 建立可重用 Agents、Skills 與 Eval Workflows | 4.5/5 |

---

# 1. Speak：最接近你的 60 人規模

## 文章

**Embracing the agentic engineering era at Speak**，2026 年 2 月 11 日發布。Speak 約有 60 位工程師，整家公司約 150 人。([Speak][1])

## 他們怎麼推

Speak 沒有把導入方式設計成「發工具帳號、上一次課」，而是把 **2026 年 Q1 明確設定為工作方式轉型期**，並告訴團隊：短期變慢、實驗失敗都可以接受。團隊認為，真正熟悉 Agentic Engineering 需要投入數十小時，不是看一份教學就能完成。([Speak][1])

一月份舉辦 Hack Week，只有一條規則：

> 盡量少手寫程式碼。

Hack Week 後，他們做了詳細問卷，盤點：

* 大家使用哪些工具
* 哪些工作已經能交給 Agent
* 哪些地方仍然卡住
* 缺少哪些基礎設施
* 日常工作到底改變了多少

問卷結果讓他們發現，Coding Agent 要在背景持續工作，必須有完整、隔離的開發環境，因此開始投資 Sandboxed Development Environment。([Speak][1])

同時他們正在建立：

* Shared Agent Skills Repository
* Repository-level Context
* 更自給自足的產品 Pods
* Context Engineering 能力
* 跨 PM、設計、工程的模糊邊界([Speak][1])

Speak 也把 Agent 能力納入招募。他們取消傳統 coding screen，改成讓候選人使用 Claude Code 或 Codex，觀察候選人如何：

* Planning
* Debugging
* 解釋設計選擇
* 分析 trade-off
* 保持品質
* 建立讓 Agent 更容易工作的環境([Business Insider][2])

## 對你的價值

這是目前最接近你 60 人部門的案例，尤其支持你正在規劃的：

```text
強制學習週
→ 使用方式盤點
→ Shared Skills Repo
→ Agent Level
→ 角色邊界重組
→ 招募與評鑑調整
```

但 Speak 的文章比較偏方向與文化，沒有公開完整的 Agent Level Matrix、Risk Authorization 或評鑑量表；這些仍然是你的 repo 可以補足的部分。

---

# 2. Kapwing：最完整的「全員採用」案例

## 文章

**How We Achieved 100% Adoption of AI Coding Agents**，2026 年 4 月 7 日發布。Kapwing 約 25 人，其中約 12 位工程師；他們的目標是讓每位員工在 2026 年第一季都實際提交程式碼。([Kapwing][3])

## 他們怎麼推

Kapwing 的導入歷時五個月，分三階段。

### 第一階段：Owner、工具和早期使用者

先指定一位 Engineering Lead 專門負責：

* 研究工具
* 做選型
* 設計 Roadmap
* 處理權限與基礎設施
* 推動採用

最後選擇 Codex，建立 ChatGPT Enterprise、Codex＋Slack 整合、公開 Slack 頻道及 Notion 文件。更重要的是，先讓 Product Lead 等管理者公開展示自己的成果，透過真實案例建立信任。([Kapwing][3])

### 第二階段：解決環境摩擦

他們發現非工程人員最大的障礙，不是 Prompt，而是：

* GitHub 權限
* 開發環境
* 如何看到修改結果
* 如何部署到測試環境

因此替非工程人員準備 Dev Machines，並設計一行 GitHub Comment 就能部署到測試環境的機制。([Kapwing][3])

### 第三階段：訓練與任務設計

訓練包含：

* Coding Agent 能做與不能做什麼
* 完整 PR Workflow
* 如何指定精確元素及檔案
* 如何測試
* 如何取得工程師 Review
* 常見故障排除

每個人的第一個任務不是自己找，而是由工程團隊事先挑選一個低複雜度、範圍清楚的真實 Backlog Item。([Kapwing][3])

## 結果

第一季約產生 108 個標記為 Codex 的 PR。QA 團隊因為擅長重現和精確描述問題，成為最活躍的程式碼貢獻者之一；持續自動處理低優先 Bug 後，他們取消了原本每季三天的 Bug Bash。工程師則花更多時間在架構、任務切分和 Review。這些成效都是 Kapwing 自行報告，未經獨立研究驗證。([Kapwing][3])

他們仍然在處理：

* Codex 與 Claude Code 並存造成的工具不一致
* Token 成本難以歸屬
* Design System 如何進入 Agent Context
* MCP 與多 Agent 培訓([Kapwing][3])

## 對你的價值

這個案例證明，推動障礙通常不是「大家不會下 Prompt」，而是：

```text
沒有適當任務
沒有開發環境
沒有測試入口
沒有權限分級
沒有 Review 支援
沒有第一個成功案例
```

對你的 repo，最值得借的是：

* 第一個任務由系統推薦，而非使用者自行挑選
* 不同職能使用不同任務模板
* Agent Roadmap 必須連接真實 Backlog
* QA 很可能是早期高潛力族群
* A0／A1 的環境應比 A2／A3 更簡單、更受控

---

# 3. MetalBear：複雜技術產品不適合盲目追求自主化

## 文章

**How Our Engineering Team Uses AI**，2026 年 1 月 15 日發布。MetalBear 有 34 人，其中 15 人屬於工程團隊，產品是使用 Rust 建立、涉及 Kubernetes、程序注入、網路攔截與多層元件的低階開發工具。([MetalBear][4])

## 他們怎麼推

MetalBear 沒有強制統一工具，而是：

* 鼓勵每位工程師實驗
* 允許自行選 Claude Code、ChatGPT、Gemini 等工具
* 設置公開 Slack 頻道分享用法
* 讓成功與失敗案例自然擴散([MetalBear][4])

他們認為最穩定的使用方式是：

* 先建立不熟悉程式區域的初步心智模型
* 在實作前探索方案和 trade-off
* 產生 Debugging Scripts
* 處理機械性或重複工作

但工程師仍會回頭閱讀真實程式碼，而不是把 Agent 的解釋當成架構真相。([MetalBear][4])

## 他們刻意不做什麼

因為系統架構複雜，Agent 很難掌握完整脈絡。團隊表示，成功讓 Agent 幾乎不需人工介入地修復複雜 Bug 的案例很少；如果讓 Agent 自主進行大範圍修改，修復它產生的程式碼有時比自己重寫更費工。([MetalBear][4])

他們使用 `CLAUDE.md` 或 `AGENTS.md` 補充：

* Architecture
* Key paths
* Build commands
* Integration tests
* Component relationships

但即使有大量 Context，團隊仍不認為 Agent 可以完全信任。([MetalBear][4])

## 對你的價值

這是很重要的反向案例。

它說明 Agent Level 不能只依「這個人多會使用 Claude Code」判定，還要考慮：

```text
Codebase complexity
Domain uniqueness
Test coverage
Context completeness
Failure detectability
```

這尤其適用於你的韌體、車用系統、資料平台和複雜整合專案。

MetalBear 的實務立場接近：

* A1／A2：可以大量使用
* A3：只適合少數邊界清楚的 Flow
* 高複雜架構：不要因工具能力提高就直接開放高自主權

---

# 4. Warmly：8 人團隊建立自己的 Agent Operating System

## 文章

**Claude Code Best Practices: How We 3x’d Engineering Velocity Without Hiring**，2026 年 4 月 15 日發布。Warmly 表示工程團隊維持 8 人，但透過平行執行 Agent，產出接近原先三倍；這是公司自行估計的說法，文章沒有提供嚴謹的對照實驗。([warmly.ai][5])

## 他們怎麼推

Warmly 發現每位工程師各自維護 Prompt、`CLAUDE.md` 和 Debug 方法，知識都散落在個人環境與 Slack 私訊中，因此建立 **Warmly Intelligence**：

```text
Internal Plugin Marketplace
+
Headless Claude Runner
```

讓所有工程師使用同一套可版本化的公司資產。([warmly.ai][5])

內容包括：

* Reviewed `CLAUDE.md`
* 20 多個專門 Subagents
* Agent Skills
* 18 個 MCP Servers
* Memory Loop
* Git Worktrees
* Background Queue
* Token Rotation
* Structured Output
* Read／Write 權限區分([warmly.ai][5])

他們把內部 Tribal Knowledge 變成可執行 Skill，讓新工程師第一天就能按照資深人員的 Debugging 順序操作。

其 Subagent 設計原則包括：

* 一個 Subagent 只處理一類任務
* Prompt 應視為規格，而不是一句說明
* 輸出使用 Typed Schema，不只回傳自然語言
* Parent Agent 只接收結構化 Evidence
* Read Tool 與 Write Tool 分開管理([warmly.ai][5])

## 對你的價值

Warmly 幾乎就是你共用資產層的縮小版：

```text
Agent Skills
Agents
MCP
Flows
Memory
Internal Marketplace
Onboarding
```

但它有一個條件：8 人團隊的溝通、信任與技術一致性遠高於 60 人部門。

你不能直接複製「18 個 MCP 全部接給每個 Agent」的做法，而應增加：

* Role-based permission
* Risk classification
* Asset approval
* Domain owner
* Eval
* Version support
* Retirement

---

# 5. Cisco：70 人 Pilot，從 Coding 提升到跨團隊流程

## 文章

**Agentic Engineering: How Swarms of AI Agents Are Redefining Software Engineering**，2026 年 4 月 17 日發布，由 Cisco 的工程主管撰寫。這不是 70 人公司，而是大型企業內一個有 **70 位實際使用者**的 Pilot Cohort。([LangChain][6])

## 他們怎麼推

Cisco 的重點不是讓 Coding Agent 單獨寫更多程式，而是建立一個跨團隊控制面：

```text
Leader Agent
→ Worker Agents
→ Coding Agents
→ MCP Tools
→ Shared Memory
→ Checkpointing
→ Validation
→ Notification
```

完整流程是：

1. 理解工程意圖
2. 從 MCP 取得跨系統 Context
3. 產生結構化 Plan
4. 通知工程師
5. 逐步執行
6. 以 Checkpoint 記錄狀態
7. 驗證實際執行是否符合 Plan
8. 把結果寫入長期記憶並通知團隊([LangChain][6])

他們也強調：

* Deterministic Authorized Actions
* Stateful Execution
* Traceability
* Long-term Memory
* IDE-independent Adapter
* Human-in-the-loop

## Pilot 結果

一個月內有 70 位使用者、512 次 Debug Sessions。20 多種跨團隊 Debugging Workflow 的歷史比較顯示，Time-to-root-cause 下降約 93%，並由獨立 QE 團隊做品質評估；15 多種 Development Workflow 的執行時間下降超過 65%。這些數字由案例作者自行整理，baseline 使用歷史資料，並非隨機對照實驗。([LangChain][6])

值得注意的是，最大改善不只發生在 code generation，而是：

* 跨團隊 Context 取得
* 測試規劃
* PR 後功能測試
* 重複工作重用

速度提升後，人工 PR Review 反而成為新瓶頸。([LangChain][6])

## 對你的價值

這支持你把共用 repo 分成：

```text
Knowledge／Context
Workflow Registry
Tool Gateway
State／Memory
Verification
Audit
```

也提醒你：A3／A4 的重點不應只是「能平行開幾個 Claude Code」，而是能否設計跨角色、跨系統、可追蹤的完整 Flow。

---

# 6. GitHub Copilot Applied Science：5 人快速建立 Agent 資產庫

## 文章

**Agent-driven development in Copilot Applied Science**，2026 年 3 月 31 日發布。案例中有五位成員加入專案，在不到三天內共同建立 11 個 Agents、4 個 Skills 及 Eval-Agent Workflows。([The GitHub Blog][7])

## 他們怎麼推

原始需求是把 AI Researcher 經常進行的 Benchmark Trajectory 分析自動化，再讓其他研究者可以建立自己的分析 Agents。

技術組合包括：

* Copilot CLI
* Claude Opus
* Copilot SDK
* MCP Servers
* Custom Tools
* Skills
* Eval-Agent Workflows([The GitHub Blog][7])

他們歸納的工程原則是：

* 先 Planning，再進入 Agent Execution
* 頻繁 Refactor
* 頻繁更新 Documentation
* 頻繁清理 Codebase
* Agent 出問題時，先檢查流程和環境，而不只是怪模型
* 複雜問題仍需要資深工程師的引導([The GitHub Blog][7])

## 對你的價值

這是一個適合 5～8 人 Chapter／Guild 起步的模式：

```text
先選一個高度重複的專業工作
→ 一人建立第一個 Agent
→ 其他成員加入
→ 建立 Skills 與 Eval
→ 形成專業資產庫
```

它比一開始就要求全 60 人建立 Skills，更容易成功。

---

# 其他 4 個具不同價值的案例

## 7. Near Space Labs：非工程角色自助建工具，工程團隊守住正式上線邊界

Near Space Labs 的 COO 描述公司從 12 人擴張到 80 人的過程。他原本不會寫程式，後來使用 Claude Code 建立 OKR Tracker 等內部工具；Product 和 Finance Lead 也開始自行建立 Roadmap 視覺化和財務模擬工具。([Near Space Labs][8])

但當本機工具要變成全公司使用的正式服務時，仍由工程團隊處理：

* Authentication
* Hosting
* 公司 IP 保護
* 正式基礎設施([Near Space Labs][8])

這是一個很好的權限邊界：

```text
非工程角色：
可以自主 Prototype 和解決局部摩擦

工程團隊：
負責正式共用、權限、安全和可靠性
```

對你的 A0～A2 設計很有參考價值。

---

## 8. Supademo：18 人公司，把 Agent 變成跨 Product／Growth 的交付方式

Supademo 目前 LinkedIn 顯示 18 位員工，2026 年 4 月 6 日發布的文章描述全團隊使用 Claude Code、Codex 和 Cursor，讓非純工程角色也能直接推動網站、Onboarding 和 Growth Experiments。([LinkedIn][9])

他們分享的三個案例是：

* 重建整個網站
* 將 Onboarding Segmentation 上線
* 一週內建立完整 Learning Academy

公司表示，新 Onboarding 使 Activation 提升超過 30%；此數字是 Supademo 自行報告，文章比較偏 Founder Case Study，缺少工程品質和治理細節。([Supademo][10])

這個案例適合研究 Prototyper／Grower，不適合直接當高風險工程治理範本。

---

## 9. Turbo AI：10 人團隊的成本失控與能力退化警告

Turbo AI 約 10 人，平均每月花約 2 萬美元在軟體開發 AI 工具上，曾因 Claude Code Fast Mode 等使用方式，使單月費用接近 3 萬美元。團隊目前沒有嚴格的 Token Approval Process，而是認為只要能帶來足夠產出就值得。([Business Insider][11])

創辦人也坦白提到：

* 工作從寫 code 轉成 planning 和 reviewing
* 擔心 coding 能力退化
* 擔心工程師對 codebase 越來越不熟悉
* 但不用 Agent 的速度差距又難以接受([Business Insider][11])

最後採用的成本控制是：

* 一般任務使用 Standard Mode
* 簡單任務使用較輕模型
* 不要把整個 codebase 都放入 Context
* Fast Mode 只在低延遲真正有價值時使用([Business Insider][11])

這篇文章支持你的 repo 增加：

```text
Cost Awareness
Context Budget
Learning Debt
Codebase Understanding
Transfer Task
```

否則 A2／A3 很可能只代表「花很多 Token」，而不是能力成熟。

---

## 10. Uber Agentic Pods：30 位 AI 工程師嵌入業務現場

Uber 是大型企業，不符合 100 人公司的範圍，但這個具體推動單位是 **30 位 AI 熟練工程師**，因此值得當作「部門內 Agent Champion／FDE」案例。

Uber 把這 30 人嵌入 Finance、Legal、HR 等單位，每次約兩週，直接觀察實際工作，再建立相應 Agent；兩個月內進行了 16 個 Agentic Pods。([Business Insider][12])

核心原則是：

> 不只閱讀流程圖或文件，而要理解工作實際怎麼完成。

案例中，財務 pacing report 從兩天縮短到約 10 分鐘；跨 150 個城市的資本配置工作從約 15 小時縮短到 30 分鐘。這些成效來自 Uber CTO 的公開說法，尚無獨立技術評估。([Business Insider][12])

這與你原本的 FDE 思路非常相近：

```text
Champion 嵌入實際團隊
→ 觀察工作
→ 找到 Context 與例外
→ 建立 Agent Flow
→ 驗證
→ 回灌部門資產庫
```

---

# 這 10 個案例呈現的四種推動路線

## 路線一：全面普及

代表：

* Kapwing
* Supademo
* Near Space Labs

目標是讓 PM、QA、設計、營運等角色也具備建造能力。

適合：

* Web
* Internal Tools
* Content
* Growth Experiments
* 易於視覺驗證的功能

風險是 Prototype 快速進入 Production，導致安全、維護性及架構問題。

---

## 路線二：工程能力轉型

代表：

* Speak
* MetalBear

重點不是所有人都提交程式碼，而是讓工程師從：

```text
Code Author
→ Task Decomposer
→ Context Engineer
→ Verifier
→ Loop Designer
```

適合你的韌體、數據平台、Backend 和架構團隊。

---

## 路線三：建立部門共用 Agent Platform

代表：

* Warmly
* Cisco
* GitHub Applied Science

共同做法是把個人技巧變成：

* Skills
* Agents
* MCP
* Structured Output
* Shared Memory
* Workflow
* Eval
* Plugin Marketplace

這正是你計畫中的共用資產層。

---

## 路線四：嵌入式 Agent Transformation Team

代表：

* Uber Agentic Pods

不是要求每個團隊自行探索，而是派熟練工程師進場：

```text
觀察
→ 建模
→ 實作
→ 驗證
→ 產品化
→ 回灌共用平台
```

對 60 人部門，可能比全員同時自由探索更有效。

---

# 所有成功案例重複出現的 8 個做法

## 1. 指定明確 Owner

Kapwing 有專門 Engineering Lead；Warmly 建立內部平台；Speak 由工程團隊作為先鋒。

沒有 Owner，最後通常只會變成每個人各自使用工具。

## 2. 使用 forcing function

常見方式：

* Hack Week
* 全員完成一個 PR
* 指定第一個任務
* Bootcamp
* 兩週 Embedded Pod

不是只叫大家「有空可以試」。

## 3. 第一個任務必須被挑選過

應具有：

* 範圍小
* 結果可見
* 可以 rollback
* 有現成 reviewer
* 不涉及深層架構
* 能在數小時至一兩天完成

## 4. Context 要變成共用資產

反覆出現：

* `CLAUDE.md`
* `AGENTS.md`
* Shared Skills
* Repository Instructions
* Architecture Documentation
* MCP
* Workflow Memory

## 5. 環境比 Prompt 更重要

真正阻礙採用的是：

* 權限
* Dev Environment
* Test Environment
* Sandbox
* Credential
* Build time
* Preview
* Deployment

## 6. Agent 產出增加後，Review 會變成瓶頸

Kapwing 的工程師開始花更多時間 Review；Cisco Pilot 直接觀察到 PR Review 成為新的速度限制。([Kapwing][3])

## 7. 高度自主必須建立 verifier

成熟案例不是只給更多權限，而是加入：

* Test
* Structured evidence
* Independent QE
* Checkpoint
* Stop condition
* Read／write separation
* Human approval

## 8. 必須同時防止 Knowledge Debt

MetalBear 擔心 Agent 在缺乏系統理解下產生看似合理的修改；Turbo AI 則直接提到 coding skill 和 codebase knowledge 退化。([MetalBear][4])

---

# 對你 60 人部門最合理的組合

我不建議完整複製其中任何一家公司，而是組合四個案例：

```text
Speak
→ Hack Week、能力轉型、共用 Skills、角色調整

Kapwing
→ 分階段普及、預選任務、環境與訓練

Warmly
→ Skills／Agents／MCP／Flows 的共用資產庫

MetalBear
→ 複雜系統的自主化邊界與工程判斷
```

再用：

```text
Cisco
→ A3／A4 多 Agent Flow 與量化方式

Uber
→ Champion 嵌入各團隊的 FDE 推動模式

Turbo AI
→ 成本與 Knowledge Debt 警示
```

---

# 建議你的第一年導入模型

## 第一季：Speak＋Kapwing

* 12～18 位跨職能 Champions
* Agent Hack Week
* 每人一個預選任務
* 導入前後問卷
* 建立 A0／A1
* 建立基礎開發與 Sandbox 環境

## 第二季：Warmly＋MetalBear

* 建立 Shared Skills Repo
* 建立角色專屬 Playbooks
* 建立 `AGENTS.md`／Repository Context
* 資產 Owner 與 Review
* 定義 A2
* 韌體及高複雜系統採更保守邊界

## 第三季：GitHub＋Uber

* 每個 Chapter 選一個重複工作
* 派 Champion 嵌入團隊
* 把成功作法做成 Agent／Skill／Flow
* 建立 Eval 與 Evidence
* 培養 A3

## 第四季：Cisco

* 建立跨團隊 Workflow
* 引入 State、Memory、Tool Gateway
* 設定 Stop／Escalation
* 衡量端到端 Flow，而非只看程式碼產量
* 僅對 R0／R1 開放 Bounded Autonomy

---

# 一個目前的研究缺口

在近半年、5～100 人、第一方且說明完整的案例中，我沒有找到足夠完整的：

* Automotive Software
* AUTOSAR
* Safety Firmware
* ISO 26262
* ASPICE

相關 Agent Coding 團隊導入文章。

目前最接近的複雜工程案例是 MetalBear，其經驗反而顯示：低階系統、特殊架構和難以完整驗證的 codebase，應比一般 SaaS 更保守地提高 Agent 自主程度。([MetalBear][4])

這意味著你部門在 Firmware／Automotive Roadmap 上，可能不能直接依賴公開案例，而需要自己建立 Pilot、Evidence 和可公開的內部 Casebook。

* [Business Insider](https://www.businessinsider.com/ai-startup-cto-explains-shift-in-engineering-hiring-2026-4?utm_source=chatgpt.com)
* [Business Insider](https://www.businessinsider.com/uber-cto-bets-on-agentic-pods-make-ai-more-efficient-2026-7?utm_source=chatgpt.com)

這類團隊案例正在快速增加，我可以每月搜尋一次新的 5～100 人導入案例，並只在找到具體流程或量化結果時整理給你。

[1]: https://www.speak.com/blog/agentic-engineering "Embracing the agentic engineering era at Speak"
[2]: https://www.businessinsider.com/ai-startup-cto-explains-shift-in-engineering-hiring-2026-4 "The CTO of an AI Startup Explains the Shift in Engineering Hiring - Business Insider"
[3]: https://www.kapwing.com/blog/how-we-achieved-100-adoption-of-ai-coding-agents/ "How We Achieved 100% Adoption of AI Coding Agents"
[4]: https://metalbear.com/blog/engineering-ai-use/ "How Our Engineering Team Uses AI | mirrord by MetalBear"
[5]: https://www.warmly.ai/p/blog/claude-code-best-practices "Claude Code Best Practices: How We 3x'd Engineering Velocity Without Hiring"
[6]: https://www.langchain.com/blog/agentic-engineering-redefining-software-engineering "Agentic Engineering: How Swarms of AI Agents Are Redefining Software Engineering"
[7]: https://github.blog/ai-and-ml/github-copilot/agent-driven-development-in-copilot-applied-science/ "Agent-driven development in Copilot Applied Science - The GitHub Blog"
[8]: https://www.nearspacelabs.com/blog/making-room-how-claude-code-changed-cf88c "Near Space Labs | Making Room: How Claude Code Changed the Way I Operate"
[9]: https://www.linkedin.com/company/supademo-hq "Supademo | LinkedIn"
[10]: https://supademo.com/blog/how-i-use-claude-code-to-scale-my-saas "How I Use Claude Code to Scale My $3M/yr SaaS : 3 Tactics | Supademo Blog"
[11]: https://www.businessinsider.com/startup-cofounder-accidentally-spent-30-000-ai-tokens-worth-it-2026-7 "Startup Cofounder Accidentally Spent $30,000 on AI Tokens, Worth It - Business Insider"
[12]: https://www.businessinsider.com/uber-cto-bets-on-agentic-pods-make-ai-more-efficient-2026-7 "Uber's CTO Bets on 'Agentic Pods' to Make AI More Efficient - Business Insider"
