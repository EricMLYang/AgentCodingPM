---
name: 尋找團隊 AI/Agent 提效實踐案例
description: 用於請 AI 搜尋「團隊已配用 GitHub Copilot,進一步建立提效機制」的各種實踐案例(團隊級 MCP、Skills 分享、Cloud Coding Agent 等),並做相關性排序與踩坑提醒
trigger: 需要找團隊 AI/Agent Coding 進階提效實踐案例時
mode: hybrid
---

# Prompt:尋找團隊 AI Coding 進階提效實踐案例

> 使用方式:把以下整段(從「你是專長...」開始)複製到 ChatGPT/Claude/Gemini/Perplexity 等工具。
> 若使用 Deep Research 模式,再附加文末的「研究範圍」區段。

---

## 主 Prompt

```
你是專長企業 AI/Agent Coding 提效實踐的研究助理。我的團隊已全員配用 GitHub Copilot,我要找的不是「導入 Copilot」的入門案例,而是「已經有 Copilot 之後,如何進一步建立團隊級機制讓整體更有效率」的真實實踐案例。

## 我的情境
- 約 40 人開發團隊,1 個處 2 個部,我屬於其中一個部、是橫向推手(非處長本人)
- 全員已配用 GitHub Copilot,個人層級的使用已經普及
- 團隊技術異質性高(韌體、CMS 維運、應用系統 Java/C#/Vue/React、Databricks、Agent 應用)
- 我關心的指標:「需求到驗證通過」lead time、瓶頸遷移(從寫程式 → 需求定義/驗證/治理/知識共享)
- 我的成熟度視角:用 L0/L1/L2 風險分層 + 五大品質門檻(供應鏈安全、工具安全、系統正確性、程式碼品質、Agentic 工作流)

## 我想找的實踐類型(舉例,不限於這些)

### A. 團隊級工具與基礎設施
- **團隊級 MCP Server**:把內部 API、資料庫、知識庫、CI/CD 包成 MCP 給全團隊用
- **Cloud Coding Agent 機制**:Devin、Cursor Background Agents、GitHub Copilot Coding Agent、Claude Code Cloud 等的團隊化部署
- **Self-hosted Agent Runner**:在自家 infra 跑 Agent,處理敏感程式碼/資料
- **共享 Context / Memory 系統**:團隊共用的長期記憶、知識索引

### B. Skills / Prompt / Workflow 分享機制
- **Team Skills Library**:把驗證過的 Skill / Custom Instructions / Prompt 集中管理
- **AGENTS.md / CLAUDE.md / .cursorrules 治理**:團隊如何協作維護這些指令檔
- **Prompt Review 流程**:像 Code Review 一樣審查 prompt
- **Internal Slash Commands / Agent Workflows**:把重複任務固化成命令

### C. Code Review 與品質防線
- **AI Code Review 多層機制**:Bot 預審 → Human Review 的分工
- **Risk-tiered Review**:依風險等級調整 Review 力度
- **Hallucination / Supply Chain 防護**:幻覺依賴偵測、registry 驗證
- **Prompt Injection / Tool 安全**:Agent 工具權限收斂

### D. 度量與治理
- **Lead Time / 瓶頸量測**:不只看 LoC,看「需求到驗證通過」全流程
- **Adoption / Quality Dashboard**:Agent 使用率、Review 拒絕率、生產事故率
- **成本控管**:Token 用量、Agent 任務成本歸因

### E. 知識共享與能力建設
- **內部 AI Coding 讀書會 / Demo Day**
- **Pair-with-AI 配對教學**
- **失敗案例庫**(Anti-pattern Hall of Fame)
- **Champion / Guild 機制**:每隊培養 AI Coding 推手

## 篩選條件(由高到低權重)
1. **進階性**:已配 Copilot 之後的進階機制 > 基礎導入故事
2. **可遷移性**:跨技術棧/跨團隊類型適用的機制 > 單一技術棧專屬
3. **規模相符**:30-100 人團隊優先;部門級導入 > 全公司 > 個人開發者
4. **角色相似**:中階主管/橫向推手視角 > CTO 高層下令 > 工程師自發
5. **真實性**:有具體做法、流程圖、配置範例、踩坑、失敗教訓 > 行銷成功故事
6. **流程深度**:有講風險分層、品質門檻、治理機制 > 只講「導入了大家很開心」

## 排除條件
- 純「我們導入了 Copilot,大家很開心,生產力 +50%」這種無方法論的入門故事
- 純行銷稿、廠商 case study(除非附帶獨立驗證資料 / 具體配置)
- 單純的工具評測(A vs B 比較),除非帶有團隊化部署經驗

## 輸出格式(每個案例)
1. **一句話定位**:誰、做了什麼機制、規模
2. **歸屬類型**:對應上述 A/B/C/D/E 的哪一類(可多選)
3. **與我情境的相似度**:★★★★★(滿分 5),並標註相似/差異點
4. **可直接借鑒的 3 件事**:具體做法、配置、流程
5. **可能踩到的坑**:他們的失敗或限制,我該預先迴避
6. **來源**:URL/書名/演講,含發表時間(我要判斷時效)
7. **下一步建議**:若我要學這個案例,第一步該做什麼(MVP 規模)

最後請給我一個總表,把所有案例:
- 按上述類型 A/B/C/D/E 分類列出(同一類型內按相似度排序)
- 指出哪 2-3 個最值得深讀
- 整體看下來,我的情境最缺乏對應參考的部分是什麼(這部分我可能要自己摸索)
- 有沒有哪些「概念被講得很多、但實作案例很少」的領域(代表市場機會點)
```

