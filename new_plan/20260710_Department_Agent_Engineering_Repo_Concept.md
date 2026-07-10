# Department Agent Engineering Repository — 構想與方向

> **文件狀態**：Concept Draft v0.1  
> **適用範圍**：約 60 人之產品／軟體／數據／韌體跨職能部門  
> **建議 Repository 名稱**：`department-agent-engineering`  
> **更新日期**：2026-07-10

---

## 1. 一句話定位

建立一個由全體部門共同維護的 **Agent Engineering Operating System**：

> 讓每位成員能看懂自己目前在 Agent-enabled Delivery 的位置、知道下一步如何進步、取得適合其角色與風險範圍的做法與資源；同時集中管理部門共用的 Agent Skills、Tools、Agents、Flows、Policies、Evals 與實務案例。

這個 Repository 不是單純的「AI Coding 教材」、「Prompt 清單」或「工具大全」，而是把下列內容連成同一套可持續演進的制度與資產：

```text
人的能力
× 專業角色
× 產品貢獻模式
× 任務風險
× Agent 自動化程度
× 可執行共用資產
× 學習與評鑑證據
```

---

## 2. 為什麼需要部門級 Repository

Agent Coding 已讓產生程式碼、原型、測試、文件與分析的成本快速下降，但部門真正面臨的問題不只是「要選哪一套工具」，而是：

1. 不同成員的專業背景、工程成熟度與 Agent 使用能力差距很大。
2. PM、設計、QA、架構、軟體、數據及韌體，不能使用同一份單一路線圖。
3. 能產生結果，不等於能判斷結果、驗證結果或對結果負責。
4. 個人能力越高，理論上可以設計更高程度的 Agent 自動化；但自動化上限仍必須受到任務風險、驗證能力與權限控制約束。
5. 各人自行收藏 Prompt、Skill、MCP、Workflow，容易造成重複、版本過期、品質不一與安全風險。
6. 部門需要一套共同語言，才能討論能力、授權、學習、治理、投資與成效。

因此，本 Repository 要同時解決三個層次的問題：

| 層次 | 核心問題 |
|---|---|
| 個人 | 我在哪裡？還缺什麼？下一步怎麼練？ |
| 團隊 | 哪些能力不足？誰能負責哪些自動化？ |
| 部門 | 哪些 Agent 資產可以共用？如何驗證、批准、版本化與淘汰？ |

---

## 3. 核心設計原則

### 3.1 能力越高，可以設計更高程度的自動化，但不是取得無限制權限

Agent 自動化程度不應只由職稱或年資決定，而應由四者交集決定：

```text
允許的 Agent 自動化程度
=
個人的 Agent 能力
∩ 專業領域能力
∩ 工作流與驗證成熟度
∩ 任務風險上限
```

一位高階 Backend Engineer 不會因為整體評為高等級，就自動取得 Safety Firmware、Production IAM 或不可逆資料刪除的自主授權。

### 3.2 專業角色不能被 Agent Level 取代

本 Repository 必須同時保留：

- Professional Role：長期專業歸屬
- Professional Level：原本的專業成熟度
- Agent Delivery Level：使用、委派及設計 Agent 工作流的能力
- Domain Scope：在哪些產品或技術域具備責任能力
- Risk Authorization：可以處理到哪一級風險

### 3.3 Roadmap 必須連到實作、證據與授權

每一個學習節點不能只有文章或影片，理想上應連到：

```text
學習內容
→ 實作任務
→ Assessment
→ Evidence
→ Level 能力
→ 可解鎖的 Agent 行為
```

### 3.4 資料與呈現解耦

Repository 內的 Markdown、YAML／JSON、Skills、Flows 與評鑑結果是 Single Source of Truth；互動網站、IDE、Coding Agent、Dashboard 都只是不同的消費端。

### 3.5 共用資產需要生命週期，不是上傳後永久有效

所有 Agent Skills、Agents、Tools、Flows 都應經過：

```text
Proposed
→ Review
→ Pilot
→ Approved
→ Monitored
→ Deprecated
→ Retired
```

