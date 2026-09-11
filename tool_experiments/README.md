# tool_experiments — 工具實驗區

> 本資料夾用來 clone 外部專案做本機測試與評估，**所有 clone 進來的子資料夾皆被 `.gitignore` 排除**，不會跟隨本 repo 追蹤。
> 此 README 與 `.gitignore` 本身會保留追蹤，作為「目前正在測試什麼」的指南。

來源：`00_context/agent_projects_list.md`（社群觀察）
最後更新：2026-08-29

---

## 一、AI Agent / Coding Agent 框架（哲學比較中）

### 1. `hermes-agent/` — 成長型個人 agent

連結：<https://github.com/nousresearch/hermes-agent>

- **What**：Nous Research 出品的「自我進化型」AI agent。內建 learning loop，agent 會在執行任務後自動產出可重用的 skill、在使用過程中自我修正、並把長期使用者偏好沉澱成跨 session 的個人模型。支援多通道（Telegram / Discord / Slack / WhatsApp / Signal / CLI），可跑在 $5 VPS、GPU cluster 或 serverless 上，模型可任選（OpenRouter、NIM、GLM、Kimi、自架 endpoint 都行），不綁特定供應商。
- **How**：核心是 skill 分層載入機制——平常只吃 ~3000 tokens 載入 skill 清單，需要時才展開細節。配合 FTS5 全文搜尋能撈到過往 session、Honcho dialectic user modeling 建立人格畫像、cron scheduler 排定無人值守任務、subagent 平行展開多工。可一鍵從 OpenClaw 遷移試水溫。
- **Why**：適合相信「agent 應該越用越強」、願意陪它長大的人。賣點不是裝完就穩，而是長期投資複利。
- **測試記錄**
  - 2026-04-29 ｜ clone 完成，初步看 README 與架構
  - 痛點：retry loop 嚴重，任務失敗後不會換策略，會一直用同樣方式重試，token 燒得很快（社群回報兩小時輕度使用 ~400 萬 tokens、Telegram 用法是 CLI 的 2-3 倍）
  - 觀察：skill 分層設計確實聰明，但目前還在「概念漂亮、實作未成熟」階段，先放著觀望

### 2. `openab/` — 團隊型 agent harness

連結：<https://github.com/openabdev/openab>

