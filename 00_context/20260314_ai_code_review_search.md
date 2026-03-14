我先給你一句總結：

**到 2026-03，Agent 軟體開發的重點，已經不是只有 AI 寫 code，而是 AI 被正式放進 PR review、CI 驗測、資安 triage、品質 gate、修補建議、甚至 AI 供應鏈盤點裡。** 真正有價值的方向，是把「AI reviewer + 確定性掃描器 + 真實執行驗證 + 人工審核門檻」串成一條品質防線，而不是相信單一 agent。 

## 一、目前最明顯的趨勢

### 1) 從「寫 code 輔助」走向「整條 SDLC 的審查與驗證」

Anthropic 的 2026 agentic coding 趨勢報告把重點放在：agent 不只是補字，而是開始處理寫測試、debug、文件、複雜 codebase 導航，甚至朝多 agent 協作發展；但同時，開發者對高風險工作仍只能低比例「完全委派」，人類監督與驗證依然是核心。這代表 review、驗測、資安檢查不再是附屬流程，而是 agent 開發的主戰場。 

### 2) 從規則型掃描，走向「上下文理解 + 規則引擎」混合模式

這是現在最重要的技術方向。Semgrep 明確把產品描述成「確定性的 static analysis + LLM contextual reasoning」的組合，用來抓傳統 SAST 不容易抓到的 business logic flaw，例如 IDOR 和 broken authorization。OpenAI 的 Codex Security 則先建立專案 threat model，再做漏洞搜尋、驗證、修補建議；它甚至強調在 sandboxed validation 環境中壓測發現，以降低噪音。Snyk Code 也強調 AI-based engine 的低誤報導向。 ([semgrep.dev][1])

### 3) Review 工具從 diff comment，走向 repo-aware / issue-aware / policy-aware

GitHub Copilot code review、GitLab Duo、Graphite、CodeRabbit、Qodo 這一類工具，已經不是只看幾行 diff。GitHub 說 Copilot 會從多個角度 review 並可套用建議；GitLab 的 Code Review Flow 會看 code changes、MR comments、linked issues、repo structure 和 cross-file dependencies；CodeRabbit、Qodo、Graphite 都主打 context-aware review。這代表 review 越來越像「小型審查 agent」，而不是單純 autocomplete 的延伸。 ([GitHub Docs][2])

### 4) 供應商開始承認：真正難的是訊號品質，不是找到更多問題

最新研究已經直接指出，AI code review agents 若一味追求抓出所有隱藏問題，常會出現 **low signal-to-noise ratio**；看起來很努力，但會用一堆雜訊拖垮開發者。OpenAI 在 Codex Security 也把「higher-confidence findings」「reduce unnecessary triage burden」當成主賣點。這說明市場正在從「多抓」轉向「少但準」。 ([arXiv][3])

### 5) 品質治理開始從「偵測 AI code」轉向「不管是不是 AI，都設更嚴格 gate」

Sonar 很值得注意。它一方面推出 AI Code Assurance，讓團隊可對含 AI 生成 code 的專案套用專門 quality gate；另一方面在 2026.1 又把自動偵測 AI code 的功能標成 deprecated，理由是高 AI adoption 下要調整做法。這很像一個訊號：未來不是執著於「哪段是不是 AI 寫的」，而是把新 code 一律放進更嚴格的品質、可維護性、安全門檻。 ([docs.sonarsource.com][4])

### 6) AI agent 本身也成為新的資安對象

國際 AI 安全報告 2026 明講：general-purpose 與 agentic models 的安全與風險治理仍有顯著證據缺口，量化 benchmark 也還不成熟。Sonar 已加入「AI agent code execution without sandboxing is security-sensitive」規則；Snyk 也開始做 AI-BOM，把模型、datasets、agent、MCP 連線都納入供應鏈盤點。這代表品質檢查的範圍已經從「你的 app」延伸到「你的 agent 生態」。 

## 二、現在主流的方法論，實際上長什麼樣

我把現在比較成熟的做法整理成一條 6 層防線：

### 第 1 層：PR / MR 的 AI reviewer

目標不是放行，而是先做第一輪高覆蓋檢查：bug、漏測、風格、潛在安全問題、說明不完整。
代表工具有 GitHub Copilot code review、GitLab Duo、CodeRabbit、Qodo、Graphite。GitLab 已支援自動 review 與 custom review instructions；GitHub 也支援 repo-wide 和 path-specific custom instructions。 ([GitHub Docs][2])

### 第 2 層：確定性安全掃描當 blocking gate

這一層還是很重要，因為可重現、可治理、可 audit。
代表是 GitHub CodeQL、Semgrep Code、Snyk Code、SonarQube。CodeQL 將結果變成 GitHub code scanning alerts；Semgrep 可接 CI/CD；Snyk Code 直接進 IDE / repo / CI；Sonar 用 quality gate 決定過不過。這些工具比較適合拿來做「阻擋 merge」或最低基線。 ([GitHub Docs][5])