### 3.6 Agent 協助評估，但不能單獨決定人員等級

Coding Agent 可以：

- 引導自評
- 讀取證據
- 找出能力缺口
- 產生練習建議
- 推薦資源與共用資產

但正式 Level 認定仍應包含實作證據、Domain Owner／Chapter Reviewer 與必要的人類校準。

---

## 4. 適用對象與角色模型

### 4.1 Professional Home：專業角色

第一版建議涵蓋：

- Product Manager／Product Owner
- Software Architect／Tech Lead
- UX／UI／Product Designer
- QA／QE／Test Architect
- Frontend Engineer
- Backend Engineer
- Mobile／App Engineer
- Algorithm／ML Engineer
- Data Engineer／Data Platform Engineer
- Firmware／Embedded Engineer
- DevOps／Platform／SRE
- Security／Compliance
- Engineering Manager／Delivery Lead

這一軸回答：

> 這個人主要在哪一個專業領域累積深度，並對什麼類型的結果負責？

### 4.2 Product Archetype：當期產品貢獻模式

參考 Boris Cherny 的分類，但不把它們改造成永久職稱：

| Archetype | 核心價值 |
|---|---|
| Prototyper｜原型探索者 | 快速降低未知、驗證可能性 |
| Builder｜建造者 | 把驗證過的方向做成可正式交付的產品或基礎設施 |
| Sweeper｜精煉者／系統整頓者 | 移除複雜度、統一介面、改善效能與可維護性 |
| Grower｜成長者 | 依使用者與市場回饋持續迭代產品 |
| Maintainer｜維護者 | 讓成熟系統在規模化後仍可靠、安全、快速 |

每人可設定：

- Primary Archetype
- Secondary Archetype
- 所屬產品階段
- 每六個月或專案階段變化時重新檢視

這一軸回答：

> 這個人目前主要透過什麼方式，為產品創造價值？

### 4.3 Agent Delivery Level：Agent-enabled Delivery 能力

建議第一版採五級：

| 等級 | 名稱 | 核心定義 | 一般自動化範圍 |
|---|---|---|---|
| A0 | Safe User | 能安全查詢、解釋及使用草稿 | Read-only、學習、摘要 |
| A1 | Assisted Contributor | 能與 Agent 一起完成小型明確任務，並理解修改 | 小批次修改、必須逐步確認 |
| A2 | Independent Delegator | 能定義、委派、審查及驗證一般 Production 任務 | Task-to-PR，人工 Merge／Release |
| A3 | Workflow／Loop Designer | 能設計可重複、可驗證、有邊界的 Agent 工作流 | 受控長時間執行、低風險 Auto-merge 候選 |
| A4 | Agent System Owner | 能治理跨團隊 Agent 平台、政策、資產及風險 | 管理 Agent Fleet 與制度，不代表無限制部署權 |

簡化理解：

```text
A0：問 Agent
A1：和 Agent 一起做
A2：把明確任務交給 Agent
A3：設計 Agent 做事的方法與閉環
A4：管理跨團隊的 Agent 工程系統
```

### 4.4 Risk Class：任務風險

| 風險 | 說明 | 例子 |
|---|---|---|
| R0 Green | 低風險、可逆、容易驗證 | 文件、格式、測試草稿、Prototype |
| R1 Yellow | 一般 Production 變更 | UI、一般 API、Pipeline、可逆 Refactor |
| R2 Orange | 高影響或高權限變更 | Auth、Schema Migration、Production IaC、敏感資料 |
| R3 Red | 安全、法規或不可逆關鍵變更 | Safety Firmware、Signing Key、Production Delete、ASIL 功能 |

基本原則：

- 高 Agent Level 只能提高「可設計與負責的自動化複雜度」。
- 任務風險仍可限制 Agent 是否能 Merge、Deploy 或取得 Credential。
- R3 原則上不能用 Model Review 取代獨立驗證與正式批准。

---

## 5. Repository 要提供的主要能力

## 5.1 個人定位與自我釐清

使用者可透過網站或 Coding Agent 問：

