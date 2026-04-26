---
title: AgentCoding 進階 Pattern（社群觀察萃取）
status: stable
last_review: 2026-04-26
sources:
  - 02_精選文章/20260425_AgengCoding進階用法.md
  - 02_精選文章/20260425_AgentCoding進階用法_pattern整理.md
tldr: 從 Tony Wang / 高健龍 / wchung 等頂尖玩家的實踐萃取出 10 個跨案例 Pattern（A-J），重點是「Harness Engineering」、「CLI > IDE/Web」、「單 Agent + Skill > 多 Agent」、「Plan mode 是底線」、「Context 經濟學」、「治理觀：模型還在童年給空間探索」。
---

# AgentCoding 進階 Pattern

## TL;DR

10 個 Pattern，工具會換、這些不會：

| Pattern | 一句話 |
|---------|-------|
| **A** Harness Engineering | 頂尖玩家自己做工具，做的是「讓 Agent 穩定產出的生產線」 |
| **B** CLI > IDE / Web | Claude Code 翻倍，Cursor 類 15-30%，因為 input 品質差距 |
| **C** 單 Agent + Skill > 多 Agent | 多 Agent 切 context = 變笨；Skill 才是真槓桿 |
| **D** Plan Mode 是底線 | 沒 plan mode 不要用；模型自我驗證能力是新觀察 |
| **E** Spec-Driven Development | Spec 是「跨 session/Agent/模型的 context 壓縮層」 |
| **F** MCP 是隔離層 | 「AI generates. Humans authorize.」最小權限 |
| **G** 開發環境是隱形瓶頸 | Procfile/devcontainer 跟 prompt/skill 一樣重要 |
| **H** Context 經濟學 | read/think/write 都吃 token，要學 context 工程 |
| **I** 1:10 人工驗證 | 設計過的關鍵場景驗證 > test case 海戰 |
| **J** 治理觀 | 模型還在童年，過早治理會錯過模型能告訴你的事 |

---

## 為什麼這篇是進階參考

如果你只讀到 `01` ~ `05`，會以為 AgentCoding 就是「按流程跑 PR」。但社群頂尖玩家在做的事比這深得多，他們在思考 context 經濟、工具鏈、模型治理。這篇是把他們的實踐萃取成 10 個可學習的 Pattern。

讀完這篇你才會知道：
- 為什麼自己做 harness 比追新模型重要
- 為什麼 multi-agent 現在不該追
- 為什麼 Procfile / overmind 會被「相見恨晚」
- 為什麼 Tony 敢全面放棄 test case

---

## Pattern A — Harness Engineering：頂尖玩家自己做工具

**訊號**：Tony Wang 做 BAT (Better Agent Terminal)、高健龍做 Spectra + Sherly、wchung 做 OpenAB。三個人不約而同把力氣花在「讓 Agent 穩定產出的生產線」，而不只是用 Agent。

**底層信念**（高健龍原話）：「**不用管哪家 Coding Agent，也不用管模型怎麼升級。AI 大廠打得越兇，這套流程只會越順。**」

**對 AgentCodingPM 的意涵**：
- 不只是「用 Claude Code 寫 code」，而是「設計 harness 讓全團隊用 Agent 產出可驗證成果」
- 我們的 `.github/skills/` + `AGENTS.md` + `00_context/` 本身就是 harness
- Harness 的影響力部分來自作者的 reputation —— Addy Osmani 寫的同一份 SKILL.md 跟普通開發者寫的傳播力差很多

---

## Pattern B — CLI Agent > IDE / Web Agent

**訊號**：Tony 估 Cursor 類提升 15–30%，Claude Code 類 CLI Agent **翻倍**（保守 100%）。

**為什麼**：
- **CLI 能讀檔、跑指令、看錯誤、改程式、跑測試** —— 直接接真實工作環境
- Web 要人肉貼資訊（input 品質差）
- IDE（如 Cursor）為了省 token 會壓縮 context（副作用大）

**例外/邊界**：DBeaver plugin 反例 —— 在「封閉 DB 連線環境」這種特殊場景，IDE plugin + 內部 MCP 反而乾淨。

**對 AgentCodingPM 的意涵**：
- 團隊推廣 Agent Coding 時，**CLI Agent 是主軸**
- IDE 整合只在「特殊封閉領域」才優先
- 不要花太多力氣推 Cursor 給整個團隊，不如教大家用好 Claude Code / Codex

