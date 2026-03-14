---
name: capture-signals
description: Convert unstructured inputs (meetings, feedback, etc.) into structured Signals. Use when you have raw notes or observations to record in the Inbox.
---

# Skill: Capture Discovery Signals（信號收集）

## 目標
將非結構化輸入（會議紀錄、回饋、觀察）轉為結構化信號，寫入 `01_inbox/0_signals.md`。

## 輸出格式

每個信號使用以下模板：

```markdown
## SIG-YYYYMMDD-###: [Short signal title]
- Source: [meeting / user feedback / tool update / research / observation]
- Stakeholders: [who said / who cares]
- Signal (what happened): [What actually happened / what was said]
- Why it matters (impact on outcome/KPI): [Link to outcomes]
- Hypothesis (potential opportunity/problem): [Your interpretation]
- Risk Level Hint: [L0 / L1 / L2 / unknown]
- Tags: [area] [persona] [workflow] [risk]
- Status: new
```

## 流程

### Step 1: 讀取背景 [autonomous]
讀取 `00_context/` 相關檔案，確認 "Why it matters" 的對齊基礎。

### Step 2: 檢查既有信號 [autonomous]
讀取 `01_inbox/0_signals.md`，取得下一個序號。

### Step 3: 轉換信號 [hybrid]
將使用者輸入轉為結構化信號區塊。標註初步風險等級提示。

## 品質標準（Quality Gates）
- 每個信號有來源與時間戳。
- 有初步風險等級提示。
- Status 設為 new。

## 已知限制（Limitations）
- 風險等級為提示，正式判定在 triage 階段。
