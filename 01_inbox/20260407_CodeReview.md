# 小團隊 Code Review 分級規範 v1

## 分級判定 (依「可逆性 + 影響範圍」)

### 🟢 L1 — 輕量過 (目標:佔 40-50% 的 PR)

**符合條件 (全部要符合)**
- 改動只在單一服務/模組內
- 不碰資料寫入邏輯、不碰 Schema、不碰權限
- 出錯了 5 分鐘內能 revert
- 不是 Databricks 破壞性操作、不是 Agent 工具權限變更

**典型例子**
- 文件、註解、log 訊息、UI 文案
- 純新增測試
- 變數改名、函式抽取等不改行為的 refactor
- Dashboard 顯示邏輯、報表欄位調整
- 非關鍵路徑的 bug fix (例如 log 格式錯誤)

**通過條件**
1. ✅ CI 綠燈 (lint + unit test + 依賴掃描 + secrets 掃描)
2. ✅ Copilot Code Review 無 high severity 問題
3. ✅ PR 作者自己勾完 checklist
4. ✅ **任 1 人 approve** (可以是同團隊任何人,不用逐行讀,看 AI CR 結論 + 測試即可)

---

### 🟡 L2 — 標準審查 (目標:佔 40-50% 的 PR)

**符合條件 (任一即適用)**
- 動到業務邏輯 (但仍在單一服務內)
- 動到資料讀取邏輯 (Silver/Gold 層 transformation)
- 動到 API response 結構但**向後相容**
- 修改既有的 PySpark job 邏輯 (非 schema、非破壞性)
- 改動會影響使用者看到的數據,但錯了可以重跑

**典型例子**
- SmartSignage 的 CMS play data 計算邏輯調整
- 新增一個 explore 層的 transformation
- VMS 的某個 API endpoint 邏輯修改
- 既有 watermark 邏輯的微調

**通過條件**
1. ✅ L1 的所有條件
2. ✅ **新增/更新對應的單元測試** (差異覆蓋率 ≥ 70%)
3. ✅ **1 人 approve + 必須讀過關鍵邏輯** (不是只看 AI CR)
4. ✅ PR 描述要寫:**「我用什麼方式驗證這個改動是對的」** (1-3 行)
5. ✅ Copilot Code Review 指出的問題,作者要在 PR 留言回覆「已處理 / 不適用,因為...」

---

### 🔴 L3 — 嚴格審查 (目標:佔 10-20% 的 PR,但守住 80% 的風險)

**符合條件 (任一即適用)**

**資料層面**
- Schema 變更 (新增欄位以外的任何改動)
- DELETE / DROP / TRUNCATE / VACUUM / 大量 MERGE
- Unity Catalog 權限變更 (GRANT/REVOKE/owner 變更)
- Bronze 層 ingestion 邏輯改動
- 影響 incremental processing 的 watermark 邏輯

**服務層面**
- 跨服務的 API 契約變更 (即使「應該向後相容」)
- 認證/授權邏輯
- 新增外部依賴套件 (尤其是不熟悉的)
- MongoDB index 變更

**Agent / AI 層面**
- PydanticAI agent 的 tool 清單變更
- Agent 可呼叫的權限範圍變更
- Azure OpenAI 整合層的修改
- Prompt template 的重大調整 (影響 production 行為)

**基礎設施**
- CI/CD pipeline 變更
- Databricks Asset Bundle 配置變更
- IAM / secret 管理變更

**通過條件**
1. ✅ L2 的所有條件
2. ✅ **2 人 approve**,其中 1 人必須是你 (Tech Lead) 或指定的 senior
3. ✅ **PR 描述必填四項**:
   - 改動目標 (一句話)
   - 影響範圍 (哪些服務/表/管線)
   - **回滾步驟** (具體到指令)
   - **驗證證據** (擇一或多項):
     - Dry-run 結果截圖
     - Staging 測試結果
     - Spark UI 截圖 (PySpark job)
     - 契約測試結果 (跨服務)
     - 備份確認 (破壞性操作)
4. ✅ 破壞性操作必須有 **dry-run + 備份**
5. ✅ 新依賴必須**手動到 PyPI/npm 確認套件存在且可信** (防幻覺依賴),並在 PR 註明已確認

---

## CI Gate 統一要求 (所有等級都要過)

| Gate | 工具 | 失敗會 block? |
|---|---|---|
| Lint / Format | ruff (Python) / 對應工具 | ✅ Block |
| Unit Test | pytest | ✅ Block |
| 依賴漏洞掃描 | pip-audit + Dependabot | ⚠️ High 以上 block |
| Secrets 掃描 | gitleaks | ✅ Block |
| Copilot Code Review | GitHub 內建 | 🟡 不 block,但 high 必須回覆 |

---

## 5 人小團隊的實務調整

1. **L1 的「任 1 人 approve」可以是非同步的**——不用等 meeting,看到就點。
2. **L3 的「2 人 approve」其中 1 人必須是你**——5 人團隊裡你是 bottleneck,但 L3 應該只佔 10-20%,一天最多 1-2 個,可以接受。
3. **Copilot Code Review 的角色**:當「第 0.5 個 reviewer」,人類 reviewer 看 AI 的結論做判斷,不要逐行重複 AI 做過的事。
4. **遇到分不清是 L2 還是 L3 的**——**升級**。寧願多審一次。
5. **每週五花 15 分鐘** review 這週的 PR 分級對不對,2-3 週後規則會穩定下來。

---

## 一句話原則

> **「L1 信任流程,L2 信任作者,L3 信任不了任何人,要證據。」**

---

要不要我下一步幫你出 **PR template 草稿** (`.github/pull_request_template.md`),把上面三級的 checklist 直接做成可勾選的版本?或是你想先讓團隊試跑這版規範再說?