- **What**：Pahud 與台灣社群用 Rust 寫的開源 ACP harness。它**不是**一個 AI agent，而是讓團隊「在 Discord / Slack 裡共用一個 coding agent」的中介層。後端可插任何 ACP-compatible CLI（Kiro CLI、Claude Code、Codex、Gemini、OpenCode、Copilot CLI 等），Telegram / LINE 也透過 standalone gateway 支援。
- **How**：架構極簡——`Discord/Slack ←→ openab (Rust) ←→ ACP CLI（stdio JSON-RPC）`，不開額外 port。Edit-streaming 每 1.5 秒更新 Discord 訊息、後端可插拔（改 config 就能換 agent）、k8s 原生部署、Zeabur 一鍵模板填三個變數就上線。
- **Why**：解決的是「多人 ↔ 一個 agent」的場景，與 Hermes / OpenClaw 的「一人 ↔ 一個 agent」互補。Pahud 哲學：「AI 時代做加法太容易了，我們要做減法」——只把 Discord 多人共用 agent 做到極致。
- 參考：[等 Uber 的五分鐘，四個 AI 幫我規劃完整個功能——回到辦公桌，我只做 Code Review](https://wchung.tw/blog/waiting-for-uber-four-ai-agents-planned-my-feature?fbclid=IwY2xjawRenc5leHRuA2FlbQIxMABicmlkETFGNTM1aXJ4b0JpM1p1SzR3c3J0YwZhcHBfaWQQMjIyMDM5MTc4ODIwMDg5MgABHhMaW8caWHZZWpf1_EdZG2UziDV2Gik2QpovbpsC2gmyqe0nDauoWDnFnXdk_aem_w592LVi0hXk9dvBg4xiMjg)
- **測試記錄**
  - 2026-04-29 ｜ clone 完成
  - 待測：用 Claude Code 當後端跑一個 Discord workspace，驗證團隊共用體感
  - 假設：若 PM 團隊要一起問同一個 codebase，這套會比每人各裝一份 CLI 直觀

### 3. `openclaw/` — 記憶型主力 agent

連結：<https://github.com/openclaw/openclaw>

- **What**：250K+ stars 的個人 AI 助理（基金會治理）。定位是「在你已經用的 channel 上回應你的個人助理」——支援 WhatsApp、Telegram、Slack、Discord、Google Chat、Signal、iMessage、IRC、Teams、Matrix、Feishu、LINE 等 20+ 通道。Gateway 只是 control plane，產品的價值在 assistant 本身。
- **How**：差異化在記憶架構——搭配 Obsidian vault 存長期記憶（不是丟進 SQLite 就忘）、Crontab + dreaming 機制每晚自動整理 context、Linear 串任務進度。可以同時跑多個 agent 各司其職（寫文章的、寫程式的、管工作的），長期累積後 agent 會「知道你昨天做到哪」。
- **Why**：用久了會發現——agent 強不強，取決於你餵它的記憶結構好不好。OpenClaw 的生態（Obsidian + dreaming + Linear）把長期記憶這件事做得最完整，適合長期穩定作戰。
- **測試記錄**
  - 2026-04-29 ｜ clone 完成（16803 個檔案，repo 體積較大）
  - 待測：搭 Obsidian vault 跑 dreaming，看一週後 context 整理品質
  - 假設：作為 PM 主力大腦，能補足 Claude Code session 之間缺乏記憶的問題

---

## 二、其他先前已 clone 的測試專案

### 4. `agent-skills/` — Coding lifecycle 的 skill pack

連結：<https://github.com/addyosmani/agent-skills>

- **What**：Addy Osmani 維護的「production-grade engineering skills」集合，把資深工程師在 Define → Plan → Build → Verify → Review → Ship 各階段的工作流、品質門檻、最佳實踐打包成 skill，讓 AI coding agent 能一致地遵循。
- **How**：提供 7 個 slash command（`/spec`、`/plan`、`/build`、`/test`、`/review`、`/code-simplify`、`/ship`），每個 command 自動載入對應的 skill 集合。例如 `/spec` 會啟動「先寫規格再寫 code」的工作流、`/build` 強制「一次只做一個 atomic task」。
- **Why**：對齊 AgentCodingPM 的 R0–R3 風險分層 / Gate A–E 品質門檻概念——這套 skill pack 等於把最佳實踐外部化、避免每次都要在 prompt 裡重複交代規範。直接套用或挑選 skill 都可，最適合作為自家 skill 設計的對照組。
- **測試記錄**
  - 2026-04-25 ｜ clone 完成
  - 觀察：與本 repo 的 `.agents/skills/` 結構相似，可作為「skill 寫法 / checklist 結構」的參考來源
  - 待測：把 `/spec` 與 `/review` 套到 review_test_qa 的真實案例上比對

### 5. `better-agent-terminal/` — 多終端 + Agent 整合 GUI

連結：<https://github.com/tony1223/better-agent-terminal>

- **What**：Tony 用 Electron 寫的跨平台終端聚合器（Windows / macOS / Linux），可在單一視窗管理多 workspace 的 terminal，並內建 Claude Code agent panel、檔案瀏覽器、git viewer、snippet 管理、遠端存取。
- **How**：Claude Code agent 直接嵌在右側 panel，含權限控制、status line、streaming output；長時間任務（build、watch、monitor）可在獨立 terminal 跑，不會卡住 agent 對話。多 workspace 切換不用開多個視窗。
- **Why**：解決「Claude Code 開在 iTerm + 還要另外開幾個終端 + IDE 切來切去」的多視窗疲勞。對於 PM 同時管多個 sub-repo（MI / VMS / SmartSignage / DatabricksAnalysisAgent）的場景特別有感。
- **測試記錄**
  - 2026-04-25 ｜ clone 完成
  - 待測：在 PM_Head_work 用一週，比對與純 iTerm + Claude Code 的工作流差異
  - 風險：Electron 應用記憶體佔用，長時間開著要看狀況

### 6. `spectra-app/` — Spec-Driven Development 的桌面版 GUI

連結：<https://github.com/kaochenlong/spectra-app>

- **What**：高見龍寫的桌面 app，把 [OpenSpec](https://github.com/Fission-AI/OpenSpec) 的 Spec-Driven Development（SDD）工作流帶到 GUI——管理 spec 文件、change proposal、task 進度，不用再用 CLI 手動編輯 `openspec/` 目錄、YAML 與 Markdown。資料格式與 OpenSpec 完全相容。
- **How**：自動解析 Markdown checklist (`- [ ]` / `- [x]`) 推算 task 進度、一鍵 archive 完成的 change、整合 Claude Code 的 `/spectra-discuss` slash command 做設計討論、內建 contradiction / gap scanner 比對 proposal、spec、design、task 之間的不一致。
- **Why**：SDD 是 AgentCodingPM「需求定義」瓶頸的關鍵解法——當 agent 寫 code 越快，需求模糊就越致命。Spectra 把「讓 spec 變成可視化、可追蹤、可追溯到 task」這件事用 GUI 做出來，比純 CLI/Markdown 工作流更容易讓非工程角色一起參與。
- **測試記錄**
  - 2026-04-25 ｜ clone 完成
  - 待測：跑一個 `priority-decide` 或 `prd-write` 的真實 PM 案例，看 SDD 能否取代目前的 PRD-only 流程
  - 假設：適合作為 OPP → Backlog 之間的「規格化」工具

### 7. `orca/` — 多 Agent 編排的開發環境（ADE）

連結：<https://github.com/stablyai/orca>

- **What**：Stably AI 出品的 ADE（Agent Development Environment），MIT 授權。它不是模型也不是 agent，而是架在所有 CLI Agent（Claude Code / Codex / Cursor / OpenCode / Grok 等 30 餘種）上面的一層統一工作介面。自我定位：「The AI Orchestrator for 100x builders」。
- **How**：核心是「同時跑、看得見、比得了、合得回去」四件事——Parallel Worktrees（同一 prompt 扇出給多個 agent，各自隔離工作區）、Terminal Splits（WebGL 渲染、scrollback 跨重啟保留）、Annotate AI Diffs（在 diff 任一行留評論丟回 agent）、Design Mode（點瀏覽器裡的 UI 元素直接塞進 prompt）、SSH Worktrees（agent 跑遠端）、Mobile Companion（手機監看與追加指令）、Orca CLI（`orca worktree create` / `snapshot` / `click` / `fill`——**agent 也能驅動 Orca 自己**）。
- **Why**：對照 Better Agent Terminal 的「多終端聚合」，Orca 押的是更上層的**協調層**：當操作單位從「檔案與程式碼」變成「Agent session、worktree、任務、修改結果」時，需要的是隔離／監看／審查這三件事的工務所。其 `AGENTS.md` 本身也是很好的教材（SSH 場景把判定詞彙鎖死為 `live`/`unverifiable`/`exited`，明文寫下「失聯不等於死亡」）。
- **測試記錄**
  - 2026-08-23 ｜ 朋友推薦，研究 repo 與社群討論，落成 capture `card_notes/003_Capture/20260823_Orca_開源多代理開發環境初探.md`
  - 2026-08-26 ｜ 寫成發佈文〈Orca 想當的不是更好的 IDE，是 Agent 們的工務所〉
  - 2026-08-27 ｜ clone 完成
  - 判斷：**對數據工程場景先踩煞車**——任務常共享同一條管線／schema／分析脈絡，硬切成多個隔離工作區，整合成本可能吃掉平行化收益。試驗清單只放低相依、可驗收的場景（同一數據問題不同解法／文件與測試獨立重構／套件升級／比較兩家 Agent 的設計判斷）
  - 延伸：與 better-agent-terminal 一起作為「為自己的工作方式打造環境」的思維參考，見 `personal_agent_design/20260829_我與Agent的互動方式_方向素材.md` §2

---

## 三、選型一句話摘要

- **成長型** — 個人 agent 自己長技能 → Hermes Agent
- **團隊型** — Discord 共用 coding agent → OpenAB
- **記憶型** — 長期穩定作戰、記憶為王 → OpenClaw
- **流程型** — 為 coding agent 套上 lifecycle skill → agent-skills
- **介面型** — 多終端 + agent panel 整合 → Better Agent Terminal
- **編排型** — 多 Agent 平行 worktree + 監看 + 比較合併 → Orca
- **規格型** — Spec-Driven Development GUI → Spectra

主流組合建議：OpenClaw 當主力大腦 + OpenAB 給團隊用，互補不互搶；agent-skills 與 Spectra 作為流程資產來源。

---

## 四、操作慣例

- 重新 clone：直接在本資料夾 `git clone <url>`，會自動被 `.gitignore` 排除
- 新增測試專案：在本 README 加一節（含 What / How / Why / 測試記錄 / 連結）
- 移除測試：直接刪除子資料夾即可，不影響 PM repo
- 淘汰但保留資訊：在標題後加 `（已棄用）`，記錄棄用原因，避免日後重複踩雷
- 每次跑出新觀察，補一行到對應條目的「測試記錄」（含日期）
