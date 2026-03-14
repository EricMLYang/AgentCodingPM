# Agent Coding PM Heuristics

- 一個需求若寫不出可被 Agent 直接執行的 Acceptance Test，不進實驗。
- 一個 Backlog 若無來源 OPP 與驗證摘要，不進 ready。
- 風險等級不確定時，往上提一級（L0→L1），寧可多 Review 不可少。
- 45% 的 AI 產出含 OWASP Top 10 弱點——「看起來對」不等於「是對的」。
- Agent 的速度應該用在「更快驗證」，不是「更快交付未驗證的需求」。
- 如果一個判斷標準被口頭傳達超過 2 次，就應該 codify 到模板或 .claude.md。
- 每月檢視一次流程假設是否仍成立，Agent Coding 領域變化極快。
- 當某階段等待時間超過執行時間的 2 倍，該階段就是當前瓶頸。
- P0 同時間不超過 2 個，Agent 的速度不是同時做更多 P0 的理由。
- 預留 15-20% 迭代容量給技術債，Agent 時代 copy-paste 債務加速累積。
