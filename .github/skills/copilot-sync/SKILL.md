---
name: copilot-sync
description: Sync AGENTS.md changes to copilot-instructions.md. Use when user says "AGENTS.md 有修改,幫我確認 copilot-instructions.md", "確認橋接同步狀態", or after role-bootstrap.
---

# Skill: copilot-sync — Copilot 橋接同步

## 用途（Purpose）

當 `AGENTS.md` 有變更後，比對並更新 `copilot-instructions.md`，確保 Copilot 取得正確的角色識別與技能資訊。

- **輸入**：已更新的 `AGENTS.md`
- **輸出**：已驗證或已更新的 `copilot-instructions.md`

## 何時使用（When to Use）

- 「AGENTS.md 有修改，幫我確認 copilot-instructions.md」
- 「確認橋接同步狀態」

## 流程（Process）

### Step 1: 比對差異 [autonomous]
讀取 AGENTS.md、_index.yaml、copilot-instructions.md，輸出同步差異報告。

### Step 2: 確認更新範圍 [interactive]
展示需更新項目，詢問使用者確認。

### Step 3: 更新橋接內容 [autonomous]
更新 copilot-instructions.md，保留「完整版請參閱 AGENTS.md」連結。

## 品質標準（Quality Gates）

- 技能表與 _index.yaml 完全一致
- 角色識別與 identity.yaml 一致
- 「完整版請參閱 AGENTS.md」連結存在
- 更新後 ≤ 60 行

## 已知限制（Limitations）

- 不會自動偵測 AGENTS.md 的變更，需使用者觸發
