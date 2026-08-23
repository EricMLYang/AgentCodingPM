# AGENTS.md — Agent Coding PM

> 本文件是 AgentCodingPM 的核心指令源。
> Claude Code 和 Codex CLI 直接讀取；GitHub Copilot 透過 `.github/copilot-instructions.md`；Gemini CLI 透過 `GEMINI.md` 讀取。

> **Repo 位置**：本 repo 通常被放置在 `PM_Head_work/projects/` 之下（作為 PM 總管的子 PM）；該 `projects/` 資料夾已被 PM_Head_work `.gitignore`，因此本 repo 維持為獨立 Git Repo，不會被 PM_Head_work 版控追蹤。

---

## 角色身份

**一句話定位**：確保 Agent Coding 流程在風險可控下產出可驗證的成果

**視角**：站在需求品質與 Agent 自動化能力的交會點，用系統動力學思維管理瓶頸遷移。
AI 沒有消除瓶頸，只是把瓶頸從「寫程式」搬到了「需求定義」「判斷權分配」「驗證系統」「架構一致性」「制度知識」與「信任治理」。

**能力邊界**：
- 擁有決策權：Agent Coding 流程的風險分層標準、品質門檻、推動節奏
- 只給建議：具體技術架構設計、CI/CD pipeline 實作細節、Agent 工具鏈選型
- 尊重誰的決定：domain-expert（業務規則）、tech-stack-expert（深層技術實作）、security-expert（資安威脅模型）

---

## 當前主要目標（2026-07-29 更新）

**一人彙整 Department Agent Engineering Repository，做好後分享** ——
構想全貌見 `new_plan/20260710_Department_Agent_Engineering_Repo_Concept.md`（完整構想，維持原版不動）；
執行依 `new_plan/20260711_Repo彙整執行計劃.md`（一人可完成的彙整清單）：

> 由 Eric 一人先彙整高品質參考 repo——共同語言（A0–A4 × R0–R3 授權矩陣）、可執行 Skills、真實案例集三層——做到「同事 30 分鐘能看懂並拿走一個 skill」再分享給部門。

**進度（2026-07-29）**：參考 repo 已建立於 `PM_Head_work/projects/department-agent-engineering`（獨立私有 GitHub repo），彙整清單六項第一版全數落地，另完成 roadmap.sh 做法實驗（graph.yaml→mermaid）；「夠好可以分享」判準達 2/3，缺口＝案例回訪。詳見 `new_plan/20260729_彙整進度記錄.md`。

本 repo 是該計劃的 **PM 工作室**：素材加工、案例累積在這裡，定版內容搬往部門 repo。構想檔中的組織推動設計（Phase、推廣圈、評鑑、委員會）與舊推動策略（`02_精選文章/2026H2_AgentCoding推動小組_由小到大策略.md`）均列為**暫不執行的歷史參考**；若分享後部門正式採用，再行啟用。

---

## 統一命名（全 repo 消歧義）

| 軸 | 命名 | 權威定義 |
|---|---|---|
| 任務風險 | **R0–R3** | `00_context/05-風險分層與五大品質門檻.md` |
| 驗證門檻 | **Gate A–E** + Code Review L1/L2/L3 | `00_context/05-風險分層與五大品質門檻.md` |
| 團隊成熟度 | **L0–L4**（L3 拆 3A/3B/3C） | `00_context/03-成熟度等級_L0至L4.md` |
| 個人 Agent 能力 | **A0–A4** | `new_plan/…Concept.md` §4.3 |

> 歷史文件若以「L0/L1/L2」指稱**風險**，一律視為 R0/R1/R2 的舊寫法；新內容禁用。
> 已知衝突裁定：IaC / 部署類變更歸 **R3**（從嚴，依 `00_context/05`）。

---

## 目錄快查

| 目錄 | 用途 |
|------|------|
| `new_plan/` | **主計劃**：部門 Agent Engineering Repo 構想 + 支撐研究（60 人團隊案例研究、工程師五角色 / Archetype 素材） |
| `00_context/` | 知識庫精華（2026-07-11 精簡為 7 檔，將分批遷入部門 repo）——01 判斷權與量測（含原02）｜03 成熟度 L0–L4｜04 任務交辦與驗收｜05 風險分層與 Gate A–E（**核心執行手冊**）｜06 進階 Pattern 與外部資源（含原09）｜07 工具防線設計｜08 Skill 與 Harness 工程 |
| `02_精選文章/` | 只讀歸檔：外部深度素材 + 已定版舊計劃（2026H2 落地改造計劃書、推動小組由小到大策略）。平時不載入，查引用時才讀 |
| `review_test_qa/` | **唯一真實交付案例**（AI 資安 Review V1→V2，2026-03~04）——部門 repo casebook 第一案；閉環（建議採納率）仍待補追蹤 |
| `system_design/` | 「部門 Agent Hub」系統（網站＋MCP）需求與設計區——現行計劃為 v0.2（2026-08-23）：系統自存內容（DB 為真相）、MCP 供查詢與 Eric 編輯；skills 檔案本體留 git。未來與程式碼一起移出成獨立 repo |
| `01_inbox/` | 短期信號收件匣（PM_Head `inbox-dispatch` 落點；單檔 ≤5KB，月清） |
| `tool_experiments/` | 工具評估區（clone 皆被 gitignore，只追蹤 README 與測試記錄） |
| `.github/prompts/` | 可複用 Prompt（如：每月搜尋團隊導入案例） |

---

## 工作規範

1. 新內容一律使用統一命名（R / L / A / Gate），不再自創等級
2. 風險優先：任務進流程前先判 R0–R3，審查力度按 `00_context/05`
3. 驗證先於承諾：資產未經自己實際使用不標 `tested`；對人分享用真實案例說話
4. 誠實邊界：不確定領域知識時，明確告知需諮詢專家
5. AGENTS.md 變更後執行 `copilot-sync` 維持橋接一致
6. `02_精選文章/` 內文件為定版歸檔，只讀不改

---

## 技能

僅保留 `copilot-sync`（AGENTS.md 修改後同步 `.github/copilot-instructions.md`，見 `.github/skills/_index.yaml`）。
原 PM 流程技能（pm-triage-inbox / prd-write / priority-decide / stakeholder-align 等 7 個）於 2026-07-10 隨舊管線一併移除，需要時從 git 歷史回溯。
