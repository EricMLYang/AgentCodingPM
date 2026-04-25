# Agent Coding 進階用法 — Pattern 整理

> 來源：`20260425_AgengCoding進階用法.md`（Tonyq Wang、高健龍、wchung 等人 FB/部落格觀察）
> 整理目的：抓出可持續 follow 的 pattern，而不是只蒐集片段技巧。
> 整理日期：2026-04-25

---

## 0. 我內心隱含想搞懂的問題清單

把原檔讀到的訊號，反推成「我其實在追什麼」：

1. **頂尖玩家在用什麼工具？他們為什麼自己做工具？** ←「Harness Engineering」這個詞背後到底是什麼。
2. **CLI Agent vs IDE Agent vs Web，誰才是主流？** 為什麼 Tony 敢說「Claude Code 這類 CLI Agent 100% 提升」。
3. **多 Agent 真的不行嗎？** 還是「現在不行，但要知道何時會行」。
4. **Plan mode、Skill、Context 管理三者怎麼搭？** 哪個是真正的槓桿？
5. **Spec-Driven Development（SDD）到底是不是新瓶裝舊酒？** 跟我們 Inbox→OPP→Backlog→PRD 的流程有什麼關係。
6. **MCP 該怎麼用才安全？** OpenClaw vs OpenAB 的差異教了什麼。
7. **Procfile / overmind 為什麼讓 Tony 「相見恨晚」？** 開發環境本身是不是 Agent Coding 的隱形瓶頸。
8. **怎麼看待測試？** Tony 幾乎放棄 test case、靠 1:10 人工驗證，這合理嗎？什麼條件下適用？
9. **Context window、thinking token、cache write 的經濟學是什麼？** 我每送一個 message 到底花了什麼成本？
10. **趨勢面：native binary、SDK 去重量化、企業自建系統 — 在告訴我什麼？**

---

## 1. 工具地圖（連結都進去讀過）

### 1.1 自製工具流派（這群人共同點：自己做、為自己做）

| 工具 | 作者 | 一句話定位 | 解決的痛 | 連結 |
|------|------|-----------|---------|------|
| **BAT (Better Agent Terminal)** | Tonyq Wang | Electron 多工作區終端 + 內建 Claude Code SDK + 整合 Procfile | 多專案多 terminal 的混亂、AI agent 與 terminal 切來切去、token/cost 黑盒 | https://github.com/tony1223/better-agent-terminal |
| **Spectra** | 高健龍（5xCamp） | 跨 Agent 的 SDD 工具包（discuss → propose → apply → ingest → archive） | AI 沒結構就製造混亂，需要規格做護欄，避免 context 漂移與 scope creep。支援 23 種 Agent | https://spectra.5xcamp.us/ |
| **Sherly Terminal** | 高健龍 | 知道當下跑的是哪個 Agent 的終端機（OSC 0/2 解析）、把 Claude Code / Codex 的 statusline 整進 status bar | 「畫面上都是自己做的工具」— 工作流的底層 UI 是自己掌控的 | （部落格/社群） |
| **OpenAB** | wchung 等 | Rust 開源、Figma MCP 讀 + Jira MCP 寫，限定權限。「AI generates. Humans authorize.」 | 不要給 AI 完整 file/shell 權限（OpenClaw 的反面） | https://wchung.tw/blog/from-openclaw-to-openab-automating-design-handoff-with-ai |
| **codex-shim** | Tonyq | 為了用 client/server 模式開 codex 而寫（已踩坑） | Codex CLI 給的東西被封裝太多，要自己撬出來 | （未發布） |
| **/auto-continue** | Tonyq | 偵測 codex 停下後送 prompt 叫他繼續，可設次數 | Codex 中斷感太重 | （自製 slash command） |

### 1.2 借用的開源基底

| 工具 | 為什麼出現 | 連結 |
|------|-----------|------|
| **Overmind** | Procfile-based 多 process 管理，tmux 控制模式無損 log。BAT 把它的精神內建。Tony 說「跟 git/DI 工具同級的相見恨晚」 | https://github.com/DarthSim/overmind |
| **DBeaver CE + Eclipse plugin** | 把 Claude Code SDK 包成 plugin，內部 MCP 連 DDL/查詢 — 「MCP 做隔離層」反例之一 | （Tony 自製 POC） |
| **GitHub spec-kit** | SDD 的官方 toolkit，搭配 DeepLearning.AI 課程 | https://github.com/github/spec-kit |
| **DeepLearning.AI SDD 課程** | 教 project constitution、feature specs、跨 session 保 context | https://www.deeplearning.ai/short-courses/spec-driven-development-with-coding-agents/ |

### 1.3 觀察重點（不是工具，是訊號）

- **Claude Agent SDK 連續 5 版去重量化**：拔 cli.js 依賴、改 native binary、不再內建 cli — 暗示 Anthropic 要把 SDK 塞到新環境。
- **Claude Code 改版重點**（Tony 標記）：v113 native binary、v116-117 效能、v117 Opus 4.7 context 修復、v118 vim visual mode、v119 /config 寫檔。
- **/rewind 指令** 是 context 管理的關鍵 — focus 比 context 大小重要。Gemini 1M context 早就有了沒贏，就是因為不專注。

---

## 2. Pattern 萃取（橫跨多人觀察的共通結構）

> 這 10 條才是真正要 follow 的 pattern。工具會換，這些不會。

### Pattern A — 「Harness Engineering」：頂尖玩家自己做工具