---

## Pattern C — 單 Agent + Skill + Context 管理 ＞ 多 Agent

**訊號**：
- Tony 明確反對現階段多 Agent
- 高健龍把 Skill 做進 Spectra
- Anthropic 推 Skill 機制
- agent-skills 的設計規則：**persona 不能呼叫其他 persona**，只有 user/command 是 orchestrator

**為什麼**：
- 多 Agent 切 context = 變笨
- 綜合判斷類任務切碎反而失去整體理解
- token 控制更難
- Tony：「現階段多 Agent 控制起來非常累而且不好掌握 token」

**對策**：
- 把人類經驗濃縮成 Skill，讓單一 Agent 在需要時調用
- 要平行就**開多個 Claude Code session**，不要塞進「多 Agent 系統」

**對 AgentCodingPM 的意涵**：
- `.github/skills/` 路線是對的
- 不要急著做 multi-agent orchestration
- 觀察 trigger：multi-agent 何時值得投入？等模型能 reliably 切 context 不變笨時

---

## Pattern D — Plan Mode 是底線，自我驗證是新能力

**訊號**：
- Tony：「沒 plan mode 我已經不會用了」
- Codex 的 plan mode 比 Claude Code 聽話
- GPT 5.5 自己會切實驗、保留成功條件、躲坑

**模型行為新觀察**：「他自己具有某種疊代性思考，會去想失敗怎麼切、成功怎麼保留，把環境設計成具實驗條件的環境」—— 這在取代很多以前要靠 flow 引導的工作。

**對 AgentCodingPM 的意涵**：
- 風險分層的 L1/L2 應該**強制走 plan mode**；L0 可放開
- 開始觀察「模型自我驗證能力」何時夠強，能省掉哪些 flow 步驟
- 升級你的 prompt 習慣：固定要求「請先進入 planning 模式，不修改任何檔案，先提出 plan / 風險 / 驗收方式，等我確認後再實作」

---

## Pattern E — Spec-Driven Development 是 context 跨 session 的解法

**訊號**：
- Spectra 流程 = `discuss → propose → apply → ingest → archive`
- DeepLearning.AI / JetBrains 的 SDD 課強調 project constitution + feature spec 跨 session 保 context、降低 cognitive debt
- GitHub spec-kit 是 SDD 官方 toolkit

**核心點**：spec 不是文件潔癖，是**「讓下一個 session 的 Agent 知道前面為什麼這樣做」的 context 壓縮機制**。

**對 AgentCodingPM 的意涵**：
- 我們的 `00_context/` + `05_product/Outcome → OPP → Backlog → PRD` 本來就是 SDD 雛形
- 可以直接對照 spec-kit / Spectra 的成熟做法強化
- 我們缺的是 Spectra 的 `assumptions mode`（brownfield 專案進 SDD 時，先掃 codebase 再提問，把 15 回合縮到 2-4 回合）

---

## Pattern F — MCP 是隔離層，不是「給 AI 全權」

**訊號**：
- OpenClaw（給 AI 完整 file/shell 權限）→ OpenAB（MCP 限定「可讀 Figma、可寫 Jira、禁止其他」）
- Tony 的 DBeaver plugin 也是「MCP 做隔離層比較乾淨」

**口號**：**「AI generates. Humans authorize.」**

**對 AgentCodingPM 的意涵**：
- 對應五大品質門檻 **Gate B（Agent 工具安全 / 最小權限）**
- MCP 應該優先用在「跨系統授權」場景，不是隨便加
- 設計 MCP server 時學 OpenAB 的權限契約：「可讀 X、可寫 Y、禁止其他」明文化

---

## Pattern G — 開發環境本身是 Agent Coding 的隱形瓶頸

**訊號**：Tony 對 Procfile 「相見恨晚」，把 overmind 整進 BAT。

**背景**：每開新專案 setup 都要弄 DB、docker、TLS、cron、cloud sql… 多 terminal 黑洞。tmux named session 解一半但要背 key（「為什麼要花腦袋寶貴的 memory buffer 處理他」）。

**Pattern**：把「啟動環境的雜事」也納入 Agent 管理範圍 —— 一個畫面（Procfile + AI 寫指令）解決所有 stateful process。

Tony：「最近用 procfile，他可以把多個 process merge 在一起，以 cli 的形式，可以局部重啟，可以界面整合。我已經三四年沒有看到過這種相見恨晚的東西了。大概可以跟 git, di 工具並列吧。」