- 我的角色與 Domain 應選哪一條 Roadmap？
- 依目前工作證據，我可能位於 A1、A2 還是 A3？
- 我目前比較偏 Prototyper、Builder、Sweeper、Grower 還是 Maintainer？
- 目前有哪些能力缺口？
- 下一個 30／60／90 天應練習什麼？
- 哪些任務適合拿來作為升級證據？
- 哪些共用 Skill、Tool 或 Flow 適合我？

輸出不應只有一個分數，而應包含：

```text
Current Position Hypothesis
Strengths
Gaps
Required Evidence
Suggested Practice Tasks
Recommended Assets
Risk Boundaries
Next Review Point
```

## 5.2 角色與能力 Matrix

介面應能查看：

- Role × Agent Level
- Role × Archetype
- Agent Level × Risk Authorization
- Domain × Approved Assets
- Team × Capability Distribution
- Product Stage × Archetype Coverage
- Roadmap Node × Assessment／Evidence

Matrix 的目的是支援成長與資源配置，不應直接變成人員排名工具。

## 5.3 個人化 Roadmap

每位成員的 Roadmap 可由三部分組成：

```text
Common Foundation
+ Role-specific Path
+ Archetype／Domain Extension
```

例如：

```text
Backend Engineer
├── Common A0 → A1：安全、Context、Git、Explain-back
├── Backend A1 → A2：API、Transaction、Test、Observability
├── Maintainer Extension：Incident、Reliability、Performance
└── A2 → A3：CI Repair Loop、Dependency Workflow、Verifier Design
```

Firmware Engineer 則可能包含：

```text
Firmware Engineer
├── Common A0 → A1
├── Build／Flash／Debug／Trace
├── CAN／DBC／Static Analysis
├── SiL／HIL／Regression
├── MISRA／Traceability
└── Safety Boundary：禁止自主 Release 的範圍
```

## 5.4 共用 Agent 資產 Marketplace

Repository 應集中管理：

- Agent Skills
- Custom Agents／Subagents
- Commands
- MCP Servers／Tools
- Hooks
- Prompt／Instruction Packs
- Flow／Workflow
- Spec Templates
- Evals
- Test Fixtures
- Reference Architectures
- Checklists
- Evidence Package Templates

每項資產至少需要：

```yaml
id: backend-api-review
name: Backend API Review
type: skill
owner: backend-chapter
status: approved
version: 1.2.0
supported_tools:
  - claude-code
  - codex
  - github-copilot
roles:
  - backend
  - architect
minimum_agent_level: A2
allowed_risk:
  - R0
  - R1
requires:
  - repository-read
  - test-execution
prohibited:
  - production-write
verification:
  - contract-tests
  - security-checklist
  - human-review
```

## 5.5 Agent-assisted Assessment

Repository 可提供 Tool-neutral Assessment Flow：

1. 讀取使用者選擇的角色、Domain 與工作案例。
2. 透過反問釐清工作內容，而不是只讓使用者勾選自評。
3. 要求提供可驗證 Evidence，例如 PR、設計文件、Test Plan、Incident Review。
4. 執行實作題、Defective AI PR Review 或 Transfer Task。
5. 產生建議等級與缺口。
6. 由 Chapter Reviewer／Domain Owner 進行正式校準。

評鑑不能只看 Prompt 技巧，應評估：

- Problem framing
- System comprehension
- Technical judgment
- Test and verification design
- Debugging
- Security／risk awareness
- Delivery ownership
- Agent control and escalation

---

## 6. 資料層與渲染層分離

參考 roadmap.sh「圖結構資料 + Markdown 內容」的概念，但不綁定其私有渲染器或資料格式。

### 6.1 Source-of-Truth Repository

建議把資料分成四種：

| 資料 | 建議格式 | 用途 |
|---|---|---|
| Roadmap Graph | YAML／JSON | 節點、邊、前置關係、群組、位置 |
| Knowledge Content | Markdown | 說明、範例、練習、資源 |
| Machine-readable Models | YAML／JSON Schema | Level、Role、Risk、Asset、Assessment |
| Executable Assets | SKILL.md、Scripts、Config | Agent 可以直接使用的能力 |