**訊號**：Tony 做 BAT、高健龍做 Spectra + Sherly、wchung 做 OpenAB。三個人不約而同把力氣花在「讓 Agent 穩定產出的生產線」，而不只是用 Agent。
**底層信念**：「不用管哪家 Coding Agent，也不用管模型怎麼升級。AI 大廠打得越兇，這套流程只會越順。」（高健龍）
**對我的意涵**：AgentCodingPM 不只是「用 Claude Code 寫 code」，而是「設計 harness 讓全團隊用 Agent 產出可驗證成果」。

### Pattern B — CLI Agent > IDE / Web Agent

**訊號**：Tony 估 Cursor 類提升 15–30%，Claude Code 類 CLI Agent 翻倍。
**為什麼**：input 品質。CLI 能讀檔、跑指令、看錯誤、改程式、跑測試 — 直接接真實工作環境。Web 要人肉貼資訊、IDE 為了省 token 會壓縮 context（副作用大）。
**例外/邊界**：DBeaver plugin 反例 — 在「封閉 DB 連線環境」這種特殊場景，IDE plugin + 內部 MCP 反而乾淨。
**對我的意涵**：團隊推廣 Agent Coding 時，CLI Agent 是主軸；IDE 整合只在「特殊封閉領域」才優先。

### Pattern C — 單 Agent + Skill + Context 管理 ＞ 多 Agent

**訊號**：Tony 明確反對現階段多 Agent；高健龍把 Skill 做進 Spectra；Anthropic 的 Skill 機制本身。
**為什麼**：多 Agent 切 context = 變笨；綜合判斷類任務切碎反而失去整體理解；token 控制更難。
**對策**：把人類經驗濃縮成 Skill，讓單一 Agent 在需要時調用。要平行就開多個 Claude Code session，不要塞進「多 Agent 系統」。
**對我的意涵**：AgentCodingPM 的 `.github/skills/` 路線是對的；不要急著做 multi-agent orchestration。

### Pattern D — Plan Mode 是底線，自我驗證是新能力

**訊號**：Tony「沒 plan mode 我已經不會用了」、Codex 的 plan mode 比 Claude Code 聽話、GPT 5.5 自己會切實驗、保留成功條件、躲坑。
**模型行為新觀察**：「他自己具有某種疊代性思考，會去想失敗怎麼切、成功怎麼保留，把環境設計成具實驗條件的環境」— 這在取代很多以前要靠 flow 引導的工作。
**對我的意涵**：風險分層的 L1/L2 應該強制走 plan mode；L0 可放開。同時開始觀察「模型自我驗證能力」何時夠強，能省掉哪些 flow 步驟。

### Pattern E — Spec-Driven Development 是 context 跨 session 的解法

**訊號**：Spectra 流程 = discuss → propose → apply → ingest → archive；DeepLearning.AI/JetBrains 的 SDD 課強調 project constitution + feature spec 跨 session 保 context、降低 cognitive debt。
**核心點**：spec 不是文件潔癖，是「讓下一個 session 的 Agent 知道前面為什麼這樣做」的 context 壓縮機制。
**對我的意涵**：我們的 `00_context/` + `05_product/Outcome → OPP → Backlog → PRD` 本來就是 SDD 雛形。可以直接對照 spec-kit / Spectra 的成熟做法強化。

### Pattern F — MCP 是隔離層，不是「給 AI 全權」

**訊號**：OpenClaw（給 AI 完整 file/shell 權限）→ OpenAB（MCP 限定「可讀 Figma、可寫 Jira、禁止其他」）。Tony 的 DBeaver plugin 也是「MCP 做隔離層比較乾淨」。
**口號**：AI generates. Humans authorize.
**對我的意涵**：對應到我們的五大品質門檻 B（Agent 工具安全 / 最小權限）。MCP 應該優先用在「跨系統授權」場景，不是隨便加。

### Pattern G — 開發環境本身是 Agent Coding 的隱形瓶頸

**訊號**：Tony 對 Procfile 「相見恨晚」、把 overmind 整進 BAT。背景：每開新專案 setup 都要弄 DB、docker、TLS、cron、cloud sql… 多 terminal 黑洞。
**Pattern**：把「啟動環境的雜事」也納入 Agent 管理範圍 — 一個畫面（Procfile + AI 寫指令）解決所有 stateful process。
**對我的意涵**：團隊推廣 Agent Coding 時，**開發環境模板化（Procfile/devcontainer）** 跟 prompt/skill 一樣重要。否則 onboarding 永遠卡住。

### Pattern H — Context 經濟學：read / think / write 都吃 token

**訊號**：Tony「context 戰爭迷霧」、thinking 多會壓縮其他資訊、5m vs 1h cache write 各有取捨、cache hit/miss 線性影響成本、休眠 session 喚醒會整批 write。
**思考框架**：
- Context 管理 = 把問題投影到低維度 + DP 快取已解過的子問題。
- 投影方法暗示「你預設的答案」— 預設錯，後面全錯（駕駛重要）。
- 競爭未來 3 軸：context 大小、token 效率、thinking 黃金比例。
**對我的意涵**：團隊要學的不是「prompt 工程」，是「context 工程」。每個 read/think/write 都是成本 — 教大家算這筆帳。

### Pattern I — 1:10 人工驗證取代 test case

**訊號**：Tony「全面放棄 test case，整合測試佔 0.1%；讓 Agent cook 10 小時，自己用 1 小時積極驗證關鍵場景；場景是設計過的」。
**先決條件**：模型本身有規劃 + 自我驗證能力；驗證者懂全流程能設計關鍵場景。
**警告/邊界**：這對應 Tony 個人 / 高熟練度 / 探索型專案。**對團隊推廣不是 1:1 套用**：L2（跨服務契約、Schema migration）絕對需要 test/contract test；L0/L1 才能逐步放掉。
**對我的意涵**：把這條寫成「驗證策略矩陣」(L0/L1/L2 × test 覆蓋深度)，不要一刀切。