**對 AgentCodingPM 的意涵**：
- 團隊推廣 Agent Coding 時，**開發環境模板化（Procfile / devcontainer）跟 prompt/skill 一樣重要**
- 否則 onboarding 永遠卡在環境問題

---

## Pattern H — Context 經濟學：read / think / write 都吃 token

**訊號**：
- Tony：「context 戰爭迷霧的管理」
- thinking 多會壓縮其他資訊
- 5m vs 1h cache write 各有取捨
- cache hit/miss 線性影響成本
- 休眠 session 喚醒會整批 write

**思考框架**：
- **Context 管理 = 把問題投影到低維度 + DP 快取已解過的子問題**
- 投影方法暗示「你預設的答案」—— 預設錯，後面全錯（駕駛重要）
- 競爭未來 3 軸：context 大小、token 效率、thinking 黃金比例

**Tony 原話**：「context managing 本質是戰爭迷霧的管理。所以要先把所有問題投影成一個可以觀測的維度（維度越低解的越快），然後把解過的問題紀錄，採用 DP 的邏輯快取已經有過得答案再往前進。但是最可怕的其實不是這種問題，是不存在答案的問題，沒有答案的話任何探索都兼具有效跟不有效的特性。而投影方法的選擇本身則直接暗示了你發問方所預設的答案，如果這個預設錯誤，最後答案會全錯。這也是為什麼駕駛重要，確認大方向的正確從來沒有比過往更重要過了。」

**對 AgentCodingPM 的意涵**：
- 團隊要學的不是「prompt 工程」，是 **「context 工程」**
- 每個 read / think / write 都是成本 —— 教大家算這筆帳
- 要在 `00_context/` 加一份 `context-economics-heuristics.md`（拆出來放 `09-外部資源` 或 `08-Skill與Harness工程`）

---

## Pattern I — 1:10 人工驗證取代 test case

**訊號**：Tony 「全面放棄 test case，整合測試佔 0.1%；讓 Agent cook 10 小時，自己用 1 小時積極驗證關鍵場景；場景是設計過的」。

**Tony 原話**：「我在這個階段基本上是全面放棄 test case，只有在非常窄的條件我才寫 integration test，而且比例大概佔不到總產出量的 0.1%。相對的我投入大量的人類人工，去跟他打對台做關鍵場景驗證。當然這個場景驗證是經過設計的。也就是我讓他 cook 可能十個小時累積成我需要花一個小時積極的去測試跟驗證，而且這個驗證可以很有效率的確保他這十個小時是正常產出的。」

**先決條件**：
- 模型本身有規劃 + 自我驗證能力
- 驗證者懂全流程能設計關鍵場景

**警告/邊界**：這對應 Tony 個人 / 高熟練度 / 探索型專案。**對團隊推廣不是 1:1 套用**：
- L2（跨服務契約、Schema migration）絕對需要 test/contract test
- L0/L1 才能逐步放掉

**對 AgentCodingPM 的意涵**：
- 把這條寫成「**驗證策略矩陣**」（L0/L1/L2 × test 覆蓋深度），不要一刀切
- 對 PM 流程的類比：OPP 驗證也可以「設計關鍵 experiment」取代「全面 user research」

---

## Pattern J — 治理觀：模型還在童年，給空間探索

**訊號**：Tony 「你會在意小朋友五歲前的治理秩序嗎？只要安全沒問題，剩下就讓他自由探索，告訴我們他世界長什麼樣，我們順著他探索的決定允許或禁止。」

**邏輯**：Sonnet 4 / Opus 4.5 / 4.6 / 4.7 都是躍進中，過早治理會錯過模型能告訴你的事。

**配套**：但要 git 等存檔工具當安全網 + 高品質人類驗證。

**對 AgentCodingPM 的意涵**：
- L0/L1 給更多空間探索；只在 L2 +「不可逆/敏感資料/跨契約」處嚴管
- **治理不等於普遍管制**
- 這跟 `05-風險分層與五大品質門檻.md` 的「按風險分流」是同一個哲學

---

## 趨勢觀察（多人共指）

