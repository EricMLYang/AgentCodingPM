---
name: pm-move-backlog-to-handoff
description: 將 Backlog 項目移交工程，產出完整 Product Brief。適用於「移交工程」「handoff」「產出 Product Brief」「準備 PDR」。
---

# Skill: 移動項目 Backlog → Handoff

## 目標
將 Backlog 項目移交工程，產出 Product Brief 供工程團隊進行 PDR 與開任務。

## 輸入
- `item_id`：例如 `RQ-001`
- `title`：項目標題
- `reason`：移動原因
- `handoff_date_actual`：YYYY-MM-DD

## 執行前檢查
1. 讀取 `05_product/03_backlog/00_backlog_list.md`，確認該項狀態可移交（`ready`）。
2. 檢查是否具備足夠證據（驗證摘要、風險、範圍建議）。
3. 必讀 `00_context/` 下相關背景檔案。

## 執行動作

### 1. 更新 Backlog 清單 [autonomous]
將狀態更新為 `handed-off`，填入實際 Handoff Date。

### 2. 產出 Product Brief [autonomous]
在 `05_product/03_backlog/product_brief/` 建立檔案，命名為 `{item_id}_{簡短英文標題}.md`。
使用 [Product Brief 模板](templates/product-brief.md) 撰寫。

### 3. 品質門檻確認 [hybrid]
根據風險等級，確認對應的 Gate 檢查項已列入 Product Brief。

### 4. 決策記錄 [autonomous]
若有關鍵取捨，追加到 `.ai/memory/decisions.md`。

## 撰寫原則
- 內容要足夠讓工程團隊理解需求全貌並做技術評估。
- User Stories 需具體到可拆 Task。
- 標註「待 PM 確認」的項目，不替 PM 最終拍板。
- 語言：繁體中文（技術名詞可保留英文）。

## 禁止事項
- 沒有 Product Brief 不算完成 handoff。
- 一個需求一個檔案，不合併。
- 任何取捨都需可追溯。

## 品質標準（Quality Gates）
- Product Brief 完整可執行。
- 風險等級對應的 Gate 檢查項已列入。
- User Stories 可拆 Task。

## 已知限制（Limitations）
- 技術可行性需工程團隊在 PDR 確認。