### Pattern J — 治理觀：模型還在童年，給空間探索

**訊號**：「你會在意小朋友五歲前的治理秩序嗎？只要安全沒問題，剩下就讓他自由探索，告訴我們他世界長什麼樣，我們順著他探索的決定允許或禁止。」（Tony）
**邏輯**：Sonnet 4 / Opus 4.5 / 4.6 / 4.7 都是躍進中，過早治理會錯過模型能告訴你的事。
**配套**：但要 git 等存檔工具當安全網 + 高品質人類驗證。
**對我的意涵**：L0/L1 給更多空間探索；只在 L2 +「不可逆/敏感資料/跨契約」處嚴管。治理不等於普遍管制。

---

## 3. 趨勢觀察（Tony 與其他人都在指）

| 趨勢 | 觀察點 | 對 AgentCodingPM 的意涵 |
|------|--------|----------------------|
| Claude Agent SDK 持續去重量化 | 拔 cli.js 依賴、native binary、可換 cli path | 預期會出現「整合 Agent SDK 的殺招」服務 — 提早觀察整合機會 |
| 企業自建系統取代 SaaS 分潤 | 資料自主權變核心競爭力 | 我們做的內部 harness 本身是資產 |
| Coding Agent 競爭 = context × token 效率 × thinking 比例 | 不是模型變大就好 | 培訓重點：context 工程、cost awareness |
| Multi-Agent 還太早 | 但「下一個讓人驚艷的東西」可能來自這 | 設立觀察 trigger（什麼條件成熟了再投入） |
| MCP/ACP = AI 時代的 API | 「十年前問要不要 API，今天無人生存」 | 內部系統盡量 MCP 化，不是寫一次性 prompt |
| Claude design 從 RN repo 自己懂 view 做設計 | 模型對 codebase 理解能力比預期強 | 「給 Agent 整個 repo」比「給片段 context」更值得試 |

---

## 4. 持續 Follow 清單

> 不是一次讀完，是長期訂閱觀察。

### 人物
- **Tonyq Wang (tony1223)** — BAT 作者，深度技術觀察、context economics、模型行為觀察。FB / GitHub。
- **高健龍** — Spectra / Sherly Terminal、SDD 推廣、Harness Engineering 實踐者。5xCamp / FB。
- **wchung** — OpenAB / MCP × ACP 的架構思考。https://wchung.tw/blog/

### Repo（追 release / commit）
- https://github.com/tony1223/better-agent-terminal — 看 BAT 怎麼整合 Procfile / 權限分層 / cost 追蹤
- https://github.com/github/spec-kit — SDD 官方 toolkit
- Anthropic 的 claude-agent-sdk — 看「去重量化」會怎麼開出新整合
- https://github.com/DarthSim/overmind — 學 procfile 模式

### 主題訂閱
- Claude Code / Codex 改版（Tony 標記的版本號可當 baseline 對照）
- /rewind、/auto-continue 類「context 管理 slash command」生態
- MCP server 生態（Figma / Jira / DB / Notion / Linear / Slack）
- Skill 設計範式（Anthropic 官方 + Spectra）

### 課程
- DeepLearning.AI **Spec-Driven Development with Coding Agents**（JetBrains × Paul Everitt）
  https://www.deeplearning.ai/short-courses/spec-driven-development-with-coding-agents/
- DeepLearning.AI **Claude Code: A Highly Agentic Coding Assistant**

---

## 5. 對 AgentCodingPM 的具體啟發（先列、後續決策時拿出來對）

1. **Skill 庫優先於多 Agent** — 我們現在 `.github/skills/` 路線正確，不要分心做 orchestration。
2. **風險分層 × 驗證策略** — 把 Tony 的 1:10 人工驗證寫進 L0/L1，但 L2 守住 test/contract test。對應現有 5 大品質門檻表強化。
3. **Plan mode 強制條件** — L1/L2 預設走 plan mode；列入 Backlog → Handoff 的 checklist。
4. **MCP 最小權限模板** — 仿 OpenAB「可讀 X、可寫 Y、禁止其他」的權限契約寫法，對應 Gate B。
5. **開發環境模板** — 評估把 Procfile / overmind / devcontainer 列入「team 開新專案 baseline」。
6. **Context 工程培訓** — 不只教 prompt，教 read/think/write 的 token 帳。`.ai/knowledge/` 加一頁 context economics 經驗法則。
7. **SDD 對照表** — 把現行 `Outcome → OPP → Backlog → PRD` 流程跟 Spectra 的 `discuss → propose → apply → ingest → archive` 對齊一次，找補強點。
8. **觀察 trigger 設定** — 為「multi-agent 何時值得投入」「Claude Agent SDK 整合殺招」「資料自主權對組織」設明確觀察條件，避免漏掉轉折點。

---

## 6. 後續行動建議

- [ ] 把 Pattern A–J 抽成 `.ai/knowledge/agent-coding-patterns.md`（穩定知識，不是 inbox 訊號）
- [ ] 把「持續 Follow 清單」做成可訂閱（GitHub watch / RSS / 社群追蹤）
- [ ] 每月對照 Claude Code / Codex 改版，更新 patterns 是否需要修訂
- [ ] 評估第 5 章 8 條啟發中的優先順序，挑 2–3 條做 OPP

---

## 7. Repo 深入解析

> 把 `tool_experiments/` 下 clone 的 repo 一個個拆開，看「要做到這種工具，需要哪一級的開發者、付出什麼樣的成本」。