| 趨勢 | 觀察點 | 意涵 |
|------|--------|------|
| Claude Agent SDK 持續去重量化 | 拔 cli.js 依賴、native binary、可換 cli path | 預期「整合 Agent SDK 的殺招」服務即將出現 —— 提早觀察整合機會 |
| 企業自建系統取代 SaaS 分潤 | 資料自主權變核心競爭力 | 我們做的內部 harness 本身是資產 |
| Coding Agent 競爭 = context × token 效率 × thinking 比例 | 不是模型變大就好 | 培訓重點：context 工程、cost awareness |
| Multi-Agent 還太早 | 但「下一個讓人驚艷的東西」可能來自這 | 設立觀察 trigger（什麼條件成熟了再投入） |
| MCP/ACP = AI 時代的 API | 「十年前問要不要 API，今天無人生存」 | 內部系統盡量 MCP 化，不是寫一次性 prompt |
| Claude design 從 RN repo 自己懂 view 做設計 | 模型對 codebase 理解能力比預期強 | 「給 Agent 整個 repo」比「給片段 context」更值得試 |

---

## 進階使用者的核心心智模型

整合 Tony 與其他人的觀察，進階使用者不只是會寫 prompt，他們在做的事是：

```
不是：我 → Claude Code / Codex CLI → Agent 幫我改程式

而是：我 → 自己的 Agent 工作台
       → 控制 workspace / terminal / git / file / plan / permission / context
       → 再去驅動 Claude Code / Codex / terminal
```

也就是說：
> **進階使用 Coding Agent，不是一直換更強的模型，而是讓 Agent 的工作流程變得可管理。**

進階使用者會看：
- 這次用了多少 context？快滿了嗎？
- 成本多少？cache 有沒有命中？
- session 能不能 resume？
- 這次改了哪些檔案？有沒有可以 fork 出另一條路線？

這已經接近「Agent operating system」的思維。

---

## 9 個可以馬上學的進階習慣

不需要從頭做 BAT，但可以馬上升級工作流：

1. **建立自己的 Agent 使用規則** —— 在 repo 放 `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` / `.github/copilot-instructions.md`
2. **改 prompt 為 plan-first** —— 「請先讀 repo，不修改檔案，先提出 plan / 相關檔案 / 風險 / 驗收方式，等我確認後再實作」
3. **重要任務用 branch / worktree 隔離** —— 每個 Agent 任務開一個 branch，做完先看 diff 再決定 merge
4. **記錄 Agent 任務結果** —— 任務目標、用了什麼工具、改了哪些檔案、遇到什麼問題、哪些 prompt 有效、哪些地方失控
5. **Plan mode 變預設** —— L1/L2 任務強制 plan mode
6. **Context 管理刻意化** —— 不貼 500 行 test output，只貼關鍵 stack trace
7. **Skill 比 prompt 重要** —— 把重複的 prompt 沉澱成 skill 檔
8. **Procfile / devcontainer 模板化** —— 開發環境別讓人手動 setup
9. **看 cost / cache TTL** —— 讓 token 經濟感成為肌肉記憶

---

## 對 AgentCodingPM 的具體啟發

1. **Skill 庫優先於多 Agent** —— `.github/skills/` 路線正確
2. **風險分層 × 驗證策略** —— Tony 的 1:10 寫進 L0/L1，但 L2 守住 test/contract test（對應 `05-`）
3. **Plan mode 強制條件** —— L1/L2 預設走 plan mode，列入 Backlog → Handoff checklist
4. **MCP 最小權限模板** —— 仿 OpenAB「可讀 X、可寫 Y、禁止其他」對應 Gate B
5. **開發環境模板** —— Procfile / overmind / devcontainer 列入「team 開新專案 baseline」
6. **Context 工程培訓** —— 不只教 prompt，教 read/think/write 的 token 帳
7. **SDD 對照表** —— 把 `Outcome → OPP → Backlog → PRD` 跟 Spectra 的 `discuss → propose → apply → ingest → archive` 對齊
8. **觀察 trigger 設定** —— 為「multi-agent 何時值得投入」「Claude Agent SDK 整合殺招」「資料自主權對組織」設明確觀察條件

## 與其他主題的連結

- 工具選擇細節 → [`07-工具地圖與防線設計.md`](./07-工具地圖與防線設計.md)
- Skill 設計範式 → [`08-Skill與Harness工程.md`](./08-Skill與Harness工程.md)
- 風險分層怎麼跟 Pattern J 治理觀對應 → [`05-風險分層與五大品質門檻.md`](./05-風險分層與五大品質門檻.md)
- 持續 follow 的人物與 Repo → [`09-外部資源與閱讀清單.md`](./09-外部資源與閱讀清單.md)
