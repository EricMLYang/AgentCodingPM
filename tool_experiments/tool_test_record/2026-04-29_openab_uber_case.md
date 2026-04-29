# OpenAB 多 Agent 案例研讀筆記：

目的：用工程師比較容易理解的方式，重寫「OpenAB + Discord + 多個 Coding Agent」的運作方式。
對象：對 Agent 不算熟，但懂基本程式、CLI、API、process 概念的工程師。
核心問題：OpenAB 到底是什麼？它怎麼讓多個 Coding Agent 在 Discord 裡協作？

素材來源：
- 原文：[Waiting for Uber: Four AI Agents Planned My Feature](https://wchung.tw/blog/waiting-for-uber-four-ai-agents-planned-my-feature)
- repo：`tool_experiments/openab`（README、`docs/`、`config.toml.example`）

已驗證事實清單（避免本筆記寫錯，往下讀時可以放心）：
- ACP 全名是 Agent Client Protocol（OpenAB README 第一段，連到 `anthropics/agent-protocol`）
- 原文五個 agent 的角色與後端：
  - 梅長蘇（Architect）→ `claude-agent-acp` → 雲端 Claude
  - 簡晨（Backend）→ `opencode acp` → 本地 Ollama，模型 `qwen3.5:35b`
  - 言豫津（Frontend）→ `opencode acp` → 本地 Ollama，模型 `devstral:24b`
  - 蒙摯（Ops）→ `opencode acp` → 本地 Ollama，`qwen3.5:35b`
  - 夏冬（QA）→ `opencode acp` → 本地 Ollama，`devstral:24b`
- 原始指令：`@梅長蘇 老闆想要一個 OAuth2 社群登入功能，支援 Google 和 GitHub，下週 demo 要用`
- 專案 repo：`pangcah-accounting-system`，含 `backend-django/`、`frontend-react/`
- 原文沒有提到用 K8s 跑 OpenAB（Railway / Vercel 是該產品本身的部署，不是 agent 基礎建設）
- 原文有提到 OpenClaw（在「上一篇」），沒有提到 Hermes Agent（後者是 Eric 的延伸比較）

---

## 1. 先講結論：OpenAB 是什麼？

OpenAB 不是 LLM，也不是另一個 Claude Code。

更精準地說：OpenAB 是一個用來 harness Coding Agent CLI 的橋接程式。

它把聊天平台（Discord / Slack / Telegram / HTTP API）上的訊息，轉給後面的 Coding Agent CLI，再把 Coding Agent 的回覆送回聊天平台。

OpenAB 的位置：

```
人
↓
Discord / Slack
↓
OpenAB
↓
Coding Agent CLI
↓
LLM / 本地模型 / 工具 / Repo / MCP / Skills
```

OpenAB 自己通常不直接串 LLM API。 真正呼叫 Claude、OpenAI、Gemini、Ollama 的，是後面的 Coding Agent CLI。例如：

- `OpenAB → Claude Code（claude-agent-acp）→ Claude cloud`
- `OpenAB → OpenCode → Ollama / local model`
- `OpenAB → Codex adapter → OpenAI / Codex model`
- `OpenAB → Gemini CLI → Gemini model`

> 記住一句話：OpenAB harness 的對象不是 LLM，而是 Coding Agent CLI。

---

## 2. 這個案例的大架構

原文的多 Agent 案例可以理解成：

```
使用者
↓
Discord channel / thread
↓
OpenAB bot process
↓
ACP-compatible Coding Agent CLI
↓
LLM + tools + working_dir
```

每個角色都有自己的：
- Discord bot 身分（獨立 token）
- OpenAB 設定（獨立 config.toml）
- Coding Agent CLI（claude-agent-acp 或 opencode acp）
- working_dir（獨立目錄）
- 模型來源（雲端或本地）
- 工具權限

原文五個角色的具體配置：

- 梅長蘇（Architect）：用 Claude（雲端），`working_dir` 對到整個 `pangcah-accounting-system`，負責掃 codebase、列缺口、分派任務
- 簡晨（Backend）：用 OpenCode + Ollama `qwen3.5:35b`（23GB 模型），`working_dir` 鎖在 `backend-django/`
- 言豫津（Frontend）：用 OpenCode + Ollama `devstral:24b`（14GB 模型），`working_dir` 鎖在 `frontend-react/`
- 蒙摯（Ops）：用 OpenCode + Ollama `qwen3.5:35b`，看環境配置與部署相關目錄
- 夏冬（QA）：用 OpenCode + Ollama `devstral:24b`，做整體驗收與測試補強

這個案例最重要的設計，不是「多個 AI 一起聊天」，而是：

> 用 working_dir 限制每個 agent 的視野，讓角色分工變得具體。

原文的精華句：

> 「你不需要教 AI『你是前端專家』，你只要把它指向前端的資料夾。」

也就是說，不只是 prompt 說「你是前端工程師」，而是讓它真的主要只能看到前端相關資料夾。

---

## 3. 一個任務如何被分派？

實際流程：

1. 使用者在 Discord 對統籌角色下指令
   - 例如：`@梅長蘇 老闆想要一個 OAuth2 社群登入功能，支援 Google 和 GitHub，下週 demo 要用`
2. Discord 把訊息事件送給對應 bot
3. OpenAB 收到訊息，開始判斷
   - 是否在允許的 channel？
   - 是否有 @ 到這個 bot？
   - 這個 thread 對應哪個 session？
   - 是否要啟動或復用 agent process？
4. OpenAB 透過 ACP 把訊息交給 Coding Agent CLI（這時 `claude-agent-acp` 子進程被 spawn）
5. 統籌角色分析後，在 Discord 回覆規劃
   - 例如：`@簡晨 請實作 SocialLoginView`、`@言豫津 請改登入頁`、`@蒙摯 補 OAuth env`、`@夏冬 補測試`
6. 被 @ 的其他 agent 收到 Discord 訊息事件
7. 各自的 OpenAB process 把任務轉給各自的 Coding Agent CLI
8. 各 agent 在自己的 working_dir 內工作
9. 結果回到 OpenAB，再貼回 Discord

要注意：

> Discord 是人與 agent 的協作介面，也提供 message event。真正判斷要不要觸發 agent 的，是 bot / OpenAB 的設定與邏輯。

如果要允許 bot-to-bot 協作，通常要設定成「只有被明確 @mention 時才處理」（OpenAB 的 `allow_bot_messages = "mentions"`），避免 agent 彼此無限對話。

---

## 4. 名詞解釋

### OpenAB

OpenAB 是聊天平台與 Coding Agent CLI 之間的橋接程式。

它負責接收 Discord / Slack / HTTP API 的訊息、管理 session、啟動或連接 agent backend，然後把回應送回聊天平台。

它不是模型本身，也不是負責推理的 agent brain。

### Coding Agent CLI

Coding Agent CLI 是可以在命令列裡工作的 AI coding agent。例如：

- Claude Code
- OpenCode
- Gemini CLI
- Codex adapter
- Cursor agent

它通常具備：讀 repo、理解程式碼、修改檔案、執行指令、呼叫工具、產生 diff。

OpenAB 主要是把聊天平台的訊息送給這些 Coding Agent CLI。

### Harness

Harness 在這裡不是指直接包住 LLM API 的 prompt / memory / tool 系統。

在 OpenAB 這裡，harness 比較接近：用一個外層程式去啟動、控制、連接、管理 Coding Agent CLI。

所以 OpenAB 是 agent / CLI harness，不是典型 LLM harness。

### ACP（Agent Client Protocol）

ACP 是 Anthropic 帶頭定的協定，讓外部 client 與 Coding Agent 溝通。

在這個案例裡可以理解成：OpenAB 用 ACP 跟支援 ACP 的 Coding Agent CLI 溝通。

它解決的問題是：不同 Coding Agent 各自有不同的輸入輸出格式，OpenAB 要接每一種都會很麻煩。ACP 提供一套一致的溝通方式，讓 OpenAB 可以用類似方式控制不同 agent。

注意：不是所有 Coding Agent 都原生支援 ACP。有些是原生支援（Kiro、Gemini、OpenCode、Cursor、Copilot CLI），有些是透過 adapter 支援（Claude Code 透過 `claude-agent-acp`，Codex 透過 `codex-acp`）。

### JSON-RPC

JSON-RPC 是一種用 JSON 表達 request / response 的格式。

在 OpenAB 案例中，OpenAB 跟 Coding Agent CLI 子進程之間就是用 JSON-RPC 溝通：

```json
{
  "jsonrpc": "2.0",
  "method": "session/prompt",
  "params": {
    "text": "請幫我分析登入功能"
  },
  "id": 1
}
```

JSON-RPC 不是 agent 本身，它只是訊息格式。

可以這樣分層理解：

- ACP：規定 agent client 與 agent 怎麼互動（信的內容規範）
- JSON-RPC：ACP 底層使用的 request / response 格式（信封格式）
- stdio：這些訊息實際傳輸的本機管道（郵差）

### stdin / stdout

stdin / stdout 是 process 的標準輸入與標準輸出。

OpenAB 跟 agent 的對話流程：

```
OpenAB 啟動一個 Coding Agent CLI 子進程
↓
OpenAB 把 JSON-RPC 訊息寫進 agent 的 stdin
↓
Agent 把結果寫到 stdout
↓
OpenAB 讀 stdout，再轉回 Discord
```

所以這不是 HTTP API，也不是網路通訊，而是父進程 OpenAB 用 stdin/stdout 跟子進程 agent CLI 溝通。

### Discord bot token

Discord bot token 是 Discord 給某個 bot 應用程式的憑證。

OpenAB 需要這個 token，才能用該 bot 的身分連上 Discord、收訊息、發訊息。

它不是你的個人密碼，但要像密碼一樣保護。外流後，別人可能可以控制你的 bot。

申請完全免費，一個 Discord 開發者帳號可以建很多個 bot——這就是文章作者一次跑 5 個角色的基礎。

### Ollama

Ollama 是在本機或內部 server 上跑 LLM 的工具。

在這個案例中，它讓 4 個 agent（簡晨、言豫津、蒙摯、夏冬）使用本地模型（qwen3.5:35b、devstral:24b），不必每次都呼叫雲端模型 API。

要注意：

- 模型下載需要網路（之後可離線跑）
- Discord 協作仍然需要連 Discord
- 本地推論會吃 CPU / GPU / 記憶體
- 多個 agent 同時打 Ollama，瓶頸會在模型推論資源

### working_dir

working_dir 是 agent 工作時看到與操作的主要目錄。

這是 OpenAB 案例中最關鍵的設計：

- Backend agent → `working_dir = backend-django/`
- Frontend agent → `working_dir = frontend-react/`
- Architect agent → `working_dir = whole repo`

它的作用是限制 agent 的上下文與操作範圍，比單純在 prompt 裡說「你是 Backend 工程師」更具體。

### MCP（Model Context Protocol）

ACP 和 MCP 要分開看：

- ACP：外部 client 怎麼控制 agent
- MCP：agent 怎麼使用外部工具或資料源

完整鏈路：

```
Discord
↓
OpenAB
↓ ACP
Claude Code
↓ MCP
GitHub / DB / Browser / internal tools
```

ACP 是「控制 agent」的協定，MCP 是「agent 使用工具」的協定。

### Skills

Skills 可以理解成 agent 可重複使用的能力包，內含：

- 特定任務說明
- prompt 規則
- script
- 工具使用方式
- 輸出格式
- 專案慣例

在大架構裡，Skills 通常在 Coding Agent 內部或 repo 內部被使用，不是 OpenAB 自己的核心能力。

---

## 5. OpenAB 會做什麼？

- 連接 Discord / Slack / HTTP API
- 接收訊息事件
- 判斷是否被 @mention
- 管理 thread 與 session 的對應
- 啟動或連接 Coding Agent CLI
- 透過 ACP 與 agent 溝通
- 把 agent 的串流回覆送回聊天平台（每 1.5 秒 edit 一次訊息）
- 維持多輪對話狀態
- 支援多個 bot / 多個 agent 設定
- 搭配 working_dir 做角色分工
- 內建 cron 排程功能（cronjob.toml 可熱更新）
- 部署時搭配 Docker / K8s / Helm / PVC 管理環境與狀態

---

## 6. OpenAB 不會做什麼？

- 自己直接扮演 LLM brain
- 自己完成程式碼理解與修改
- 自己直接呼叫 Claude / OpenAI / Gemini API 作為主要推理層
- 自己決定複雜任務怎麼拆解
- 自己取代 Claude Code / OpenCode / Codex
- 自己提供完整的 autonomous agent framework
- 自己處理所有工具權限與安全治理

真正的思考、寫程式、跑工具，主要由後面的 Coding Agent CLI 負責。

OpenAB 比較像：聊天入口 + agent process 管理 + session bridge + ACP 轉接層。

---

## 7. 跟 Hermes Agent、OpenClaw 的精要比較

> 註：原文有提到 OpenClaw（作者「上一篇」用過）。Hermes Agent 不是原文內容，是 Eric 自己對 `tool_experiments/` 內其他 repo 的延伸比較。

OpenAB、Hermes Agent、OpenClaw 不是同一類東西。

OpenAB 比較像 Chat platform → Coding Agent CLI 的橋。重點是讓人可以在 Discord / Slack 裡操作不同 Coding Agent。

Hermes Agent / OpenClaw 比較像 agent framework / autonomous coding agent system。

重點是 agent 自己如何規劃、執行、反思、使用工具、改善任務流程。

最簡單的差別：

- OpenAB：讓現有 Coding Agent 進入聊天平台協作 → 偏「整合層」
- Hermes / OpenClaw：嘗試打造更完整的 agent 執行框架 → 偏「agent 本體」

選擇邏輯：

如果公司只是想先測試「工程師在 Discord 裡 @不同 coding agent 協作」→ OpenAB 比較直接。

如果公司想研究「agent 如何自動規劃、執行、反省、長任務自治」→ 才比較接近 Hermes / OpenClaw 的方向。

---

## 8. 用一則訊息傳遞看細節

假設使用者在 Discord 輸入：`@簡晨 請幫我檢查 OAuth2 callback API`

實際傳遞拆成五段。

### 第一段：APP 網路通訊

```
使用者手機 / 電腦上的 Discord App
↓
Discord server
↓
bot 收到 message event
```

這一段是一般網路通訊。Discord 負責把訊息事件送給有權限的 bot。

### 第二段：Discord event 到 OpenAB

```
Discord message event
↓
OpenAB bot process
```

OpenAB 收到後會判斷：

- 這是不是允許的 channel？
- 有沒有 @ 到我？
- 這是不是 thread？
- 這個 thread 有沒有既有 session？
- 是否允許 bot message？（決定要不要吃其他 agent 的 @mention）

如果條件符合，OpenAB 就準備把任務交給後面的 Coding Agent。

### 第三段：OpenAB 到 Coding Agent

```
OpenAB
↓ ACP（規範）
Coding Agent CLI
```

ACP 定義 OpenAB 要怎麼跟 agent 溝通。底層常見形式是 JSON-RPC over stdin/stdout：

- OpenAB 把 JSON-RPC 訊息寫到 `agent.stdin`
- agent 把 JSON-RPC 回應寫到 `stdout`
- OpenAB 讀 stdout

### 第四段：Coding Agent 到模型與工具

```
Coding Agent CLI
↓
LLM / local model / tools / repo / MCP / skills
```

具體到原文：

- 簡晨（OpenCode）→ Ollama → 本地 `qwen3.5:35b`
- 梅長蘇（claude-agent-acp）→ Anthropic API → Claude cloud

Agent 也可能讀檔、改檔、跑測試、呼叫 MCP tool。

### 第五段：結果回傳

```
Coding Agent CLI stdout
↓
OpenAB
↓
Discord API
↓
Discord channel / thread
↓
使用者看到回覆
```

整體不是單一 API call，而是一串分層傳遞：

`Discord 網路事件 → OpenAB 判斷與 session 管理 → ACP → JSON-RPC → stdin/stdout → Coding Agent → LLM/tools → 回到 Discord`

---

## 9. 單機 / 單 server 快速測試

如果只是 PoC，不需要一開始就上 K8s（原文作者的部署也沒提 K8s，只提 Railway/Vercel，那是該產品本身的線上部署，與 agent 基礎建設無關）。

可以先用單機或單 server 測：

```
一台 Mac / Linux server
↓
跑一個或多個 OpenAB process
↓
各自連不同 Discord bot token
↓
各自啟動不同 Coding Agent CLI
↓
必要時共用同一個 Ollama 服務
```

最小測試：

- 1 個 Discord bot
- 1 個 OpenAB process
- 1 個 Coding Agent CLI
- 1 個測試 repo

多 agent 測試：

- 3 個 Discord bot
- 3 個 OpenAB process
- 3 組 config
- 3 個 working_dir
- 1 個 Discord channel

不一定要多台機器，一台機器可以跑多個 OpenAB process。

真正吃資源的通常不是 OpenAB 本身，而是：

- 本地 LLM 推論（Ollama）
- Coding Agent 掃 repo
- 跑測試
- 跑 build
- 多 agent 同時工作造成的 CPU / RAM / GPU 壓力

PoC 階段先確認以下幾件事就夠了：

- Discord 能不能觸發 bot
- OpenAB 能不能接 agent
- agent 能不能讀 repo
- working_dir 分工是否有效
- agent 回覆是否能回到 Discord

---

## 10. 未來規模化潛力

如果未來要給團隊或公司使用，OpenAB 的價值在於：

> 把 Coding Agent 從個人 terminal 工具，變成團隊可協作的聊天平台成員。

可能的規模化方向：

### 10.1 多角色分工

可以把不同 agent 固定成不同角色：

- Architect agent
- Backend agent
- Frontend agent
- QA agent
- Ops agent
- Data engineer agent
- PM assistant agent

每個角色有自己的 working_dir、模型、工具、權限。

### 10.2 團隊共用入口

工程師不用每個人都在自己的 terminal 裡操作 agent。改成：

> 在 Discord / Slack 的專案頻道裡 @agent，所有人都看得到討論、分派與結果

好處：知識共享、任務透明、review、onboarding、團隊協作。

### 10.3 權限與安全治理

正式導入後需要補上：

- repo 權限
- secret 管理
- branch / PR 流程
- agent 可寫範圍
- 測試與驗證
- log 留存
- 操作稽核
- 成本控管
- 失敗回滾

OpenAB 提供協作入口，但不會自動解決所有治理問題。

### 10.4 部署升級

PoC 可以單機跑。未來若要 24/7、多人共用、高可用，可以考慮：

- Docker
- systemd / supervisor
- K8s
- Helm
- PVC
- 獨立 Secret
- 每個 agent 獨立 Pod
- 監控與 log

> K8s 的價值不是讓功能變多，而是讓部署更穩定、可管理、可重啟、可隔離。

---

## 11. 對公司用途的初步判斷

### 軟開協作

最適合先試。可以借 OpenAB 案例的三個設計：

- 聊天平台作為團隊入口
- 不同 agent 對應不同角色
- working_dir 限制每個角色視野

但不要直接導入正式開發流程。要先補 PR、測試、權限、secret、稽核與 rollback。

### 產業資訊整理

可以借多角色分工概念，但不一定要用 Coding Agent CLI。

產業資訊更需要的是：搜尋、爬蟲、文件解析、摘要、來源可信度、分類、報告生成。

OpenAB 可以當入口，但核心可能是另一套資料處理 pipeline。

### PPT 製作

不一定適合多 bot 討論式協作。PPT 製作更像：

```
一個 orchestrator
↓
大綱
↓
資料整理
↓
圖表生成
↓
投影片產生
↓
檔案回傳
```

比較適合單一 PPT bot + 工具鏈，而不是多個 agent 在 Discord 裡互相討論。

---

## 12. 最後總結

> OpenAB 的價值不是讓 AI 更聰明，而是讓既有 Coding Agent 可以進入團隊協作場景。

它的核心能力：

- 聊天平台整合
- agent process 管理
- ACP 橋接
- session 管理
- 多 agent 分工
- working_dir scope 控制

它不是 LLM brain，也不是完整 autonomous agent framework。

最重要的一句理解：

> OpenAB 把「個人在 terminal 使用 Coding Agent」改造成「團隊在 Discord / Slack 裡協作使用 Coding Agent」。

這個案例真正值得學的地方，是它把 agent 分工從抽象 prompt 變成具體工程設計：

- 不同 bot
- 不同 agent backend
- 不同 working_dir
- 不同模型
- 不同任務邊界

這才是它對團隊導入 AI coding agent 最有價值的地方。

---

## 13. 想真的讀懂 OpenAB 原始 repo，至少需要哪些知識

不必全會才能讀，列出來是為了「卡住時知道是缺哪一塊」。分四圈，由必要到可選。

### 第一圈：絕對必要

- **Rust 基礎語法**（能讀，不必會寫）：`fn` / `struct` / `trait` / `async fn` / `Result` / `?` / `Arc<Mutex<T>>` / `serde` derive
- **async/await 心智模型**：理解「concurrent ≠ parallel」、`tokio::select!`、channel；沒這層讀 `acp/connection.rs` 會卡死
- **行程與 stdio**：父子進程、pipe、`Command::spawn().stdin/stdout`，知道為什麼 stdio 比 HTTP 簡單但夠用
- **JSON-RPC 2.0**：request/response/notification 三種訊息、`id` 為什麼重要（async 配對）。半小時讀完 jsonrpc.org 規範
- **ACP 核心 method**：`session/new`、`session/prompt`、`session/update`、`session/request_permission`。直接讀 `anthropics/agent-protocol` schema

### 第二圈：理解架構需要

- **Discord/Slack bot 機制**：Gateway WebSocket、Socket Mode、bot token、@mention payload、thread 概念
- **serenity / slack-morphism 函式庫的 EventHandler 模式**（不必會寫，要能跟著讀）
- **TOML 設定設計**：跟 YAML/JSON 的差別、為什麼 secret 走環境變數
- **Cron 表達式 + 排程器設計**：5-field 語法、IANA timezone、hot-reload 的動機

### 第三圈：看懂部署運維

- **Docker 多階段構建**：`FROM ... AS builder` / `COPY --from=builder` 為什麼要分離
- **K8s 概念（光看圖懂就好）**：Pod、Deployment、ConfigMap、Secret、PVC、Helm chart——不必會 kubectl
- **Linux 基礎**：環境變數、`$HOME`、`tini`（PID 1 為什麼要包）、`pgrep` 健康檢查

### 第四圈：可選

- **`aws-samples/sample-acp-bridge`**（OpenAB 的設計來源，更小更好懂）
- **OpenClaw**（`reactions.rs` emoji status 的設計來源）
- **STT 鏈**：`media.rs` + `stt.rs`，不用語音功能可整段跳過
- **WebSocket 協定本身**：debug 連線問題才需要

### 建議閱讀順序（給自己當地圖）

1. `README.md` → `config.toml.example`
2. `src/main.rs`（entrypoint）
3. `src/adapter.rs`（trait 抽象）
4. **`src/acp/protocol.rs`（最值得讀的一塊，看 ACP 訊息怎麼模型化）**
5. `src/acp/connection.rs`（spawn 子進程 + 雙向 JSON-RPC）
6. `src/acp/pool.rs`（session 管理）
7. `src/discord.rs`、`src/slack.rs`（具體平台適配）
8. 部署：`Dockerfile` → `k8s/` → `charts/openab/`

### 高 ROI 的補充順序（時間有限就照這個）

1. JSON-RPC 規範（半小時，受用十年）
2. ACP schema（一小時，OpenAB 的核心抽象就解開了）
3. Rust async/await 心智模型（一個週末）
4. Discord bot 概念 + 申請一次 token（兩小時）
5. Docker 多階段構建（一小時）
6. K8s 概念（光看圖，半天）
7. Rust 語法本身（最後補——光懂語法不懂 async 也讀不動）

### 兩個觀念解鎖一半的 OpenAB

1. **「父進程透過 stdin/stdout 控制子進程」跟 HTTP API 是同等抽象，只是換管道**——理解這點就理解 OpenAB 為何不需要 HTTP server
2. **「協定」≠「函式庫」**：ACP 是協定（規範），`claude-agent-acp` 是實作協定的 adapter。理解這點就理解為什麼 OpenAB 換 backend 這麼容易
