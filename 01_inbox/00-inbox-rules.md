---
title: Inbox 使用規範
last_review: 2026-04-26
---

# 01_inbox — 短期信號池規範

## 定位

Inbox 是**未消化信號的暫存區**，不是長期知識庫。

長期知識在 `00_context/`；歷史原檔在 `02_精選文章/`。

## 內容類型

只放這四類：

1. **任務派發**：PM 總管 / 跨 Repo 給的 dispatch
2. **會議片段**：當下記下、還沒整理的會議重點
3. **未分類碎片**：看到一條訊息、一個連結、想到一個點子
4. **外部觀察待消化**：FB / 部落格剪貼，還沒判斷怎麼用

## 規則

| 規則 | 說明 |
|------|------|
| 單檔 ≤ 5KB | 超過代表已經不是「信號」是「整理過的內容」，要分流 |
| 命名 `YYYYMMDD_<topic-slug>.md` | 日期前綴 + 一句話標題 |
| 每月清空一次 | 月底 review，每個檔案決定下一站 |
| Frontmatter 必填 | `status: raw / processing / promoted / archived` |

## 消化路徑（每個檔案要走完一條）

```
raw signal
   │
   ▼
processing （我正在判斷這要怎麼用）
   │
   ├──► 升級成 Context  → 改寫進 00_context/ 對應主題檔 → 原稿如有外部引用價值搬 02_精選文章/，否則直接刪
   ├──► 純參考價值     → 搬 02_精選文章/（要符合精選條件，見該資料夾 README）
   ├──► 升級成 OPP     → 進 05_product/opps/ 走 Discovery 流程
   ├──► 一次性任務     → 執行完直接刪
   └──► 不再需要       → 直接刪
```

## Frontmatter 範例

```yaml
---
title: <一句話描述>
date: 2026-04-25
status: raw
type: dispatch | meeting | fragment | external-obs
source: <誰給的 / 從哪看到>
next-action: <你預期接下來要做什麼>
---
```

## 不要做的事

- ❌ 不要把成熟規範放在 inbox（要搬進 `00_context/`）
- ❌ 不要把單檔養超過 1 個月（不是停止思考就是該升級）
- ❌ 不要把外部報告原文放這裡（要嘛萃取進 `00_context/`，要嘛搬 `02_精選文章/`）
- ❌ 不要當作待辦清單（待辦用 `15_management/` 或 TaskCreate）

## 給 Agent 的指引

- 載入專案 context 時**不要**載入 `01_inbox/` 內容（除非使用者明示要處理 inbox）
- 處理 inbox 時優先用 `pm-triage-inbox` skill
- 升級 context 時優先用 `pm-update-context` skill