### 7.1 Better Agent Terminal（BAT）— tony1223/better-agent-terminal

對應 Pattern：**A（Harness Engineering）+ G（開發環境模板化）+ H（Context 經濟學）**

#### 7.1.1 工具是什麼、目的、做完後的更好狀態

**定位**：跨平台桌面 App（Electron），把「多專案 × 多 terminal × 多 AI agent × git × Procfile × 遠端」全部塞進一個視窗。

**它在解什麼痛**（從 README + PLAN + CLAUDE.md 反推）：

| 痛點 | BAT 的解法 |
|------|-----------|
| 多專案多 terminal 視窗黑洞、切來切去 | Workspace 概念：每個 workspace 綁一個資料夾,內含多個 terminal + 一個 Claude/Codex agent panel |
| Agent 跟 terminal 在兩個程式裡 | 把 `@anthropic-ai/claude-agent-sdk` 直接內嵌成 panel,不用另開 CLI |
| Token/cost/cache 黑盒、cache TTL 過期被全價重算 | 自製 statusline（15 個 item）+ cache history + cache TTL 倒數 + **過期 >150k token 跳預送警告** |
| Plan mode 沒護欄、Agent 改壞主 working tree | **Git worktree 隔離**的 agent preset,agent 在獨立 worktree 跑 |
| 開新專案要起 DB/docker/cron 等多個 stateful process | **Worker panel = Procfile**（致敬 Overmind）整進主視窗 |
| 想從手機/外面操作家裡的 agent | 內建 WebSocket server + Token 認證 + QR Code + Tailscale 自動偵測 + **headless `bat-server`**（VPS 用） |
| Codex 中斷感重,要一直手動「繼續」 | `/auto-continue` slash command,stop 後自動再送 prompt N 次 |
| 多 Claude 帳號要切 | `/switch` + Anthropic OAuth/Chrome session key 雙路 usage 監控 |

**做完後達到的狀態**（用 Tony 自己的話翻譯）：「畫面上每一塊都是我自己掌控的工具,AI 大廠怎麼改版我都能順著吃到,不用被任何單一 IDE/CLI 綁住。」

#### 7.1.2 技術內容

**規模**：約 **37,865 行 TS/TSX**。光 `claude-agent-manager.ts` 就 2593 行、`main.ts` 1387 行、`codex-agent-manager.ts` 1525 行 — 不是 toy project。

**核心 stack**：
- **桌面框架**：Electron 28 + Vite 5 + electron-builder（Win NSIS / Mac DMG notarized / Linux AppImage 三平台簽章）
- **Terminal 層**：`xterm.js` + `node-pty`（CJK/Unicode、PTY 進程生命週期）
- **AI SDK 層**：`@anthropic-ai/claude-agent-sdk` + `@anthropic-ai/claude-code` + `@openai/codex-sdk` + `@modelcontextprotocol/sdk`（MCP）+ `@ai-sdk/openai`
- **儲存**：`better-sqlite3`（snippet、session、profile）
- **遠端通訊**：`ws`（WebSocket）+ `selfsigned`（自簽憑證）+ `qrcode` + Tailscale IP 偵測
- **i18n**：i18next（EN / zh-TW / zh-CN）

**真正關鍵的架構決策**（一般 demo project 不會碰到）：
1. **`pty-manager` 多視窗廣播**：一個 PTY 進程的 output 要同步餵到多個 renderer（detached window + remote client）
2. **Remote handler-registry 統一抽象**：local IPC 跟 WebSocket remote 共用同一組 handler 定義（`electron/remote/`）
3. **Worktree manager 做生命週期管理**：建立、清理、rehydrate（重啟後接回未完成的 worktree）
4. **Cache 經濟學內建到 UI**：cache read/write 拆 5min vs 1h TTL、按 Opus/Sonnet/Haiku 分價 — 對應 Pattern H
5. **`flushSync` 強制 React 同步渲染**：Claude SDK streaming 太快會被 React 18 batching 吃掉 active task UI（CLAUDE.md 有專條註記）
6. **Headless `bat-server`**：把 GUI 拔掉只留 WebSocket server,可以丟到 VPS 跑
7. **Agent SDK 不可靠的 task 訊息**：CLAUDE.md 寫明「Claude Agent SDK 的 `task_started` 不可靠,自己從 `tool_use` block 推」— 用到把官方 SDK 的內部行為摸熟才知道的細節

#### 7.1.3 什麼樣的開發者才能做到這一步

不是「會寫 React」就能到這。要同時具備這幾條：

1. **痛點驅動的 builder 人格** — Pattern A 的本質
   - 自己天天用 Coding Agent 工作,每天累積具體不爽（cache 過期被全價、Codex 停下來、Plan mode 沒護欄）,把不爽變需求
   - 不是「想做個產品」,是「不做這個我自己沒辦法工作」

2. **Electron + Native module 的多平台地獄經驗**
   - `node-pty`/`better-sqlite3` 的 build script、macOS notarization、Windows NSIS、Linux AppImage、`asarUnpack` 路徑問題
   - 從 contributor 列表看出來：**七位 contributor 各自修不同平台的 bug**（macOS spawn ENOENT、Windows zombie process、terminal resize 黑屏…）,意味著 maintainer 要能 review 所有平台的 PR

3. **「能撬開官方 SDK」的逆向能力**
   - Codex CLI 包太死 → 自己寫 codex-shim
   - Claude Agent SDK 的 task message 不可靠 → 自己從 tool_use 重建狀態
   - 需要看得懂官方包的內部行為,不只是看 docs 用 API