Roadmap 節點透過固定 ID 綁定內容：

```text
roadmaps/backend/graph.yaml
content/backend/api-contract@backend-a2-014.md
assessments/backend/backend-a2-014.yaml
```

`backend-a2-014` 是跨 Graph、Content、Assessment、Evidence 與介面的共同 Key。

### 6.2 Renderer／Portal

渲染器應是獨立專案或獨立應用，可以替換而不影響內容：

```text
Repository Data
├── Web Portal
├── Matrix Dashboard
├── Coding Agent
├── IDE Extension
├── Static Site
└── BI／Management Dashboard
```

可能技術選項：

- Astro／Next.js／Vue
- React Flow／Vue Flow／D3
- Markdown／MDX renderer
- Full-text／Vector Search
- GitHub／Azure DevOps API
- Internal SSO

但第一版不應先被前端技術綁住；核心應先穩定 Schema、ID、內容治理與使用流程。

### 6.3 為什麼需要解耦

- Markdown 適合 Git Diff、PR Review 與多人協作。
- YAML／JSON 適合 Agent、程式與 Dashboard 使用。
- Renderer 可持續演進，不影響內容資產。
- 不同 Coding Agent 可讀取同一份標準資料。
- 部門未來可更換 Claude Code、Codex、Copilot 或內部 Agent，而不重寫制度。

---

## 7. 建議 Repository 結構

```text
department-agent-engineering/
│
├── README.md
├── AGENTS.md
├── CLAUDE.md
├── CONTRIBUTING.md
├── CODEOWNERS
│
├── docs/
│   ├── vision.md
│   ├── principles.md
│   ├── glossary.md
│   └── governance.md
│
├── models/
│   ├── agent-levels/
│   │   ├── A0.yaml
│   │   ├── A1.yaml
│   │   ├── A2.yaml
│   │   ├── A3.yaml
│   │   └── A4.yaml
│   ├── roles/
│   ├── archetypes/
│   ├── risks/
│   └── matrices/
│
├── schemas/
│   ├── roadmap.schema.json
│   ├── node.schema.json
│   ├── asset.schema.json
│   ├── assessment.schema.json
│   ├── evidence.schema.json
│   └── workflow.schema.json
│
├── roadmaps/
│   ├── common/
│   ├── product/
│   ├── architect/
│   ├── design/
│   ├── qa/
│   ├── frontend/
│   ├── backend/
│   ├── app/
│   ├── algorithm-ml/
│   ├── data-platform/
│   ├── firmware/
│   └── sre-security/
│
├── content/
│   ├── common/
│   ├── roles/
│   ├── archetypes/
│   └── domains/
│
├── assessments/
│   ├── self-assessment/
│   ├── practical-exams/
│   ├── defective-ai-prs/
│   ├── transfer-tasks/
│   └── rubrics/
│
├── assets/
│   ├── skills/
│   ├── agents/
│   ├── commands/
│   ├── hooks/
│   ├── mcp/
│   ├── tools/
│   ├── flows/
│   ├── templates/
│   └── plugins/
│
├── registry/
│   ├── proposed/
│   ├── pilot/
│   ├── approved/
│   ├── deprecated/
│   └── retired/
│
├── evals/
│   ├── datasets/
│   ├── scenarios/
│   ├── adversarial/
│   ├── regression/
│   └── results/
│
├── evidence/
│   ├── examples/
│   └── schemas/
│
├── casebook/
│   ├── success/
│   ├── failures/
│   ├── incidents/
│   └── lessons-learned/
│
├── adapters/
│   ├── claude-code/
│   ├── codex/
│   ├── github-copilot/
│   ├── kiro/
│   └── internal-agent/
│
└── .github/
    ├── copilot-instructions.md
    ├── agents/
    ├── instructions/
    ├── ISSUE_TEMPLATE/
    ├── PULL_REQUEST_TEMPLATE.md
    └── workflows/
```

---

## 8. 建議使用者介面

