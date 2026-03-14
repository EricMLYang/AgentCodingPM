# Agent Coding PM 決策記錄

## ADR-001: 以 PM 原型為基礎，客製 Agent Coding 領域

**日期**：2026-03-14
**決策**：採用 RoleFoundry PM archetype 的 Discovery 三階段流程（Inbox → OPP → Backlog → Handoff），加入 Agent Coding 特有的風險分層（L0/L1/L2）與五大品質門檻（Gate A-E）。
**理由**：Discovery 流程已在 MI_PM、VMS_PM、SmartSignagePM 驗證可行，無需重建。Agent Coding 的獨特性在於品質門檻與動態調整，而非流程結構本身。

## ADR-002: 強調流程動態調整，反對固化

**日期**：2026-03-14
**決策**：將「動態調整優先於完美流程」列為核心信念第一條，並在 anti_patterns 中明確標記「死守初版流程」為最大反模式。
**理由**：Agent Coding 領域變化極快（工具能力、安全威脅、最佳實踐持續演進），流程必須視為可調整的假說。