4. **系統觀 + IPC 設計**
   - PTY、AI session、git、檔案、Procfile、WebSocket 共處一爐,每個都要持久化、可重啟、可斷線重連
   - 把 local IPC 跟 remote WebSocket 用同一套 handler registry 抽象 — 中型架構師等級的判斷

5. **Token 經濟學的精算意識**
   - 沒有「我要在 statusline 看到 5min/1h cache TTL 倒數」這種具體痛感,根本不會做 cache history × per-model 計價
   - 對應 Pattern H：頂尖玩家會把 read/think/write 的成本納入肌肉記憶

6. **能 hold 住 ~3.8 萬行 TS/TSX 不崩盤**
   - React 18 batching、xterm 渲染、PTY broadcast、worktree 生命週期、多 window registry 同時 in mind
   - CLAUDE.md 還能寫出 "No Regressions Policy" — 暗示已經被 regression 痛過

#### 7.1.4 對 AgentCodingPM 的取捨

> BAT 是「一個天天用 Agent 的資深技術人,把自己每個工作流痛點全部內建成 GUI」的成品。**抄產品意義不大,抄 pattern 才是重點**：把「Procfile 模板化、cache TTL 警告、worktree 隔離 plan mode、cost statusline」這四件事拿來檢視我們自己的 harness — 比再做一個 Electron App 槓桿大得多。

---

### 7.2 Spectra — kaochenlong/spectra-app

對應 Pattern：**A（Harness Engineering）+ C（Skill 庫）+ E（SDD 跨 session context）**

> ⚠️ 注意：這個 repo 是**閉源產品**。`spectra-app` 只放 README、CHANGELOG、releases 資料夾（裝 `.dmg`/`.exe` binary）。下面的技術反推來自產品行為、檔名命名、CHANGELOG 描述，不是源碼級分析。

#### 7.2.1 工具是什麼、目的、做完後的更好狀態