渲染介面不是第一版強制項目，但理想上應提供以下頁面。

### 8.1 My Position

顯示：

- Professional Role／Level
- Primary／Secondary Archetype
- Agent Level
- Domain Scope
- Risk Authorization
- 最近 Evidence
- 下一個建議節點

### 8.2 Interactive Roadmap

- 顯示已完成、進行中、建議、鎖定節點。
- 點擊節點可閱讀 Markdown、開啟資源、執行練習或呼叫 Coding Agent。
- 可依 Role、Archetype、Level、Domain 篩選。
- 可顯示節點完成所解鎖的能力，但避免把學習變成單純打勾遊戲。

### 8.3 Capability Matrix

- Role × Agent Level
- Team × Skill Coverage
- Domain × A3／A4 Owner
- Product × Archetype Coverage
- Risk × Authorized Owner

管理介面應預設聚合呈現，個人資訊需有適當權限，避免成為公開排名。

### 8.4 Asset Marketplace

每項資產顯示：

- 用途
- Owner
- 狀態與版本
- 支援工具
- 適用角色
- 最低 Level
- 風險範圍
- 使用方式
- Eval 結果
- 最近更新
- 已知限制

### 8.5 Workflow Registry

顯示每個自動化 Flow：

- Trigger
- Goal
- Allowed Scope
- Tool／Permission
- Verifier
- Stop Condition
- Escalation
- Human Approval Point
- Cost Limit
- Audit／Evidence
- Production Status

### 8.6 Ask the Repository

使用 Coding Agent 或部門 Agent 直接詢問：

```text
「我是 Backend A1，最近完成一個 API feature，
但還不熟交易一致性與 integration test，
請根據部門 Roadmap 幫我規劃下一個 30 天。」
```

或：

```text
「這個 Dependency Update Flow 是否允許用於 R2 系統？
目前還缺哪些批准與驗證？」
```

Agent 的回答必須引用 Repository 中的版本化內容，而不是只依一般模型知識回答。

---

## 9. 共用資產的治理方式

### 9.1 Asset Owner

每項共用資產都必須有明確 Owner，例如：

- Backend Chapter
- Firmware Chapter
- QA／QE Chapter
- Agent Platform Team
- Security Owner
- Product Operations

### 9.2 進入 Approved 前的最低條件

- 目的與觸發條件清楚
- 支援工具與版本清楚
- 最小權限清楚
- 禁止行為清楚
- 有可重現範例
- 有 deterministic 或可接受的 verifier
- 有失敗與停止條件
- 通過安全掃描與 Domain Review
- 有維護 Owner
- 有版本與 Changelog

### 9.3 不直接匯入外部 Skills

外部 Skill、Plugin、MCP 或 Agent 只能作為候選來源，應先經過：

```text
Source Review
→ License Review
→ Prompt／Instruction Review
→ Script／Dependency Scan
→ Permission Review
→ Domain Evaluation
→ Pilot
→ Internal Packaging
```

### 9.4 版本與過期機制

共用資產應記錄：

- Last verified date
- Supported tool／model versions
- Domain compatibility
- Deprecation date
- Replacement
- Known failures

避免 Skill 在工具或 framework 更新後仍被誤用。

---

## 10. 學習與成長 Roadmap

## Phase 0：Baseline 與治理基礎（0～1 個月）

- 盤點 60 人角色、Domain、產品階段與現有 Agent 使用方式。
- 定義 A0～A4 與 R0～R3。
- 選出 12～18 位跨職能 Pilot／Champion。
- 盤點 Repository、CI、Test、Documentation readiness。
- 建立 Tool、Data、Secret 與 License Policy。
- 建立最小 Repo Skeleton 與 Schema。

## Phase 1：安全與共同語言（1～3 個月）

目標：

- 全員至少理解 A0。
- 多數交付者開始 A1。
- Pilot 成員進入 A2 實作。

內容：

- Agent 限制
- Context
- Git／Rollback
- Small Batch
- Explain-back
- Testing
- Security／Sensitive Data
- 基本 Evidence Package

