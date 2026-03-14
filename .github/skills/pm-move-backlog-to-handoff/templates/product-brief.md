# Product Brief 模板

在 `05_product/03_backlog/product_brief/` 建立檔案，命名為 `{item_id}_{簡短英文標題}.md`。

```md
# Product Brief: {item_id} — {標題}

- 狀態: handed-off
- Handoff Date: {YYYY-MM-DD}
- 來源 OPP: {OPP ID}
- 對齊 OUT: {OUT ID}
- 風險等級: {L0/L1/L2}
- RICE: {分數}
- CoD Flag: {High/Medium/Low}

---

## 1. Problem Statement
- 要解決什麼問題／把握什麼機會
- 目前現況與痛點描述
- 不做會怎樣（影響範圍）

## 2. Target Users & Scope
- 目標使用者角色與人數
- 本次交付範圍（In Scope / Out of Scope）

## 3. Outcome & Success Metrics
- 對齊的 Outcome（引用 OUT 編號）
- 成功指標（可量化）
- 如何衡量（資料來源、頻率）

## 4. Evidence Summary
- 驗證方法與結果摘要（引用 OPP 編號）
- 關鍵數據或使用者回饋
- 信心水準（High / Medium / Low）與依據

## 5. User Stories / 功能需求
- 格式：「身為 {角色}，我希望 {功能}，以便 {價值}」
- 標註優先級（Must / Should / Could）
- AC 需可直接轉為自動測試

## 6. 技術背景與限制
- 相關系統架構
- 已知技術限制或相依性
- 需要的基礎建設或環境準備

## 7. 品質門檻（依風險等級）
- Gate A（供應鏈安全）：[適用檢查項]
- Gate B（Agent 工具安全）：[適用檢查項]
- Gate C（系統正確性）：[適用檢查項]
- Gate D（程式碼品質）：[適用檢查項]
- Gate E（Agentic 工作流）：[適用檢查項]

## 8. Assumptions & Risks
- 尚未驗證的假設
- 已識別風險與建議緩解方式
- 相依項目或外部因素

## 9. Recommended Scope & 階段建議
- 建議的 MVP 範圍
- 可拆分的階段（Phase 1 / 2 / ...）

## 10. Open Questions for Engineering
- 需要工程團隊在 PDR 釐清的問題
- 需要 Technical Spike 的項目

## 11. 相關資源
- 相關文件連結
- 參考的 decisions.md 條目
```
