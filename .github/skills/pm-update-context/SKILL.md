---
name: pm-update-context
description: 從 Inbox 提取已確認事實回寫 Context 檔案，保持專案背景時效性。適用於「更新背景」「update context」「這個資訊要更新到哪裡」。
---

# Skill: Update Context（更新背景資訊）

## 目標
從 Inbox 信號中提取已確認的事實，回寫到對應的 Context 檔案，保持專案背景資訊的時效性。

## 核心原則
- Context 是穩定文件，只更新「事實性資訊」。
- 信號中的「意見、推測、未定案討論」不寫入 context。

## 輸入
- `signal_source`：Inbox 檔案路徑
- `scope`：（可空）限定只檢查特定 context 檔案

## 執行步驟

### 1. 讀取信號 [autonomous]
讀取指定的 Inbox 信號檔案，提取所有可能屬於 context 的事實性資訊。

### 2. 掃描 Context 檔案 [autonomous]
讀取 `00_context/` 下所有 context 檔案（若有指定 scope 則只讀對應檔案）。

### 3. 比對與分類 [autonomous]
將信號中的事實逐一比對現有 context，分為三類：
- **新增**：context 尚未記錄的事實
- **修正**：與 context 現有內容矛盾或已過時的部分
- **無需變更**：已涵蓋的內容

### 4. 產出變更草案 [interactive]
對每個需要更新的 context 檔案，列出具體變更內容，等待 PM 確認。

### 5. 執行更新 [autonomous]
PM 確認後，執行 context 檔案的更新。

## 輸出格式
```markdown
### Context Update Summary
- Signal Source: [檔案路徑]
- Date: [YYYY-MM-DD]
- Facts Extracted: [數量]
- Updates Proposed: [數量]

### Changes by File
#### [檔案名稱]（新增 / 修正）
- **段落**：[段落名稱]
- **變更**：[具體描述]
- **來源依據**：[信號中的原文摘要]

### Checks
- Facts Only (no opinions): pass/fail
- Decision Log Needed: yes/no
- Contradictions Found: [無 / 描述]
```

## 禁止事項
- 只寫入「已確認的事實」，不寫入推測或討論中的方案。
- 變更前後都要列出，讓 PM 可以快速 review diff。

## 品質標準（Quality Gates）
- 輸入來源明確且可追溯。
- 只包含事實性更新，無意見或推測。
- 變更前後對比清晰。

## 已知限制（Limitations）
- 無法自動判斷「事實」與「推測」的邊界，需 PM 確認。