## Phase 2：角色化 Roadmap 與獨立委派（3～6 個月）

目標：

- 建立主要角色 Roadmap。
- 約 60～70% 交付者朝 A2 邁進。
- 上線第一批 Approved Skills 與 Flows。

必要資產：

- PM／Design／QA／Frontend／Backend／Data／Firmware Playbook
- Practical Assessment
- Defective AI PR
- Transfer Task
- Task-to-PR Flow
- Asset Registry

## Phase 3：受控 Loop（6～9 個月）

目標：

- 培養 12～18 位 A3 候選者。
- 建立 5～10 個正式 Workflow。
- 建立 Eval、Telemetry、Incident 與 Cost Tracking。

優先 Flow：

- CI Failure Triage
- Test Expansion
- Dependency Update
- Documentation Sync
- Data Quality Check
- UI Regression
- Static Analysis Classification
- SiL Test Generation

## Phase 4：Bounded Autonomy（9～12 個月）

目標：

- 3～6 位 A4／System Owner。
- 15～25% 團隊達到 A3 能力。
- 低風險且驗證充分的 Flow 可有限度 Auto-merge。
- R2／R3 繼續保留正式人類批准與獨立驗證。
- 用 Quality、Flow、Risk 與 Product Outcome 評估效益。

---

## 11. 第一版 MVP

第一版不需要先做完整網站，也不需要同時涵蓋所有角色。

### 建議 v0.1 內容

1. Repository Vision、Principles 與 Glossary
2. A0～A4 定義
3. R0～R3 定義
4. Agent Level × Risk Matrix
5. 三條 Role Roadmap：
   - Backend
   - QA／QE
   - Firmware 或 Data Platform
6. 三個 Assessment：
   - A1 Explain-back
   - A2 Defective AI PR Review
   - A2 Transfer Task
7. 三個 Approved／Pilot Assets：
   - Code Review Skill
   - Test Design Skill
   - CI Failure Triage Flow
8. Asset Schema 與 Workflow Schema
9. Coding Agent 問答入口：
   - Where am I?
   - What should I learn next?
   - Which assets can I use?
10. 一個簡單的 Static Site 或 Matrix View

### 暫不做

- 一開始就打造複雜 HR 系統
- 以 Level 直接決定升遷與績效
- 全自動 Production Deployment
- 把所有外部 Skill 全部同步進來
- 為每種工具重寫一套內容
- 過早建立龐大的 Knowledge Graph

---

## 12. 成效衡量

### 12.1 Capability

- A0～A4 分布
- Roadmap completion with evidence
- Explain-back 通過率
- Transfer Task 表現
- A2／A3 評鑑通過率
- 各 Domain 是否有足夠 Owner

### 12.2 Adoption

- Active users
- Approved asset usage
- Repeat usage
- Role coverage
- Agent-assisted Assessment 使用率

### 12.3 Engineering Flow

- Change lead time
- Review rework
- Deployment frequency
- Failed deployment recovery time
- Change fail rate

不同產品型態不可直接互相比排名，例如 Firmware、App 與 Data Pipeline 應分開解讀。

### 12.4 Quality／Risk

- Escaped defects
- Agent-related rollback
- Security findings
- Unauthorized tool calls
- Workflow escalation rate
- Workflow false success
- Outdated asset usage
- Incident and recovery evidence

### 12.5 Product Outcome

依 Archetype 使用不同指標：

| Archetype | 建議成果指標 |
|---|---|
| Prototyper | 從想法到可驗證實驗的時間、有效學習數 |
| Builder | 從通過驗證到正式交付的時間與品質 |
| Sweeper | 複雜度、技術債、效能、移除與整併成果 |
| Grower | Adoption、Retention、Feedback Cycle |
| Maintainer | Reliability、Security、Cost、Recovery |

---

## 13. 主要風險

### 13.1 把 Agent Level 誤用成績效排名

對策：

- Level 描述委派與驗證能力，不等同原有專業職級。
- 以成長討論為主，不公開個人排行榜。
- 評估必須依 Domain，不給單一永久總分。

### 13.2 只建文件，沒有使用閉環

