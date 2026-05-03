# Agent Coding PM 決策記錄

## ADR-001: 以 PM 原型為基礎，客製 Agent Coding 領域

**日期**：2026-03-14
**決策**：採用 RoleFoundry PM archetype 的 Discovery 三階段流程（Inbox → OPP → Backlog → Handoff），加入 Agent Coding 特有的風險分層（L0/L1/L2）與五大品質門檻（Gate A-E）。
**理由**：Discovery 流程已在 MI_PM、VMS_PM、SmartSignagePM 驗證可行，無需重建。Agent Coding 的獨特性在於品質門檻與動態調整，而非流程結構本身。

## ADR-002: 強調流程動態調整，反對固化

**日期**：2026-03-14
**決策**：將「動態調整優先於完美流程」列為核心信念第一條，並在 anti_patterns 中明確標記「死守初版流程」為最大反模式。
**理由**：Agent Coding 領域變化極快（工具能力、安全威脅、最佳實踐持續演進），流程必須視為可調整的假說。

## ADR-003: OpenAB 本機 Discord ↔ 多 Coding Agent 實證環境

**日期**：2026-05-02
**決策**：在 `/Users/ericmlyang/Coding/openab-work/` 建立 OpenAB v0.8.2 本機測試環境，並排接 Claude Code / Codex / Copilot CLI 三個 ACP backend 對 Discord 的同 channel 共存運行。Runbook 在工作區 `README.md`、安裝計畫在 `~/.claude/plans/work-dir-gentle-kahan.md`。
**理由**：呼應 `00_context/07-工具地圖與防線設計.md` 想驗證的「Coding Agent 多架構並陳」實證材料；同 channel 三隻 bot 可一次比對三家 agent 對相同 prompt 的工具使用、回覆風格、檔案操作差異，遠優於單獨切換 config 的串列測試。
**踩過的坑（記錄供下次參考）**：
- npm 全域裝 ACP adapter (`@agentclientprotocol/claude-agent-acp`、`@zed-industries/codex-acp`) 會被沙盒擋（套件名屬 agent 推得），需使用者手動裝
- Google Drive 路徑下的 OpenAB 不要直接 `cargo build`，rsync 到本機磁碟避開 fsync 風險
- Discord Bot 必須在 Developer Portal **Bot 頁** 啟用 Message Content Intent + Server Members Intent，否則 `Discord rejected privileged intents`
- Bot 加入 server 用 **OAuth2 → URL Generator**（不是 Bot 頁），Token 對 ≠ bot 在 server 裡
- `.env` 必須在跑 OpenAB 的 terminal `source` 過，否則 token 為空 → `Discord rejected bot token`
