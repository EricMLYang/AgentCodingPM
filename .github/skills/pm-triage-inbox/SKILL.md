---
name: pm-triage-inbox
description: 分類 Inbox 信號為事實/信號/行動項目，作為後續處理前置步驟。適用於「分類信號」「triage inbox」「這些會議紀錄要怎麼處理」。
---

# Skill: Inbox Triage（分類 Inbox 信號）

## 目標
讀取 Inbox 檔案，將每筆資訊分類並輸出建議行動，作為後續 update-context / move-inbox-to-opp-exp 的前置步驟。

## 輸入
- `inbox_file`：（可空）指定單一檔案，例如 `01_inbox/20260226_dev_discuss.md`
- 若未指定，掃描所有 `01_inbox/` 下**未標記 `Triage 狀態：✅ 已處理`** 的檔案

## 執行前檢查
1. 讀取 `00_context/` 下相關背景檔案，確認當前專案主軸。
2. 讀取 `05_product/01_outcomes/01_outcomes.md`，掌握現有 Outcome 編號。
3. 讀取 `05_product/02_opp_and_exp/0_OPP.md`，掌握現有 OPP 編號（避免重複建議）。

## 執行動作

### 1. 掃描未處理 Inbox [autonomous]
讀取目標 Inbox 檔案，逐段解析內容。

### 2. 分類每筆資訊 [autonomous]
將每筆資訊標記為以下四類之一：

| 類型 | 判斷標準 | 建議後續動作 |
|------|---------|------------|
| **事實** | 確認的技術資訊、人員、決策、清單 | 執行 `update-context` |
| **信號** | 潛在問題、機會、風險、使用者需求 | 執行 `move-inbox-to-opp-exp` |
| **行動項目** | 有指定執行人的待辦事項 | 列入 PM Notes 追蹤 |
| **已處理/重複** | 已有對應 OPP 或 context 已涵蓋 | 略過 |

### 3. 標記 Inbox 檔案 [interactive]
PM 確認後，在 Inbox 檔案頂部加入：
```
- **Triage 狀態**：✅ 已處理
- **處理日期**：YYYY-MM-DD
```

### 4. 歸檔流程（每處理一個檔案必走四步）[hybrid]

依共用規範 `PM_Head_work/.ai/knowledge/standards/inbox-mechanism.md`，
任一 inbox 檔案被處理後，**四步缺一不可**：

1. **執行業務動作** — 升級成 OPP / 寫入 context / 搬 02_精選文章 / 執行任務 …
2. **append 處理記錄到 `01_inbox/00-processing-log.md`**
   - 受控動作詞彙（五選一）：
     - `promoted` — 升級為 OPP（去向欄填 OPP 編號）
     - `merged` — 萃取進 `00_context/`（去向欄填目標檔 + 章節）
     - `curated` — 移到 `02_精選文章/`（去向欄填新路徑）
     - `done` — 一次性任務執行完（去向欄填簡短說明）
     - `dropped` — 不再需要（仍 archive 原檔，去向欄填原因）
   - 表格格式：`| YYYY-MM-DD | <原檔名> | <動作> | <去向/說明> | <處理人> |`
   - **append-only** — 不修改既有條目
3. **歸檔原檔** — `mv 01_inbox/<原檔> 01_inbox/_archive/YYYY-MM/`
   - 月份資料夾不存在則建
   - 即使是 `dropped` 也不刪檔，保留原檔留底
4. **重建 `01_inbox/00-inbox-overview.md`**
   - 重新掃描未處理檔（排除 `00-*.md`、`_archive/`、`README.md`）
   - 從 log 抓最近 7 天動作填入 dashboard
   - 計算滯留天數，>30 天者列入「警示」

> 跳過任一步都會讓 overview / log 失真，未來無法追溯。

## 輸出格式
```markdown
### Triage Summary
- 來源：[檔案路徑]
- 日期：[YYYY-MM-DD]
- 掃描項目：[數量]
- 需後續動作：[數量]

### 分類清單

#### 🗂️ 事實（建議 update-context）
- [資訊摘要] → 建議更新至 [context 檔案名稱]

#### 📡 信號（建議 move-inbox-to-opp-exp）
- [信號摘要]
  - 類型：Problem / Opportunity
  - 風險等級：L0 / L1 / L2
  - 可能對齊：OUT-XXX

#### ✅ 行動項目（待 PM 追蹤）
- [執行人]：[事項描述]

#### ⏭️ 已處理/重複（略過）
- [說明略過原因]

### PM Notes
- [需 PM 確認或決定的事項]

### 建議下一步
1. 執行 update-context（若有事實類）
2. 執行 move-inbox-to-opp-exp（若有信號類）
3. 追蹤行動項目（若有）
```

## 禁止事項
- 分類有疑慮時，優先標記為「待 PM 確認」，不自行強分類。
- 「事實」只包含已確認的資訊，討論中的方案不寫入 context。
- 不在 Triage 階段直接寫入任何 context 或 OPP 檔案。
- 同一信號若已有對應 OPP，標為「已處理」，但可備註「有新進展」。

## 品質標準（Quality Gates）
- 每筆資訊都被分類，無遺漏。
- 信號類有初步風險等級判定（L0/L1/L2）。
- 輸出包含明確的下一步建議。
- 每處理一個檔案，`00-processing-log.md` 必有對應條目，`_archive/YYYY-MM/` 必有原檔，`00-inbox-overview.md` 必同步重建。

## 已知限制（Limitations）
- 無法判斷信號的業務重要性排序，需 PM 最終決定。
- 風險等級為初步判定，可能需後續調整。