對策：

- 每個 Roadmap Node 連接任務、Assessment 與 Asset。
- 讓 Coding Agent 能直接讀取並引用 Repository。
- 定期從實際專案回收案例與失敗。

### 13.3 Skills 變成未驗證 Prompt 垃圾場

對策：

- 建立 Registry 狀態、Owner、Eval 與 Retirement。
- 專業 Skill 必須由 Domain Chapter 負責。
- 優先小型、可組合、可測試資產。

### 13.4 高能力被誤解為可以取消治理

對策：

- 高 Agent Level 只提高可設計的自動化複雜度。
- Risk Class、Credential、Verifier 與 Release Gate 保持獨立。
- 高風險任務永遠保留責任人。

### 13.5 Renderer 先行，內容與制度空洞

對策：

- 先穩定 Schema、ID、Matrix、Roadmap 與 Asset Lifecycle。
- 前端第一版可使用簡單 Static Site／Matrix。
- Renderer 可替換，Repository Data 不被 UI 綁定。

---

## 14. 值得參考的公開專案

> 篩選原則：本輪研究以公開 GitHub Repository、超過 100 Stars、近期仍有實質更新，且對本構想具有直接參考價值為主。Stars 與活躍度會持續變動，正式採用前應重新確認版本、License、Security 與維護狀態。

## 14.1 核心模型、Roadmap 與流程