**定位**：把 [OpenSpec](https://github.com/Fission-AI/OpenSpec) 的「Spec-Driven Development（SDD）」CLI 工作流**升級成桌面 GUI + 跨 Agent Skill 標準**。

**起源故事很關鍵**（高健龍自述,翻譯）：
> 「最初是 OpenSpec 的 GUI 前端,但用了一陣子發現 OpenSpec 流程跟我自己開發習慣不太合,有些指令不夠順手。起初想透過修改 Skills 設定檔調整,但光這樣不足以發揮 SDD 的價值,最後決定基於 OpenSpec 的設計把整個流程重寫一次。」

→ 這正是 **Pattern A 的標準範例**：先用既有工具,撞牆,改 config 不夠,**自己重寫**。和 Tony 做 BAT 是同樣性格。

**它在解什麼痛**：

| 痛點 | Spectra 的解法 |
|------|--------------|
| OpenSpec CLI 要手動管 `openspec/` 目錄 + YAML + Markdown | GUI + 看板（Kanban）+ 拖拉變狀態 |
| Brownfield 專案進 SDD,Agent 要問 ~15 回合需求才動工 | `/spectra-discuss` 的**「assumptions mode」**：先掃 codebase 再提問,降到 **2–4 回合** |
| Agent 直接動工,結果發現檔案不在/codebase 已偏移 | `/spectra-apply` 加**Preflight check**：偵測遺失檔案、codebase drift、stale 內容,實作後再做 verification |
| `/spectra-propose` 產出的半成品 spec 留在 changes 目錄污染 git | Validate 完**自動 park**(暫存),不污染 working tree |
| 多 spec 同時進行,git 工作區一團亂 | **Park 機制 + Git worktree**(`spx/<change-name>` branch),每個 change 一個獨立目錄 |
| Spec 多了找不到、跨語言搜不到 | **RAG 向量搜尋**(EN/JP/zh-TW/zh-CN),`/spectra-ask` 自然語言查詢 |
| Slash Commands 越加越多、Skills 又是 Anthropic 新機制,要選一個 | v2.2.5 起**預設只產 Skills,不產 Slash Commands** — 押注 Anthropic Skills 是未來標準 |
| Agent 太愛附和 | `/spectra-discuss` 加**反阿諛 + pushback rules**(v2.2.0) |

**做完後達到的狀態**：「**不用管哪家 Coding Agent、模型怎麼升級,SDD 流程都是同一套。AI 大廠打得越兇,這套流程只會越順。**」(高健龍原話)— Spec 即是跨 session、跨 Agent、跨模型的 context 壓縮層。

#### 7.2.2 技術內容（從產品行為反推）

**桌面框架推測**：很可能是 **Tauri (Rust)**,不是 Electron。理由：
- DMG 檔名 `Spectra_2.2.5_aarch64.dmg` 是 Rust toolchain 命名慣例(BAT 是 `${productName}-${version}-${arch}.dmg`)
- RAG 向量搜尋**需要 Apple Silicon 或 Windows** → 強烈暗示用 native ML inference (ONNX Runtime / candle / 之類),Electron 要做這個會很難
- 體積/啟動速度上的選擇

**核心技術組件**(行為反推)：
1. **Vector store**：嵌入式(可能是 sqlite-vss / LanceDB),v2.2.5 換成「更小、多語言」的 embedding model — 暗示是 self-hosted ONNX model
2. **Skills 寫入機制**：直接寫入 Claude Code 的 `~/.claude/skills/` 或專案 `.claude/skills/`(對應 Pattern C)
3. **Git worktree 整合**:branch 命名 `spx/<change-name>`,自動偵測切換
4. **Agent 偵測**：v2.2.0 修了「把 `.opsx` 目錄誤判為 Claude Code」— 暗示有自己的 Agent 偵測邏輯,可能掃 `CLAUDE.md`/`AGENTS.md`/`GEMINI.md`
5. **跨平台 CLI(`specx`)**：v2.2.5 把 GUI 邏輯拆出 standalone CLI(實驗中)
6. **`.spectra.yaml` 應用層設定** vs OpenSpec 的 `config.yaml` — 把「app 行為」和「資料格式」清楚切開
7. **跨 Agent 支援**：CLAUDE.md / GEMINI.md 自動產生(v2.2.0),官網號稱支援 23 種 Agent

**SDD 流程的真正槓桿在 prompt/skill 設計,不在程式碼**：
- `/spectra-discuss` assumptions mode = 一段 prompt,讓互動回合從 15 → 2-4
- `/spectra-apply` preflight = 一段 prompt + 檔案 lint 規則
- `/spectra-propose` 自動 park = 一段流程協調
- `/spectra-debug` max 3 attempts = 流程護欄

→ **這個工具的 IP 大半在 .md skill 檔案的內容**,不在 Rust/Electron 程式。

#### 7.2.3 什麼樣的開發者才能做到這一步

跟 BAT 是**完全不同的能力組合**。BAT 是技術深度,Spectra 是**流程設計 + 商業判斷**：

1. **流程設計師(這是核心)**
   - 能看出 SDD 流程哪裡會卡住(assumptions mode 把 15 回合縮到 2-4 是觀察 + 設計的成果)
   - 知道 anti-sycophancy 該寫進哪一個 skill(`/spectra-discuss`)而不是全域(全域會破壞其他 skill)
   - 把「park / unpark」這種「不污染 git」的小痛點獨立成機制 — 是流程觀察力

2. **prompt / skill 工程能力**
   - Skills 跟 Slash Commands 的取捨判斷(v2.2.5 押注 Skills 是未來)
   - 知道 brownfield vs greenfield 要不同 prompt 策略(assumptions mode 是 brownfield 專屬)
   - 跨 Agent 抽象(同一個 SDD 流程要在 Claude Code / Codex / Gemini / Cursor 都能跑)

3. **本地 RAG 工程能力**
   - Embedding model 選型(體積 vs 多語言準確度的取捨,v2.2.5 才換成多語言版)
   - 索引同步策略(`spec_dir` 變更要觸發向量同步,v2.2.5 修這個 bug)
   - 跨語言搜尋(EN/JP/zh-TW/zh-CN)— 對 embedding model 的選型有要求

4. **桌面 + CLI 雙形態的工程能力**
   - GUI(Tauri/Electron)+ standalone CLI(`specx`)兩條 distribution 同時維護
   - macOS aarch64 + x64 + Windows x64 三平台簽章打包

5. **商業產品 mindset(跟 BAT 不同的關鍵)**
   - **閉源**(repo 只放 binary,沒源碼)→ 押注是商業產品,不是開源工具
   - 有官網 `spectra.5xcamp.us`、有品牌(5xCamp 是高健龍的訓練營)、有 Homebrew tap
   - Issue template 都做了 → 在準備接社群回饋,不是個人玩具

6. **產品迭代節奏**
   - 從 git log 看：v1.1.5 → v2.2.5 在很短時間內疊代
   - CHANGELOG 雙語 + 結構化 + 有「Experimental」標記 → 有產品管理意識

#### 7.2.4 對 AgentCodingPM 的取捨

> Spectra 跟我們的流程**最像**。我們的 `Outcome → OPP → Backlog → PRD` 對應它的 `discuss → propose → apply → ingest → archive`,而我們的 `.github/skills/` 對應它的 SDD skill 集。

**直接可抄的 pattern**(不是抄程式,是抄流程設計):
1. **Preflight check 機制** — `/spectra-apply` 的「實作前先掃檔案/codebase drift/stale 內容」可以套到我們的 backlog → handoff 階段(Gate C 系統正確性的補強)
2. **Assumptions mode** — 我們做 PRD 寫作時,讓 Agent 先掃 codebase 再問問題,而不是空白問。可加進 `prd-write` skill
3. **Park 機制** — `01_inbox` 跟 `05_product` 之間,可能需要一個「半成品暫存」狀態(現在 inbox 太雜)
4. **Anti-sycophancy 進 skill** — 我們的 `priority-decide`、`stakeholder-align` skill 應該明寫 pushback rules
5. **Skills > Slash Commands 的押注** — 我們的 skill 集走在這個方向是對的(對應 Pattern C)

**不要抄的部分**:
1. 不要做 GUI(我們是 PM Repo,不是工具產品)
2. 不要自己搞 RAG(用現成的 Spectra 或 spec-kit 即可)
3. 不要做閉源產品化(我們的目的是流程,不是工具營收)

**應該觀察的訊號**:
- Spectra 怎麼解 brownfield 的 `assumptions mode` — 我們團隊接手舊 codebase 也有同樣問題
- `specx` standalone CLI 的演化 — 看「GUI 邏輯拆 CLI」對團隊化推廣的價值

---

### 7.3 Agent Skills — addyosmani/agent-skills

對應 Pattern：**C（Skill 庫優先）+ E（SDD）+ J（治理觀的反向應用：Skill 是給模型的「不可偷懶」護欄）**

> 跟前兩個 repo 是**完全不同物種**：不是應用程式、不是桌面工具,是**「資深工程師工作流的 Markdown 化 + 跨 Agent plugin 標準化」**。作者是 Addy Osmani(Google Chrome team、《Learning JavaScript Design Patterns》《Image Optimization》作者)。

#### 7.3.1 工具是什麼、目的、做完後的更好狀態

**定位**：把資深工程師的工作流(spec、plan、build、verify、review、ship 全生命週期)封裝成 **20 個 SKILL.md + 3 個 persona + 7 個 slash command**,做成 **Claude Code Marketplace plugin**,同時相容 Cursor / Gemini / Windsurf / Copilot / Kiro / OpenCode / Codex。

**它在解什麼痛**:

| 痛點 | Agent Skills 的解法 |
|------|-------------------|
| AI agent 預設走最短路徑(跳過 spec、test、security review) | 每個 skill 都有 **anti-rationalization table**(常見偷懶藉口 + 反駁) |
| 個人 prompt 不可移植、不可分享、會 drift | 標準化 SKILL anatomy(6 個 section): Overview / When to Use / Process / Rationalizations / Red Flags / Verification |
| 各 IDE/Agent 各自為政,同一套 skill 要重寫 | 純 Markdown + bash script,搭 9 份 setup guide(每個 IDE 一份)做跨平台抽象 |
| Skill 寫得太抽象 → Agent 看完也不知道要做什麼 | **Process,not prose**: 每個 skill 是「步驟 + checkpoint + exit criteria」,不是參考文件 |
| 「跑完 skill」沒有客觀證據 | **Verification is non-negotiable**: 每個 skill 結尾都要 evidence(tests passing、build output、runtime data) |
| Agent 為了快會省略 sub-step | Skill 有 **Red Flags** 段落明列「這是出問題的徵兆」 |
| Token 用量爆炸 | **Progressive disclosure**: 只載 SKILL.md(<500 行),supporting refs 按需載入,scripts 不算 context |

**做完後達到的狀態**:
> 「**任何 AI agent,只要 Markdown 看得懂,就能下載 Google 工程文化下來執行。**」

每個 skill 都嵌進一條工程經典原則:
- API design → **Hyrum's Law** + **One-Version Rule**
- Testing → **Beyonce Rule** + test pyramid 80/15/5
- Code review → **change sizing ~100 行** + 五軸標準 + Nit/Optional/FYI severity
- Code simplification → **Chesterton's Fence** + Rule of 500
- Git → **trunk-based development** + atomic commit
- CI/CD → **Shift Left** + **Faster is Safer** + feature flags
- Deprecation → **Code as a liability**(主動刪 zombie code)

#### 7.3.2 技術內容

**規模**:
- **20 個 SKILL.md,共 5,962 行**(都是 prompt engineering / process design)
- 3 個 persona(code-reviewer / test-engineer / security-auditor)
- 7 個 slash command(.claude/commands/{spec,plan,build,test,review,code-simplify,ship}.md)
- 4 個 reference checklist(testing / security / performance / accessibility)
- 9 種 setup guide(Claude Code / Cursor / Gemini / Windsurf / Copilot / Kiro / OpenCode / Codex / 其他)
- 8 個 hook script(session-start、SDD cache pre/post、simplify-ignore...)

**核心 stack**:
- 100% **Markdown + bash**(沒有 TS、沒有 Rust)
- **Claude Code plugin 格式**: `plugin.json` + `marketplace.json` 走 Anthropic 官方 plugin marketplace
- **SessionStart hook**: 用 bash 跑 `session-start.sh`(對應 Pattern E 的 context loading)
- **SDD cache hooks**: pre/post pair script(把 spec 結構快取住,不要每次重讀)

**最有設計感的 5 個機制**:

1. **Anti-Rationalization Tables** — 這是這個庫的招牌
   ```
   | Rationalization | Reality |
   | "I'll write tests after the code works" | You won't. Tests after the fact test implementation, not behavior. |
   | "I tested it manually" | Manual testing doesn't persist. Tomorrow's change might break it. |
   ```
   → 直接針對 Agent 最常見的偷懶藉口逐條反駁

2. **Lifecycle Mapping**:七個 slash command 對應軟體生命週期六階段(`/spec` → `/plan` → `/build` → `/test` → `/review` → `/ship`,加 `/code-simplify`)

3. **Personas vs Skills vs Commands 分層**(AGENTS.md 寫得很清楚):
   - **Skills** = 工作流(*how*),mandatory hops
   - **Personas** = 角色 + 觀點(*who*)
   - **Slash commands** = 使用者入口(*when*),orchestration layer
   - 規則: **persona 不能呼叫其他 persona**,只有 user/command 是 orchestrator(避免無限遞迴 + multi-agent 失控,呼應 Pattern C「現階段反對多 Agent」)

4. **唯一允許的 multi-persona**:`/ship` 的 **parallel fan-out + merge** — 同時跑 code-reviewer / security-auditor / test-engineer,最後合併報告。明確拒絕「router persona 決定要呼叫誰」這種 anti-pattern。

5. **Progressive disclosure**:SKILL.md 控制在 500 行內,reference checklist(testing-patterns、security-checklist 等)只在 skill 內部 reference 時才載入 — 對應 Pattern H(Context 經濟學)。

#### 7.3.3 什麼樣的開發者才能做到這一步

跟 BAT 的「技術深度」、Spectra 的「流程設計 + 商業判斷」都不同。Agent Skills 需要的是 **「資深工程教練 + 開源社群運營者 + 暢銷書作者」三合一**:

1. **覆蓋全生命週期的工程判斷力**(這是天花板)
   - 寫一個 `test-driven-development` skill 不難,寫**所有 20 個跨領域 skill 且彼此互不重疊** 才難
   - 知道 `code-review-and-quality` 跟 `code-simplification` 該怎麼切(前者是 merge 前 gate,後者是事後重構)
   - 知道 `incremental-implementation` 跟 `planning-and-task-breakdown` 的邊界(前者是執行紀律,後者是分解策略)

2. **能從工程經典書中提煉成「步驟」的轉譯力**
   - Hyrum's Law、Beyonce Rule、Shift Left 都是抽象原則
   - 把它們變成 Agent 可以一步一步跑的 process,加 verification — 是寫作能力 + 工程經驗的乘積
   - 對標:**《Software Engineering at Google》本身是 600 頁書,Addy 把核心做法塞進 5,962 行 Markdown**

3. **識別 Agent 偷懶模式的觀察力**
   - 「I'll add tests later」「I tested it manually」「This is too simple to test」 — 這些都是 Addy 觀察過上百次 Agent 的真實輸出才寫得出
   - 反駁要寫得**讓 Agent 自己讀完都覺得不能繼續偷懶** — prompt engineering 的最高層次

4. **跨 IDE/Agent 抽象的耐性**
   - 9 份 setup guide(Claude Code / Cursor / Gemini / Windsurf / Copilot / Kiro / OpenCode / Codex)
   - 每個 IDE 的 plugin/skill/rule 機制都不同,要懂每個的差異
   - 這是「社群維護者」的工作,不是個人工具的工作

5. **開源社群運營能力**(從 contributors + commit log 看)
   - 15+ 位 contributor、有 PR review、有 CONTRIBUTING.md、有 issue template
   - commit log 看到大量 `docs(ship)`、`refactor(ship)` 持續打磨,不是發完就停
   - **PR #86 把 `/ship` 改成 parallel fan-out orchestrator** — 這種大手術級重構願意 review 別人 PR 接進來

6. **作者本身的影響力背書**
   - Addy Osmani 是 Google Chrome 團隊、Web 效能社群頂尖人物、暢銷書作者
   - 沒有這個身份,同樣的 5,962 行 Markdown 不會有這麼多 contributor 跟 marketplace 採用
   - 對應 Pattern A 的 meta 觀察:**Harness Engineering 的影響力,part of it 是作者本人的 reputation**

#### 7.3.4 對 AgentCodingPM 的取捨

> 這個庫跟我們**最直接相關**。我們的 `.github/skills/` 對應它的 `skills/`,但結構規範和執行品質落差還很大。

**直接可抄的 pattern**(這次是真的可以抄,因為它就是 spec 不是 product):

1. **採用它的 SKILL anatomy 6 個 section**(Overview / When to Use / Process / **Common Rationalizations** / Red Flags / Verification) — 我們現在的 SKILL.md 沒這麼結構化
2. **每個 skill 加 Anti-Rationalization table** — PM 流程也有偷懶藉口(「這個 OPP 太小不用驗證」「這個風險等級先當 L0 跑跑看」),寫成表逐條反駁
3. **跑 `/spec`、`/plan`、`/build`、`/test`、`/review`、`/ship` 跟我們的 skill 對標** — 看我們缺哪些 phase
4. **Progressive disclosure 規則**:SKILL.md < 500 行,reference 拆出去 — 我們有些 SKILL 已經太長
5. **Personas vs Skills vs Commands 分層原則**:特別是「persona 不能呼叫 persona,只有 command/user 能 orchestrate」 — 我們的 skill index 應該明寫這條

**評估直接引入(plugin install)**:
```
/plugin marketplace add addyosmani/agent-skills
/plugin install agent-skills@addy-agent-skills
```
- ✅ 20 個工程 skill 直接拿來用,**自己只要寫 PM 專屬 skill**(triage-inbox、move-inbox-to-opp、portfolio-report 那些)
- ⚠️ 英文(我們團隊主語繁中) → 要評估翻譯成本 vs 直接英文
- ⚠️ 它的 anti-rationalization 是針對「工程師偷懶」,我們的痛點是「PM 流程偷懶」,要自己寫

**不應該抄的**:
- 不要做 plugin marketplace(我們是 PM Repo,不是 skill 發行商)
- 不要學 Addy 那麼大規模(20 skill × 6 section × 5 anti-rationalization = 維護成本爆表) — 我們挑前 8 個關鍵 skill 強化即可

**應該觀察的訊號**:
- Anthropic Plugin Marketplace 接下來會出現多少類似的 skill pack — 這會決定我們是「自己寫」還是「裝 plugin + 改」
- Addy 的下一個 commit 趨勢(看他怎麼演進 skill 內容,當作我們的 best practice baseline)

---

### 7.X Repo 解析總結對照表

| 維度 | BAT | Spectra | Agent Skills |
|------|-----|---------|-------------|
| **產品形態** | 開源 GUI App(Electron) | 閉源商業 GUI App(推測 Tauri) | 開源 Markdown Plugin |
| **規模** | ~38k 行 TS/TSX | binary only | ~6k 行 Markdown |
| **核心 IP 在哪** | 程式架構(PTY/IPC/Worktree) | prompt/skill 設計(assumptions mode) | SKILL anatomy + anti-rationalization |
| **作者特質** | 痛點驅動 builder + 多平台地獄 | 流程設計師 + RAG 工程師 + 商業 mindset | 工程教練 + 寫作能力 + 社群運營 |
| **開發門檻** | 系統工程師(中型架構)| 全棧 + 工程觀 + 商業判斷 | 全生命週期工程經驗 + 影響力 |
| **抄的方式** | 抄 pattern(cost statusline/worktree/Procfile) | 抄流程(preflight/assumptions/park) | 抄結構規範 + 直接 plugin install |
| **對應 Pattern** | A + G + H | A + C + E | C + E + J(反向) |
| **對 AgentCodingPM 槓桿** | 中(整合進現有 harness 思路) | 高(流程最像) | **最高**(可直接 install) |