---

## Deep Research 模式附加區段

若使用 ChatGPT Deep Research / Perplexity Pro / Gemini Deep Research,把以下追加在主 Prompt 後:

```
## 研究範圍
- 時間:2025-06 之後的案例優先(Agent Coding 演進極快,半年前的工具假設已可能過時;團隊級 MCP、Cloud Coding Agent 的成熟案例多為 2025 下半年後)
- 來源優先級:
  1. 工程部落格:GitHub Eng、Stripe、Shopify、Anthropic、Vercel、Cloudflare、Netflix、Spotify
  2. 開發者社群:HN、Reddit r/ExperiencedDevs、Lobsters
  3. 演講/Podcast:QCon、Strange Loop、InfoQ、Latent Space、Practical AI
  4. 書/長文:IEEE Software、ACM Queue、Martin Fowler 站、thoughtworks Tech Radar
  5. 內部公開的 Skills/Prompts repo(GitHub 上 awesome-* 系列、各公司公開的 .cursorrules / AGENTS.md)
- 語言:英文/日文/繁中皆可
- 額外提示:特別關注「已實作並運行 3 個月以上」的案例,而非「我們打算這麼做」的展望文
```

---

## 設計理由(為什麼這樣下 prompt)

| 設計點 | 為什麼重要 |
|-------|-----------|
| 明確排除「導入 Copilot」入門故事 | 我的情境已過了這階段,需要的是進階提效機制 |
| 列出 A-E 五大實踐類型清單 | 給 AI 具體可搜尋的關鍵字,避免它只回傳泛泛而談的「文化變革」 |
| 強調「中階主管橫向推手」角色 | 大多公開案例是 CTO 視角,我要的是同梯橫向推動的實作 |
| 要求標註相似度 + 類型歸屬 | 強迫 AI 做分類與相關性判斷,而非「找到就塞」 |
| 要求踩坑/失敗教訓 | 機制類實踐特別容易過度設計,失敗案例的可遷移性最高 |
| 最後問「概念多但案例少」的領域 | 暴露市場盲區,可能是我們可以做出差異化的地方 |
| 要求「運行 3 個月以上」 | 過濾掉 PoC、概念展望,只看真正落地的 |

---

## 變體用法

- **聚焦單一類型**:把「我想找的實踐類型」中只保留一類(例如只看 A 團隊級工具),做深度搜尋
- **聚焦特定工具**:把「Cloud Coding Agent」改成具體工具名(Devin / Cursor Background / Copilot Coding Agent),要單一工具的團隊化案例
- **找失敗案例**:把篩選條件 5 改為「**只要**失敗、撤回、降級的案例」,例如「為什麼這個團隊放棄了團隊級 MCP」
- **找量測方法論**:聚焦類型 D,要「如何度量 Agent Coding 對團隊產出的真實影響」的方法論案例
- **找跨技術棧治理**:強化「技術異質性高」這個條件,專找多語言/多技術棧並存的團隊治理案例

---

## 相關 Inbox

- `01_inbox/20260425_團隊創新推廣任務.md` — 任務原始描述
