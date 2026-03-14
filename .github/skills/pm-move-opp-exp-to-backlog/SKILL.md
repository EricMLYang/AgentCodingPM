---
name: pm-move-opp-exp-to-backlog
description: 將驗證完成的 OPP 移入 Backlog，補齊 RICE 與 CoD 評分。適用於「移到 backlog」「OPP 驗證完了」「move to backlog」。
---

# Skill: 移動項目 OPP+EXP → Backlog

## 目標
將驗證完成的 OPP 移入 Backlog，並補齊評分與日期欄位。

## 輸入
- `item_id`：例如 `OPP-003`
- `title`：項目標題
- `outcome_link`：例如 `OUT-001`
- `reason`：移動原因（必須是驗證證據）
- `rice`：RICE 分數
- `cod_flag`：High | Medium | Low
- `created_date`：YYYY-MM-DD
- `handoff_date_plan`：YYYY-MM-DD（可空）

## 執行前檢查
1. 讀取相關背景檔案，確認方向一致。
2. 讀取 `05_product/02_opp_and_exp/0_OPP.md`，確認該項有驗證結論。
3. 確認狀態可更新為 `validated`。
4. 檢查 Backlog 必填欄位是否齊全。

## 執行動作

### 1. 更新 OPP 狀態 [autonomous]
在 `0_OPP.md` 將狀態改為 `validated`。

### 2. 追加到 Backlog [autonomous]
追加到 `05_product/03_backlog/00_backlog_list.md`（含 Created Date / Handoff Date / RICE / CoD）。

### 3. 歸檔詳細內容 [autonomous]
如有詳細長文，移至 `05_product/02_opp_and_exp/history/`。

## 輸出格式
```markdown
### Move Result
- Item: [item_id] [title]
- Move: opp_exp -> backlog
- Outcome Link: [OUT-###]
- Risk Level: [L0/L1/L2]
- Status: validated

### Updated Files
1. [path] - [變更摘要]

### Checks
- Evidence Ready: pass/fail
- Backlog Required Fields: pass/fail
- Quality Gates Defined: pass/fail
```

## 禁止事項
- 不可跳過驗證直接進 Backlog。
- 若驗證不足，輸出缺漏項，不要執行移動。

## 品質標準（Quality Gates）
- 有驗證證據支持移動。
- Backlog 必填欄位齊全。
- 風險等級與對應品質門檻已定義。

## 已知限制（Limitations）
- RICE 分數為粗估，最終需團隊確認。