### 第 3 層：上下文型安全 agent，專抓傳統掃描器難抓的問題

這一層才是 2025–2026 真正升級的地方。
代表是 Codex Security、Claude Code Security、Semgrep AI-powered detection。它們都在做類似事情：讀更多系統上下文、理解信任邊界、降低誤報、提出可採納 patch。這一層特別適合抓 auth logic、跨檔案權限問題、business logic flaw。 ([OpenAI][6])

### 第 4 層：測試生成與真實執行驗證

這層是「不要只看起來合理，要跑起來」。
Diffblue Cover 會自動產生 Java / Kotlin unit tests，且在 sandbox 中執行；Playwright Test Agents 會根據 Markdown plan 產生測試，並即時驗證 selector 與 assertion；QA Wolf 則把 AI 放進 test flow 管理與編修。這些做法都比「純文字 review」更接近真實品質。 ([docs.diffblue.com][7])

### 第 5 層：測試經濟學優化

當 AI 讓改動頻率與變更量變大，測試成本也會爆。
Launchable 這類工具的價值在於 predictive test selection：不是每次全跑，而是優先挑最可能失敗、最能快速驗證風險的測試。這很適合 agent 開發，因為 agent 常做很多小步迭代與多輪修補。 ([Launchable][8])

### 第 6 層：品質與維護性 gate

這是很多團隊最容易少做的一層。
Sonar 直接把 reliability、security、security review、maintainability 做成 gate；GitHub / GitLab / CodeRabbit 這類 review 工具則越來越依賴 custom instructions，把團隊自己的架構規則、檔案路徑規則、code style、風險重點明文化。換句話說，**維護性檢查的核心，不只是 complexity 指標，而是把「你們團隊真正介意的維護成本」寫進 review policy。** ([docs.sonarsource.com][9])

## 三、工具與服務地圖

### A. AI Code Review / PR Reviewer

比較主流的是：
GitHub Copilot code review、GitLab Duo、CodeRabbit、Qodo、Graphite。
共同方向都是自動 review PR/MR、提供 inline comments、可套入 custom instructions，並逐步往更強的 repo context 走。 ([GitHub Docs][2])

### B. 資安檢查 / AppSec

穩定基線：CodeQL、Snyk Code、Semgrep Code、SonarQube。
Agent / contextual 類：Codex Security、Claude Code Security、Semgrep AI-powered detection。
現在的差異點主要在：能不能理解系統上下文、能不能降低誤報、能不能給可接受 patch、能不能驗證 exploitability。 ([GitHub Docs][5])

### C. 驗測 / 測試生成 / E2E

Java/Kotlin 單元測試生成：Diffblue Cover。
Web E2E / 互動式測試：Playwright codegen / test agents、QA Wolf。
測試選擇與縮時：Launchable。 ([docs.diffblue.com][7])

### D. 軟體品管 / Maintainability

SonarQube 是最典型的 gate 型產品，適合做 reliability / maintainability / security 的合併治理。
另外，GitHub / GitLab / CodeRabbit 這類工具的 custom instructions 能把「可維護性」從抽象名詞變成明確規範，例如哪些模組不准 cross-layer 依賴、哪些路徑需要特別檢查、哪些檔案要看效能或安全。 ([docs.sonarsource.com][9])

### E. AI / Agent 供應鏈與治理

Snyk 的 AI-BOM 很值得留意。它開始把 models、datasets、agents、tools、MCP server/client 關係映射成依賴圖。這件事現在還早，但方向很對，因為 agent 系統的風險已經不只在原始碼，也在外部模型、工具連線、MCP 資源。 ([docs.snyk.io][10])

## 四、你真正該注意的幾個判斷點

### 1) 不要只看「找出幾個問題」，要看「每 10 個 comment 有幾個真的值得修」

CR-Bench 已經提醒這件事：resolution rate 不足以衡量 AI reviewer，因為雜訊會拖垮整體效益。 ([arXiv][3])

### 2) 不要把 AI reviewer 當 merge approver

目前官方文件幾乎都把這些工具定位成 review / triage / remediation assistant，而不是替代人類責任。國際 AI 安全報告也明講治理框架還不成熟。 ([anthropic.com][11])

### 3) 真正高價值的是「有上下文」

GitHub、GitLab、Semgrep 都在強調 custom instructions、linked issues、threat models、design docs、project-specific context。沒有這些，review 很容易只剩 generic 建議。 ([GitHub Docs][12])

### 4) 維護性問題正在變大，不只是安全問題

