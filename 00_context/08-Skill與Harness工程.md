---
title: Skill 設計與 Harness 工程
status: stable
last_review: 2026-07-11
sources:
  - 2026-07-11 精簡：去除 fork 決策紀錄、BAT/Spectra 細節與過期下一步，保留 Skill 設計規範與擴散方法——這是新計劃 assets/ 層的直接工作手冊（完整版見 git 歷史）
tldr: Skill = 把工程紀律編譯成「Agent 可執行的狀態機」；SKILL.md 六節結構 + Anti-Rationalization Table + Progressive Disclosure（<500 行）；擴散最小單位 = Template Repo + init.sh。
---

# Skill 設計與 Harness 工程

## TL;DR

- **Skill 不是 prompt pack，是「Agent 可執行的狀態機」**：觸發 → 階段推進 → 證據產出 → 自檢 → 驗收。
- **三層正交**：Skill（how）× Persona（who）× Slash Command（when），可獨立演進、獨立審計、獨立替換。
- **Anti-Rationalization Table 是招牌**：列出 Agent 偷懶藉口逐條反駁，把「規則」變成「可執行護欄」。
- **Progressive Disclosure**：SKILL.md < 500 行；reference 拆出去按需載入；scripts 不算 context。
- **擴散最小單位**：Template Repo + `init.sh` + 可替換 context 範本。

---

## 一、三層正交與 orchestration 規則

```text
Slash Command（when：使用者入口）
  → Persona（who：角色視角 + 輸出格式）
    → Skill（how：工作流 + mandatory hops + 退場條件）
```

三條規則：

1. **Persona 不能呼叫其他 persona**，只有 user/command 是 orchestrator（避免無限遞迴與多 Agent 失控）
2. 唯一允許的 multi-persona 是 **parallel fan-out + merge**（同時跑 code-reviewer / security-auditor / test-engineer，最後合併報告）
3. 明確拒絕「router persona 決定要呼叫誰」這種 anti-pattern

## 二、SKILL Anatomy（6 個必備 section）

```markdown
---
name: <skill-id>
description: Use when <trigger condition>
---

## Overview          （一句話本質）
## When to Use       （什麼情境觸發；什麼情境不要用）
## Process           （步驟 + checkpoint + exit criteria）
## Common Rationalizations （Agent 常用偷懶藉口 + 反駁）
## Red Flags         （出問題的徵兆）
## Verification      （驗收證據：tests passing / build output / runtime data）
```

**Verification 不可省**：沒有客觀證據，Agent 會用「看起來對」通過。

## 三、Anti-Rationalization Table（範例）

| Rationalization | Reality |
|----------------|---------|
| "I'll write tests after the code works" | You won't. Tests after the fact test implementation, not behavior. |
| "I tested it manually" | Manual testing doesn't persist. Tomorrow's change might break it. |
| "This is too small for a skill" | Size isn't the criterion; risk is. |
| "It works on my machine" | Then ship your machine. Otherwise build a reproducible env. |

同樣手法適用非工程流程的偷懶藉口（「這個太小不用驗證」「先當低風險跑跑看」）——把資深者的直覺做成新人與 Agent 能執行的規則。

## 四、人類紀律 → Agent 可執行約束的轉化

| 人類的工程紀律 | 傳統做法（易繞過） | 編碼方式 |
|--------------|-----------------|---------|
| 寫 code 前先寫 spec | 口頭提醒、Wiki | frontmatter 依 intent 自動觸發；未完成 Specify 不能進 Plan（gated workflow） |
| 先寫失敗測試 | Review 時才發現沒測試 | skill 明確要求「test must fail first」 |
| 每個 PR 限 ~100 行 | Review 時才發現太大 | 把 100 行寫進 Process 步驟，主動觸發 split |
| 不要事後合理化 | 自欺欺人 | 每個 skill 都有 Rationalizations 反駁表 |
| 證據本位決定 done | 用「看起來對」通過 | 每個 skill 最後一節是 Verification |
| 偏好特定 commit/PR 格式 | 文件上寫、沒人讀 | Hooks 在 session 生命週期強制執行 |

## 五、最值得抄的 skill 技巧

1. **Surface Assumptions**——動手前先列 4-5 點預設，「Correct me now or I'll proceed with these.」把隱性假設變顯性契約
2. **Reframe as Success Criteria**——把 `Make the dashboard faster` 改寫成 `p95 < 500ms, LCP < 2s`
3. **Plan Mode 禁寫 code**——planning 階段明文唯讀，避免計畫被既成事實綁架
4. **Three Similar Lines beats a Premature Abstraction**——看到第三個使用場景才能抽象，反制 DRY 強迫症
5. **Prove-It Pattern**——bug 先寫測試複現、測試失敗才代表 bug 真實存在
6. **Trust Levels**——讀 context 時分級：Source/Tests = Trusted；External docs = Verify；User content = Untrusted（防 prompt injection）
7. **Surface the Conflict**——docs 與 codebase 衝突時給用戶 A/B 選項，不靜默決定
8. **Severity 三級標籤**——Nit / Optional / FYI，避免把雞毛蒜皮當 blocker

## 六、按「場景痛點」反查

| 痛點 | 對應機制 |
|------|---------|
| Agent 亂猜需求 | Surface Assumptions + spec-driven |
| PR 太大 reviewer 爆氣 | Change Sizing + atomic commits |
| Agent 寫沒人維護的 abstraction | Three Similar Lines 規則 |
| Agent 編造不存在的 API | source-driven + Trust Levels |
| 修 bug 反覆復發 | Prove-It Pattern |
| Deploy 後才爆炸 | staged rollout + rollback 寫進 skill |

## 七、Harness 思維（不必做工作台，要學思路）

Harness = 設計一個更可控、更安全、更有效率的 Agent 工作環境：控制 workspace / terminal / git / plan / permission / context，再去驅動任何 CLI Agent。核心信念：「畫面上每一塊都是自己掌控的工具，AI 大廠怎麼改版都能順著吃到，不被單一 IDE/CLI 綁住。」

日常落地就是 `06` 的 9 個進階習慣（plan-first、worktree 隔離、任務記錄、context 刻意化…）。

## 八、擴散：Template Repo 三步驟

提供者做三件事：

1. **Template Repo**（`context/` + `agent/` + `scripts/init.sh` + README），設成 GitHub Template Repository
2. **context 放可替換範本**（`context/project.template.md`）
3. **init.sh**：只做一件事——把 template context 變成 project context

使用者只需記三件事：**Use template / run init.sh / edit context**。不需要記命令、不需要懂結構、不需要看文件——這是 skill 資產分享給部門時的最低摩擦形式。

## 與其他主題的連結

- 單 Agent + Skill 的理由 → [`06-AgentCoding進階Pattern.md`](./06-AgentCoding進階Pattern.md)
- Skill 觸發條件與風險分層 → [`05-風險分層與五大品質門檻.md`](./05-風險分層與五大品質門檻.md)
