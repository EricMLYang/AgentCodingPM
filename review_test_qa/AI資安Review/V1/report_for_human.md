# 🛡️ 安全漏洞檢測報告

**專案名稱**: Marketing-Intelligence-Frontend  
**掃描日期**: 2026-03-31  
**技術棧**: Vue 3 + TypeScript + Pinia + Element Plus + Vite  
**發現漏洞數**: 12 項

---

## 📊 嚴重性摘要

| 等級            | 數量 | 說明                               |
| --------------- | ---- | ---------------------------------- |
| 🔴 **CRITICAL** | 3    | 立即修復，可導致系統被完全接管     |
| 🟠 **HIGH**     | 4    | 儘快修復，存在重大安全風險         |
| 🟡 **MEDIUM**   | 3    | 計劃修復，可能被利用造成影響       |
| 🟢 **LOW**      | 2    | 擇期修復，影響有限但不符合最佳實踐 |

---

## 🔴 CRITICAL 漏洞

### VULN-001: 權限守衛完全被繞過（Permission Guard Disabled）

- **檔案**: `src/middleware/permission.ts` (第 8-12 行)
- **CWE**: CWE-287 (身份驗證不當)
- **OWASP**: A07:2021 – 身份驗證與識別失敗

**問題描述**:  
`setupPermissionGuard()` 在 `main.ts` 中被明確呼叫，但其實作中的路由守衛只是無條件執行 `next()`，完全不做任何驗證。程式碼中留有註解 `// Bypass login check for now`，表示這是暫時措施但從未修復。

```typescript
// ❌ 當前程式碼 — 完全不驗證
router.beforeEach(async (to, from, next) => {
  NProgress.start()
  // Bypass login check for now
  next() // 直接放行！
})
```

**修復建議**: 實作完整的 token 驗證與角色權限檢查，移除 bypass 邏輯。

---

### VULN-002: 偽造 Token — 無真實身份驗證

- **檔案**: `src/stores/user.ts` (第 73-75 行)
- **CWE**: CWE-287
- **OWASP**: A07:2021

**問題描述**:  
登入流程中，token 是在前端用 `'token-' + user.UserId` 直接拼接產生的，完全沒有經過後端簽發或驗證。攻擊者只要知道（或猜到）UserId，就能偽造合法 token。

```typescript
// ❌ 可預測的假 token
token.value = 'token-' + user.UserId // 例如: 'token-mock-admin-001'
```

**修復建議**: Token 必須由後端簽發（JWT / OAuth），前端僅負責存取，不得自行產生。

---

### VULN-003: 路由層級未做授權檢查

- **檔案**: `src/router/index.ts` (第 67-96 行)
- **CWE**: CWE-862 (缺少授權機制)
- **OWASP**: A01:2021 – 存取控制失效

**問題描述**:  
Router guard 只檢查 localStorage 中是否存在 token，不驗證 token 是否合法，也不檢查使用者是否有權限訪問目標路由。`reportPermissions` 僅用於 Sidebar 顯示/隱藏，但不會阻止使用者直接在網址列輸入 URL 進入受限頁面。

**攻擊場景**:

1. 以 "User" 角色登入（只有 Dashboard 權限）
2. 直接在網址列輸入 `/report-permissions`
3. 成功進入報表權限管理頁面 ✅（不應被允許）

**修復建議**: 在 router guard 中加入 `userStore.hasPermission(to.path)` 檢查，無權限時導向 403 頁面。

---

## 🟠 HIGH 漏洞

### VULN-004: 開放重導向（Open Redirect）

- **檔案**: `src/views/LoginView.vue` (第 145-146 行)
- **CWE**: CWE-601

**問題描述**:  
登入成功後，直接讀取 URL query 中的 `redirect` 參數並導航，未做任何驗證。攻擊者可製作釣魚連結：

```
/login?redirect=https://evil-site.com
```

使用者登入後會被導向惡意網站。

**修復建議**: 驗證 redirect 為合法的相對路徑，拒絕含有 `://` 或 `//` 開頭的值。

---

### VULN-005: Token 與敏感資料存於 localStorage

- **檔案**: `src/stores/plugins/persistedState.ts`, `src/utils/request.ts`
- **CWE**: CWE-922 (不安全的敏感資料存儲)

**問題描述**:  
Token、userInfo（含 UserId、Email、DepartmentId）、角色、報表權限等敏感資料全部透過 Pinia 持久化插件存入 localStorage。localStorage 對同源的任何 JavaScript 皆可存取，若發生 XSS 攻擊，所有資料都會洩漏。

**修復建議**: 認證 token 應改用 httpOnly cookie（由後端設定）。必須留在前端的資料考慮加密或使用 sessionStorage。

---

### VULN-006: 缺乏 CSRF 防護

