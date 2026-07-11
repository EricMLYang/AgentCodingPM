---
title: AgentCoding 進階 Pattern 與外部資源
status: stable
last_review: 2026-07-11
sources:
  - 2026-07-11 精簡整併：原 06-進階Pattern 去除長引文與過期趨勢，併入原 09-外部資源清單精華（完整版見 git 歷史）
tldr: 10 個跨案例 Pattern（工具會換、這些不會）+ 9 個馬上可學的進階習慣 + 精選外部資源。核心：Harness Engineering、單 Agent + Skill > 多 Agent、Plan mode 是底線、context 經濟學。
---

# AgentCoding 進階 Pattern 與外部資源

## TL;DR：10 個 Pattern

| Pattern | 一句話 |
|---------|-------|
| **A** Harness Engineering | 頂尖玩家自己做工具，做的是「讓 Agent 穩定產出的生產線」 |
| **B** CLI > IDE / Web | CLI 能讀檔、跑指令、看錯誤、跑測試——input 品質差距決定效果 |
| **C** 單 Agent + Skill > 多 Agent | 多 Agent 切 context = 變笨；Skill 才是真槓桿 |
| **D** Plan Mode 是底線 | R1/R2 任務強制 plan-first；模型自我驗證能力持續觀察 |
| **E** Spec-Driven Development | Spec 是「跨 session/Agent/模型的 context 壓縮層」 |
| **F** MCP 是隔離層 | 「AI generates. Humans authorize.」最小權限明文化 |
| **G** 開發環境是隱形瓶頸 | Procfile/devcontainer 跟 prompt/skill 一樣重要 |
| **H** Context 經濟學 | read/think/write 都吃 token，要學 context 工程不是 prompt 工程 |
| **I** 1:10 人工驗證 | 設計過的關鍵場景驗證 > test case 海戰（僅限低風險） |
| **J** 治理觀 | 模型還在童年，低風險給空間探索；治理不等於普遍管制 |

## Pattern 重點展開

- **A Harness**：不只是「用 Claude Code 寫 code」，而是設計讓全團隊用 Agent 產出可驗證成果的生產線。repo 裡的 `AGENTS.md` + skills + context 檔本身就是 harness——「不用管哪家 Coding Agent、模型怎麼升級，大廠打得越兇這套流程越順」。
- **B CLI 優先**：團隊推廣以 CLI Agent（Claude Code / Codex CLI）為主軸；IDE 整合只在封閉環境（如 DB 連線）才優先。
- **C 單 Agent + Skill**：把人類經驗濃縮成 Skill 讓單一 Agent 調用；要平行就開多個 session，不要塞進「多 Agent 系統」。觀察 trigger：等模型能可靠切 context 不變笨再投入 multi-agent。
- **D Plan Mode**：固定句式——「先進 planning 模式，不修改檔案，先提出 plan / 風險 / 驗收方式，等我確認後再實作」。R1/R2 強制，R0 可放開。
- **E SDD**：spec 不是文件潔癖，是「讓下一個 session 的 Agent 知道前面為什麼這樣做」的 context 壓縮機制。參考 GitHub spec-kit。
- **F MCP 隔離**：設計 MCP 時明文化權限契約「可讀 X、可寫 Y、禁止其他」——對應 Gate B（Agent 工具安全）。
- **G 開發環境**：把「啟動環境的雜事」也納入 Agent 管理範圍（Procfile / overmind / devcontainer 模板化），否則 onboarding 永遠卡在環境。
- **H Context 經濟學**：context 管理 = 把問題投影到低維度 + 快取已解過的子問題；投影方法暗示你預設的答案，預設錯後面全錯——所以「確認大方向」比過去更重要。
- **I 1:10 驗證**：讓 Agent cook 十小時、自己花一小時驗證「設計過的關鍵場景」。**邊界**：只適用高熟練者 + R0/R1；R2/R3 絕對需要 test/contract test。
- **J 治理觀**：只在「不可逆／敏感資料／跨契約」處嚴管，其餘給探索空間 + git 當安全網——與 `05` 的按風險分流是同一哲學。

---

## 9 個馬上可學的進階習慣

1. **建立自己的 Agent 使用規則**——repo 放 `AGENTS.md` / `CLAUDE.md`
2. **Prompt 改為 plan-first**——先 plan、列風險與驗收，確認後再實作
3. **重要任務用 branch / worktree 隔離**——做完先看 diff 再決定 merge
4. **記錄 Agent 任務結果**——目標、工具、改了什麼、哪些 prompt 有效、哪裡失控
5. **Plan mode 變預設**——R1/R2 任務強制
6. **Context 管理刻意化**——不貼 500 行 test output，只貼關鍵 stack trace
7. **Skill 比 prompt 重要**——重複的 prompt 沉澱成 skill 檔
8. **Procfile / devcontainer 模板化**——環境別讓人手動 setup
9. **看 cost / cache**——token 經濟感成為肌肉記憶

---

## 外部資源精選（原 09 併入）

### 值得 follow 的人

- **Tony Wang（tony1223）**——[BAT (Better Agent Terminal)](https://github.com/tony1223/better-agent-terminal)；context economics、cache 經濟學、plan mode 護欄
- **高健龍（5xCamp）**——[Spectra](https://spectra.5xcamp.us/)；SDD 推廣、Harness 實踐、跨 Agent 抽象
- **wchung**——[OpenAB](https://wchung.tw/blog/from-openclaw-to-openab-automating-design-handoff-with-ai)；MCP 最小權限、「AI generates. Humans authorize.」
- **Addy Osmani**——[agent-skills](https://github.com/addyosmani/agent-skills)；SKILL anatomy 範式（詳見 `08`）

### 課程

- **Stanford CS146S**（https://themodernsoftware.dev/）——完整 mental model，業界重量級來賓
- **台大 Web Programming（Ric Huang）**——免費開源，適合推薦給非 SE 背景的人
- **DeepLearning.AI: Spec-Driven Development with Coding Agents**——理解 spec-kit / SDD 設計思想

### 必讀文章

- **Anthropic: [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)**——AI 交接如工程師交接，上一棒必須留下足夠 artifacts；任務必須能在一個 context window 內解決
- **Martin Fowler / Kief Morris: [Humans and Agents in Software Engineering Loops](https://martinfowler.com/articles/exploring-gen-ai/humans-and-agents.html)**——人的角色是 build and manage the working loop
- **OWASP LLM Top 10 / 國際 AI 安全報告**——治理與資安基線，持續追蹤更新

## 與其他主題的連結

- Skill 設計規範 → [`08-Skill與Harness工程.md`](./08-Skill與Harness工程.md)
- 工具防線組合 → [`07-工具地圖與防線設計.md`](./07-工具地圖與防線設計.md)
- Pattern I/J 的風險邊界 → [`05-風險分層與五大品質門檻.md`](./05-風險分層與五大品質門檻.md)
