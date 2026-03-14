---
name: pm-move-inbox-to-opp-exp
description: 將 Inbox 信號升級為 OPP，含 Outcome 對齊檢查。適用於「建 OPP」「這個信號要升級」「move to opp」「信號升級」。
---

# Skill: 移動項目 Inbox → OPP+EXP（含 Outcome 對齊）

## 目標
將一筆 Inbox 信號直接移到 `0_OPP.md`，中間包含 Outcome 對齊檢查。

## 核心原則
- `Outcomes` 是相對穩定內容，預設不修改。
- 只有在「現有 Outcome 無法涵蓋」時，才更新 `05_product/01_outcomes/01_outcomes.md`。

## 輸入
- `signal_id`：例如 `SIG-20260224-012`
- `opp_id`：例如 `OPP-008`
- `title`：項目標題
- `owner`：負責人（可空）
- `reason`：移動原因

## 執行前檢查
1. 讀取 `00_context/` 相關背景檔案，確認方向一致。
2. 讀取 `05_product/01_outcomes/01_outcomes.md`，找出可對齊的 `OUT-###`。
3. 讀取 `05_product/02_opp_and_exp/0_OPP.md`，避免 OPP 編號重複。

## 執行動作

### 1. Outcome 對齊 [autonomous]
- 優先使用既有 `OUT-###`，不改 Outcomes。
- 若現有 Outcome 無法涵蓋，才更新（最小必要修改）。

### 2. 更新 Inbox [autonomous]
在來源信號加註 `狀態: promoted`，並標註對應 `OUT-###`、`OPP-###`。

### 3. 更新 OPP [autonomous]
在 `05_product/02_opp_and_exp/0_OPP.md` 依對應 Outcome 分組插入新 OPP 區塊。
使用 [OPP 區塊模板](templates/opp-block.md) 填寫內容。

### 4. 風險等級判定 [hybrid]
為新 OPP 判定初步風險等級（L0/L1/L2），記錄於 OPP 區塊中。

## 輸出格式
```markdown
### Move Result
- Signal: [signal_id]
- OPP: [opp_id] [title]
- Move: inbox -> opp_exp
- Outcome Link: [OUT-###]
- Risk Level: [L0/L1/L2]
- Status: [identified/experimenting]

### Updated Files
1. [path] - [變更摘要]

### Checks
- Goal Alignment: pass/fail
- Outcome Match: pass/fail
- Duplicate ID: pass/fail
- Risk Level Assigned: pass/fail
```

## 禁止事項
- 不要為了單一信號頻繁改 Outcome。
- 優先維持單檔瀏覽：OPP 只更新 `0_OPP.md`。
- 若信號是對既有 OPP 的「小補充」，不建新 OPP 編號。

## 品質標準（Quality Gates）
- 每個新 OPP 都有風險等級判定。
- Outcome 對齊明確。
- 輸入來源可追溯。

## 已知限制（Limitations）
- 風險等級為初步判定，可能需後續調整。