- **檔案**: `src/utils/request.ts`
- **CWE**: CWE-352

**問題描述**:  
Axios 請求攔截器中未設定任何 CSRF token。POST/PUT/DELETE 請求（如建立、修改、刪除報表權限）完全沒有 CSRF 防護。

**修復建議**: 實作 CSRF token 機制，或確保後端使用 SameSite cookie 政策。

---

### VULN-007: Mock 登入後門在所有環境皆啟用

- **檔案**: `src/services/UserService.ts`, `src/stores/user.ts`
- **CWE**: CWE-798 (硬編碼憑證)

**問題描述**:  
`UserService.login()` 中的 mock 帳號回退（MOCK_USERS）和 user store 中的 MOCK_PERMISSIONS 沒有用 `import.meta.env.DEV` 做環境判斷。在生產環境中，若後端暫時無法連線，任何人都能以 'admin' 帳號登入並取得 SysAdmin 權限。

```typescript
// ❌ 沒有環境判斷的 mock 回退
const mockUser = MOCK_USERS[loginId.toLowerCase()]
if (mockUser) {
  ElMessage.warning('後端未連線，已使用離線模式登入')
  return { ...mockUser } // 生產環境也會執行！
}
```

**修復建議**: 所有 mock/fallback 邏輯必須用 `if (import.meta.env.DEV)` 包裹，確保生產環境 API 失敗時直接報錯。

---

## 🟡 MEDIUM 漏洞

### VULN-008: HTML 入口缺少安全標頭

- **檔案**: `index.html`
- **CWE**: CWE-693

缺少 Content-Security-Policy、X-Content-Type-Options 等安全 meta 標籤。建議加入：

```html
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:;"
/>
```

### VULN-009: Token 無過期機制

- **CWE**: CWE-613

偽造的 token 永不過期，也無 refresh token 機制。Response 攔截器未處理 401 狀態碼。被盜的 token 可永久使用。

### VULN-010: 後端錯誤訊息直接顯示給使用者

- **檔案**: `src/utils/request.ts` (第 30-31 行)
- **CWE**: CWE-209

`res.message` 直接顯示在 `ElMessage.error()` 中，可能洩漏內部系統資訊（SQL 片段、堆疊追蹤、檔案路徑）。

---

## 🟢 LOW 漏洞

### VULN-011: 外部圖片無完整性檢查

- **檔案**: `src/layout/Header.vue` (第 51 行)

使用者頭像從外部網域 `wpimg.wallstcn.com` 載入，無 SRI 或 CSP img-src 限制。

### VULN-012: 前端角色自選（無後端驗證）

- **檔案**: `src/views/LoginView.vue` (第 76-79 行)

登入時使用者可自行選擇角色（SysAdmin / Manager / User），後端未驗證。這個下拉選單在生產環境不應存在。

---

## ⚔️ 攻擊鏈分析

### 攻擊鏈 1：完整管理員接管（CRITICAL）

```
攻擊者 → /login 頁面
  → 輸入 'admin' + 選擇 SysAdmin
  → 後端不可用時 mock 登入成功 (VULN-007)
  → 產生假 token 'token-mock-admin-001' (VULN-002)
  → Router guard 只看 token 有無 (VULN-003)
  → 🏴 完整 SysAdmin 存取權！
```

### 攻擊鏈 2：低權限使用者提權（HIGH）

```
正常使用者 → 以 'User' 角色登入
  → 只有 /dashboard 權限
  → 手動輸入 /report-permissions (VULN-003)
  → Sidebar 隱藏但路由未阻擋
  → 🏴 成功存取管理頁面！
```

---

## 📋 修復優先級

| 優先級 | 行動項目                             | 修復漏洞           |
| ------ | ------------------------------------ | ------------------ |
| **P0** | 實作真正的後端 JWT 身份驗證          | VULN-002, VULN-009 |
| **P0** | 在 Router guard 加入路由級別權限檢查 | VULN-003           |
| **P0** | Mock 程式碼限制為開發環境            | VULN-007           |
| **P1** | 移除生產環境的角色自選 UI            | VULN-012           |
| **P1** | 驗證登入後 redirect URL              | VULN-004           |
| **P1** | Token 改存 httpOnly cookie           | VULN-005           |
| **P2** | 實作 CSRF 防護                       | VULN-006           |
| **P2** | 加入 CSP 安全標頭                    | VULN-008           |
| **P2** | Token 過期與 refresh 機制            | VULN-009           |
| **P3** | 錯誤訊息淨化                         | VULN-010           |
| **P3** | 頭像改為內部資源                     | VULN-011           |

---

> **報告產出者**: GitHub Copilot 自動化安全審計  
> **建議**: 此報告涵蓋前端程式碼層面的安全問題。後端 API 的安全性（如 SQL Injection、伺服器端授權）需另行檢測。