GitClear 的資料指出，AI-assisted code 近年出現 duplicated code 上升、short-term churn 上升、moved lines 下降。這不代表 AI 一定讓品質變差，但代表如果沒有 refactor gate、結構檢查與 review policy，技術債會比以前更快累積。 ([GitClear][13])

### 5) Sandbox 與權限邊界會越來越重要

Diffblue 的 test generation 強調 sandbox；Anthropic 的 Claude Code on the web 也強調 isolated environment 與 network / filesystem restrictions；Sonar 甚至已把「AI agent code execution without sandboxing」列為 security-sensitive。這不是小事，因為 agent 已經真的會執行 code、連外、改 repo。 ([docs.diffblue.com][14])

## 五、如果你要落地，2026 比較合理的組合

### 小到中型團隊

先用：

1. PR reviewer：GitHub Copilot / CodeRabbit / GitLab Duo 擇一
2. Blocking scan：CodeQL 或 Semgrep / Snyk 擇一
3. 測試：既有 CI 單元測試 + Playwright E2E
4. 品質 gate：SonarQube 或至少把 lint / coverage / complexity / security scan 做成 merge gate
5. 對高風險模組再加 contextual security scan

這樣成本相對可控，也比較容易讓團隊接受。 ([GitHub Docs][2])

### 較成熟或高風險團隊

比較像：

* AI PR reviewer 做廣覆蓋
* CodeQL / Snyk / Semgrep 做 deterministic gate
* Codex Security / Claude Code Security / Semgrep AI detection 抓 business logic 與 auth 問題
* Diffblue / Playwright / QA Wolf 做執行驗證
* Sonar 做 maintainability / reliability gate
* Snyk AI-BOM 做 AI 供應鏈盤點

這才是現在比較接近「agent 時代品質體系」的樣子。 ([OpenAI][6])

## 六、我對這個領域的判斷

截至 2026-03，**這一塊還在快速成形，但方向已經很清楚**：

不是讓 AI 取代 review。
而是讓 AI 把 review、驗測、資安、品管從「人工抽查」變成「高頻、低延遲、可配置、可持續的系統」。
真正的瓶頸不在生成 code，而在 **訊號品質、上下文注入、真實驗證、與風險分級**。國際 AI 安全報告說治理框架仍不成熟，最新 code review 研究也提醒雜訊問題；所以短期內最強的做法，不是押注單一神奇 agent，而是把多層防線組起來。 

你如果要，我下一步可以直接幫你整理成 **一份「Agent 軟體開發品質防線地圖」**，用你熟悉的方式分成：
**AI Review / 驗測 / 資安 / 品管 / 維護性 / 治理** 六大模組。

[1]: https://semgrep.dev/docs/deployment/add-ai-to-scans "Add AI-powered detection | Semgrep"
[2]: https://docs.github.com/en/copilot/concepts/agents/code-review "About GitHub Copilot code review - GitHub Docs"
[3]: https://arxiv.org/html/2603.11078v1 "CR-Bench: Evaluating the Real-World Utility of AI Code Review Agents"
[4]: https://docs.sonarsource.com/sonarqube-server/ai-capabilities/overview "Overview | SonarQube Server | Sonar Documentation"
[5]: https://docs.github.com/code-security/code-scanning/introduction-to-code-scanning/about-code-scanning-with-codeql "About code scanning with CodeQL - GitHub Docs"
[6]: https://openai.com/index/codex-security-now-in-research-preview/ "Codex Security: now in research preview | OpenAI"
[7]: https://docs.diffblue.com/get-started/what-is-diffblue-cover "What is Diffblue Cover? | Diffblue Documentation"
[8]: https://help.launchableinc.com/features/predictive-test-selection/how-launchable-selects-tests/ "How Launchable selects tests | Launchable Docs"
[9]: https://docs.sonarsource.com/sonarqube-server/quality-standards-administration/managing-quality-gates/introduction-to-quality-gates "Understanding quality gates | SonarQube Server | Sonar Documentation"
[10]: https://docs.snyk.io/developer-tools/snyk-cli/commands/aibom "AI-BOM | Snyk User Docs"
[11]: https://www.anthropic.com/news/claude-code-security "Making frontier cybersecurity capabilities available to defenders \ Anthropic"
[12]: https://docs.github.com/copilot/using-github-copilot/code-review/using-copilot-code-review "Using GitHub Copilot code review - GitHub Docs"
[13]: https://www.gitclear.com/ai_assistant_code_quality_2025_research "AI Copilot Code Quality: 2025 Data Suggests 4x Growth in Code Clones - GitClear"
[14]: https://docs.diffblue.com/evaluation-and-onboarding/jumpstart/phase-1-up-and-running/module-1-create-your-cover-unit-test-baseline "Module 1: Create your Cover unit test baseline | Diffblue Documentation"
