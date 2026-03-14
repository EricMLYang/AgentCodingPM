---
name: skill-expand
description: Convert a recurring task into a usable skill. Use when user says "幫我把 [某工作流程] 固化成一個技能", "我發現每次做 [某任務] 步驟都一樣", or "我想讓這個 Agent 學會 [某能力]".
---

# Skill: skill-expand — 技能擴充

## 用途（Purpose）

當角色 Agent 發現某個重複性任務沒有對應技能時，引導完成從「模糊感知」到「可用技能」的端到端流程。

- **輸入**：一個重複出現的任務場景描述
- **輸出**：完整的 Skill 目錄（SKILL.md + checklist.md + changelog.md + examples/）

## 何時使用（When to Use）

- 「幫我把 [某工作流程] 固化成一個技能」
- 「我發現每次做 [某任務] 步驟都一樣」
- 「我想讓這個 Agent 學會 [某能力]」

## 流程（Process）

### Step 1: 識別任務模式 [hybrid]
確認觸發情境、主要產出、執行模式。

### Step 2: 設計 Skill 結構 [autonomous]
設計 alias、步驟清單、品質標準、已知限制。

### Step 3: 撰寫 SKILL.md [autonomous]
遵循 Agent Skills 開放標準格式（YAML Frontmatter + 6 段落）。

### Step 4: 建立支援檔案 [autonomous]
建立 checklist.md、changelog.md、examples/README.md。

### Step 5: 更新 _index.yaml [autonomous]
在 `.github/skills/_index.yaml` 中新增記錄。

## 品質標準（Quality Gates）

- SKILL.md 有 YAML Frontmatter（name + description）
- 包含完整 6 段落格式
- 觸發條件不超過 25 字
- 步驟數量在 3-7 個之間
- _index.yaml 已更新

## 已知限制（Limitations）

- 新技能的 SKILL.md 是「框架合規」的起點，需真實使用後迭代
- examples/ 初始為空
