---
name: design-experiment
description: 為特定 OPP 設計低成本驗證，並更新 0_OPP.md 的驗證方式與成功標準。適用於「設計驗證」「這個怎麼測」等。
---

# Skill: Design Experiment（驗證實驗設計）

## 目標
為指定 OPP 設計可快速執行、可判斷通過與否的驗證方案。

## 流程

### Step 1: 確認對象 [interactive]
確認 `OPP-###` 與最大風險類型。

### Step 2: 讀取背景 [autonomous]
讀取相關背景檔案與 OPP 現況。

### Step 3: 設計驗證 [autonomous]
- 驗證方式（訪談、技術 spike、資料品質測試、營運試跑等）
- 假說（至少 X% 的 Y 會 Z）
- 成功標準
- 失敗標準
- 下一步

### Step 4: 回寫 OPP [autonomous]
更新 `0_OPP.md` 對應區塊的驗證方式、成功標準、下一步。

## 禁止事項
- 不可設計沒有門檻的驗證。
- 不可跳過 OPP 對齊直接提案。
- 不可把結果判讀權限替代 PM。

## 品質標準（Quality Gates）
- 驗證有明確的成功/失敗門檻。
- 驗證方式與風險等級匹配。

## 已知限制（Limitations）
- 驗證結果的判讀需 PM 最終確認。