| 專案 | 值得參考的部分 | 在本 Repository 的建議用途 |
|---|---|---|
| [PaulDuvall/ai-development-patterns](https://github.com/PaulDuvall/ai-development-patterns) | Readiness、Codified Rules、Security Sandbox、Verification、Bounded Autonomy、Loop Engineering patterns | Automation maturity、Verifier、Workflow pattern |
| [nilbuild/developer-roadmap](https://github.com/nilbuild/developer-roadmap) | 跨角色 Roadmap、技能節點、前置關係、內容與圖結構 | Roadmap 資料模型與呈現概念；注意其 License |
| [github/spec-kit](https://github.com/github/spec-kit) | Constitution、Specify、Clarify、Plan、Tasks、Analyze、Implement | 正式 Feature Delivery Golden Path |
| [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) | Spec-driven Development、Change Proposal、跨 Repository 規格管理 | 中央 Spec／Planning Store |
| [awslabs/aidlc-workflows](https://github.com/awslabs/aidlc-workflows) | Tool-neutral AI Development Lifecycle、可驗證與自我修正流程 | 跨 Claude Code、Codex、Copilot 等工具的 Delivery Flow |
| [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) | PM、Analyst、Architect、UX、Developer、QA 的多角色 Agent 與 Lifecycle | 跨職能 Artifact、Handoff 與 Role Playbook |
| [microsoft/agent-governance-toolkit](https://github.com/microsoft/agent-governance-toolkit) | Policy、Zero Trust、Capability、Sandbox、Audit、Reliability | Governance Plane、Permission 與 Runtime Control |
| [obra/superpowers](https://github.com/obra/superpowers) | Brainstorm、Plan、TDD、Subagent、Review、Evidence Discipline | A1→A3 工作法、Composable Skills、Assessment 素材 |

## 14.2 共用 Agent 資產與 Marketplace

| 專案 | 值得參考的部分 | 建議用途 |
|---|---|---|
| [anthropics/skills](https://github.com/anthropics/skills) | Agent Skills 格式、`SKILL.md`、Scripts、References | 內部 Skill 標準與 Packaging |
| [github/awesome-copilot](https://github.com/github/awesome-copilot) | Agents、Instructions、Skills、Hooks、Workflows、Plugins 的大型 Catalog | 內部 Marketplace 分類與資產範例 |
| [EveryInc/compound-engineering-plugin](https://github.com/EveryInc/compound-engineering-plugin) | 多 Coding Agent 支援、Planning、Research、Review、Implementation plugin | Tool Adapter 與 Compound Engineering Flow |
| [wshobson/agents](https://github.com/wshobson/agents) | 大型 Agent／Plugin／Skill Registry | Registry 分類與資產發現；採用前必須內部審查 |
| [mattpocock/skills](https://github.com/mattpocock/skills) | 小型、可組合、工程導向 Skills | Skill Granularity 與 Composability |
| [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) | 工程、QA、DevOps、Security、Product、Compliance 等跨職能 Catalog | Role Coverage 檢查與候選 Skill 來源 |

## 14.3 角色專用參考

| 專案 | 角色／領域 | 建議用途 |
|---|---|---|
| [phuryn/pm-skills](https://github.com/phuryn/pm-skills) | Product Manager | Discovery、Strategy、Execution、Launch、Growth Skills |
| [deanpeters/Product-Manager-Skills](https://github.com/deanpeters/Product-Manager-Skills) | Product Manager | PM Competency、教學與完整方法庫 |
| [tractorjuice/arc-kit](https://github.com/tractorjuice/arc-kit) | Architect | Architecture Governance、ADR、Assurance、Capability Building |
| [zhinkgit/embeddedskills](https://github.com/zhinkgit/embeddedskills) | Firmware／Embedded | Build、Debug、Flash、CAN、Debugger、Hardware Verification Skills |

## 14.4 參考時的原則

不建議直接 Fork 其中一個專案，改名後當成部門制度。較合理的組合是：

```text
developer-roadmap
→ 如何表達角色與學習路線

ai-development-patterns
→ 如何定義成熟度、驗證與自動化上限

Spec Kit + OpenSpec + AI-DLC
→ 如何管理規格、計畫與交付

BMAD
→ 如何定義跨職能角色與 Artifact Handoff

Agent Governance Toolkit
→ 如何管理 Permission、Sandbox、Policy 與 Audit

Anthropic Skills + awesome-copilot
→ 如何封裝與分發可執行資產

Superpowers + Compound Engineering
→ 如何建立可重複的日常工程工作法
```

本部門 Repository 最有原創價值的部分，將是目前尚未被單一高星專案完整解決的交集：

> **個人 Agent Level × 專業領域 × Product Archetype × 任務風險 × 驗證能力，共同決定自動化建議與授權。**

---

## 15. 初步決策建議

### Repository 名稱

推薦：

```text
department-agent-engineering
```

其他選項：

```text
agent-engineering-operating-model
agent-enabled-delivery
agent-capability-and-assets
department-agent-os
```

### 第一階段技術決策

- Source of Truth：Git Repository
- Human Content：Markdown
- Structured Model：YAML + JSON Schema
- Roadmap Graph：YAML／JSON Nodes and Edges
- Agent Asset：Agent Skills-compatible folders
- Tool Entry：`AGENTS.md` + Tool-specific adapters
- Review：Pull Request + CODEOWNERS
- 第一版 Renderer：Static Site 或簡單 React Flow／Vue Flow
- Search：先全文搜尋，後續再加 Semantic Search
- Assessment：先以 Markdown／YAML + Agent-guided Flow 執行
- Identity／Authorization：沿用公司 Git 與 SSO，不在 v0.1 自建

### 組織 Owner

建議成立小型 Agent Enablement Guild：

- Agent Program Owner
- Architecture／Platform
- QA／Evaluation
- Security／Governance
- Learning／Community
- 各主要 Chapter Champion

其角色是建立 Golden Path 與共用資產，不是成為所有 Agent 使用的中央審批瓶頸。

---

## 16. 最終願景

這個 Repository 最終不只是「告訴大家怎麼使用 Coding Agent」，而是成為部門內連接人、方法、資產與治理的共同底盤：

```text
人員知道自己在哪裡
→ 知道下一步怎麼成長
→ 取得符合角色的做法與資產
→ 在正確風險邊界內使用 Agent
→ 產生可驗證的工作證據
→ 將成功與失敗回饋到共用 Repository
→ 讓部門的 Agent Engineering 能力持續累積
```

核心目標不是讓每個人少寫多少 Code，而是：

> **讓不同角色的人，能在具備足夠專業判斷與驗證能力後，安全地負責更大的工程與產品閉環。**