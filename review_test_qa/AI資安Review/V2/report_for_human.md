# 🔒 Marketing-Intelligence-Frontend 安全審計報告 v2

> **日期**: 2026-04-09  
> **範圍**: Marketing-Intelligence-Frontend（Vue 3 + TypeScript SPA）  
> **分支**: beta / commit c2b6460 / tag v1.1.0  
> **方法論**: OWASP Top 10 (2021) + OWASP ASVS Level 1  

---

## 📊 總覽

| 嚴重度 | 數量 |
|--------|------|
| 🔴 Critical | 3 |
| 🟠 High | 4 |
| 🟡 Medium | 4 |
| 🟢 Low / Hardening | 3 |
| **合計** | **14** |

### 負面發現（安全的部分）

- ✅ **XSS 模板面**：全專案無 `v-html`、`innerHTML`、`document.write` 使用 — Vue 模板 XSS 攻擊面為零
- ✅ **Sourcemap**：`vite.config.ts` 未啟用 sourcemap，Vite production build 預設不產生 sourcemap — 安全
- ✅ **npm audit**：3 個漏洞（vite path traversal/high、picomatch ReDoS/high、brace-expansion DoS/moderate）全部在 devDependencies，不影響執行期

---

## 🔴 Critical 發現

### VULN-001：Fallback Token 可預測 — 攻擊者可偽造認證狀態

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🔴 Critical |
| **信心度** | Confirmed |
| **OWASP** | A07:2021 Identification and Authentication Failures |
| **CWE** | CWE-798 |
| **檔案** | `src/stores/user.ts` : Line 62 |

**前提條件**：無。僅需瀏覽器 DevTools 即可利用。後端是否驗證 token 為獨立防線——若後端也未驗證則為完全接管；若後端驗證 JWT 簽章則僅繞過前端 UI 保護。

**說明**：  
當後端未回傳 JWT Token 時，前端用 `'token-' + user.UserId` 產生可預測的假 token。router guard 僅檢查 token 非空，此假 token 通過所有前端檢查。

```typescript
token.value = user.Token ?? 'token-' + user.UserId
```

**PoC**：
1. 開啟瀏覽器 DevTools Console
2. 執行：`localStorage.setItem('user', JSON.stringify({token:'token-EMP001'}))`
3. 重新整理頁面
4. **結果**：繞過登入頁，直接進入系統

**影響**：前端認證完全繞過。若後端 API 也未驗證 JWT 簽章，則為完全系統接管。

**修復建議**：移除 fallback token。後端未回傳有效 JWT 即視為登入失敗。

---

### VULN-002：Router Guard 僅檢查 Token 存在性，不驗證有效性

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🔴 Critical |
| **信心度** | Confirmed |
| **OWASP** | A01:2021 Broken Access Control |
| **CWE** | CWE-602 |
| **檔案** | `src/router/index.ts` : Lines 121-150 |

**前提條件**：無。任何人可在瀏覽器 Console 執行 PoC。

**說明**：  
路由守衛從 localStorage 讀取 token，僅檢查是否非空。不驗證 JWT 格式、簽章、過期時間。任何非空字串即可通過。

```typescript
const userState = JSON.parse(localStorage.getItem('user') || 'null')
token = userState?.token || null
if (!token) { next({ path: '/login' }) }
next()
```

**PoC**：
1. 開啟瀏覽器 DevTools Console
2. 執行：`localStorage.setItem('user', JSON.stringify({token:'x'}))`
3. 執行：`location.reload()`
4. **結果**：成功進入系統主畫面，繞過登入

**影響**：前端認證完全繞過。任何非空字串作為 token 即可存取所有受保護路由。

**修復建議**：  
1. 至少解析 JWT exp claim 並驗證未過期
2. Axios response interceptor 處理 401 時清除 token 並導向登入
3. 長期改為 httpOnly cookie

---

### VULN-003：登入僅需帳號不需密碼 — 零因素認證

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🔴 Critical |
| **信心度** | Confirmed |
| **OWASP** | A07:2021 Identification and Authentication Failures |
| **CWE** | CWE-306 |
| **檔案** | `src/views/LoginView.vue` : Line 171 |

**前提條件**：需知道目標員工的 loginId。loginId 通常為工號，可從名片、郵件簽名檔、LDAP 查詢等途徑取得。

**說明**：  
登入流程僅需 username（loginId），無密碼欄位。API 呼叫 `GET /v1/Auth/login-url-db?loginId=xxx`，後端回傳用戶資訊與 token。無密碼、無 MFA、無帳號鎖定、無登入頻率限制。

```typescript
await userStore.login(loginForm.value.username)
// → loginUrlDb(loginId) → GET /v1/Auth/login-url-db?loginId=...
```

**PoC**：
1. 在登入頁 username 欄位輸入任意已知員工帳號
2. 點擊登入
3. **結果**：無需密碼即成功登入，取得該員工 token 與完整個人資訊

**影響**：任何知道員工帳號的人可冒充該員工。若為外部可觸及的系統，風險極高。

**修復建議**：整合企業 SSO/OIDC（如 Azure AD Entra ID）或加入密碼驗證。配置帳號鎖定與登入頻率限制。

---

## 🟠 High 發現

### VULN-004：Permission Guard 形同虛設 + 無角色路由保護

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟠 High |
| **信心度** | Confirmed |
| **OWASP** | A01:2021 Broken Access Control |
| **CWE** | CWE-862 |
| **檔案** | `src/middleware/permission.ts` : Lines 8-12 |
| **關聯** | `src/router/index.ts` : Lines 121-150 |

**前提條件**：需已登入（持有任意 token）。結合 VULN-001 或 VULN-002 可連帳號都不需要。

**說明**：  
`permission.ts` 中的 `router.beforeEach` 無條件呼叫 `next()`（註解：`Bypass login check for now`）。`router/index.ts` 的 guard 僅檢查 token 非空，不檢查角色。`permissionStore.hasRoutePermission()` 已實作但從未被路由守衛呼叫。所有路由對任何持 token 者完全開放。

```typescript
// src/middleware/permission.ts
router.beforeEach(async (to, from, next) => {
    NProgress.start()
    // Bypass login check for now
    next()
})
```

**PoC**：
1. 以任意角色登入（例如僅有 dashboard 權限的角色）
2. 在網址列直接輸入 `/roles`
3. **結果**：成功進入角色管理頁面，可修改全系統角色權限

**影響**：任何已登入使用者可存取所有管理功能，進行越權操作。

**修復建議**：在 `permission.ts` 中整合 `permissionStore.hasRoutePermission(to.path)`，無權限時導向 `/not-authorized`。

---

### VULN-005：客戶端角色可竄改 — 前端權限提升

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟠 High |
| **信心度** | Confirmed |
| **OWASP** | A01:2021 Broken Access Control |
| **CWE** | CWE-639 |
| **檔案** | `src/views/LoginView.vue` : Lines 178-189 |

**前提條件**：需已登入。若後端 API 獨立驗證角色（從 JWT claims 取而非信任前端），則此漏洞僅影響 UI 層；若後端信任前端傳的角色參數，則為完整權限提升。

**說明**：  
EAP 角色在前端寫入 localStorage（由 Pinia 持久化插件）。使用者可透過 DevTools 修改角色陣列。前端選單渲染與 permissionStore 全部依賴此客戶端數據。MOCK_PROFILES 在 JS bundle 中暴露所有有效角色名稱。

```typescript
userStore.setEapRoles(roles) // → 持久化到 localStorage.user.eapRoles
```

**PoC**：
1. 以普通角色登入
2. 在 Console 執行：
   ```javascript
   const s = JSON.parse(localStorage.getItem('user'));
   s.eapRoles = ['ADP_SysAdmin','ADP_roles','ADP_reportPerm','ADP_salesEntry'];
   localStorage.setItem('user', JSON.stringify(s));
   ```
3. 執行：`location.reload()`
4. **結果**：Sidebar 顯示完整系統管理員選單

**影響**：前端 UI 權限提升，可見所有管理功能。若後端也依賴前端傳遞的角色資訊則為完整權限提升。

**修復建議**：角色必須由後端 JWT claims 攜帶，前端不可修改。敏感 API 後端必須從 JWT 解讀角色，不信任前端參數。

---

### VULN-006：動態注入第三方 Script 無 SRI — 供應鏈 XSS 向量

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟠 High |
| **信心度** | Confirmed |
| **OWASP** | A08:2021 Software and Data Integrity Failures |
| **CWE** | CWE-829 |
| **檔案** | `src/views/ShellView.vue` : Lines 101-111 |

**前提條件**：需 CDN (`d2w8pnc8os8ipq.cloudfront.net`) 被入侵，或 DNS 劫持，或中間人攻擊。雖為 HTTPS 但 CloudFront 配置錯誤或帳號被盜仍可能發生。

**說明**：  
ShellView 每次頁籤切換都移除舊 `<script>` 再重新 `createElement('script')` 注入 `embed.js`。無 SRI integrity 屬性、無 CSP nonce。此函數在頁面生命週期中被多次呼叫。

```typescript
function reloadEmbedScript() {
  const existing = document.querySelector('script[data-embed-script="true"]')
  if (existing) existing.remove()
  const script = document.createElement('script')
  script.src = 'https://d2w8pnc8os8ipq.cloudfront.net/embed.js'
  document.head.appendChild(script)
}
```

**PoC**：無法在本地重現（需入侵 CDN）。但可驗證風險存在：在 DevTools Network 中觀察 embed.js 載入無 integrity check。

**影響**：若 CDN 被入侵，攻擊者可在所有存取 ShellView 的使用者瀏覽器執行任意 JavaScript，竊取 localStorage 全部資料（token、PII、角色）。

**修復建議**：  
1. 加入 SRI integrity 屬性
2. 在 CSP 的 `script-src` 中限制允許的來源
3. 考慮將 embed.js 下載到本地 bundle 或使用 SDK API

---

### VULN-007：Mock Data 與硬編碼帳號殘留於生產 Bundle

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟠 High |
| **信心度** | Confirmed |
| **OWASP** | A07:2021 Identification and Authentication Failures |
| **CWE** | CWE-798 |
| **檔案** | `src/services/UserService.ts` : Lines 16-23<br>`src/api/menuApi.ts` : Lines 132-179 |

**前提條件**：無。攻擊者只需開啟 DevTools Sources 或從 JS bundle 搜尋即可取得。

**說明**：  
多處 mock/測試程式碼未用 `import.meta.env.DEV` 保護：
1. `UserService.getUserProfile()` 硬編碼回傳 Admin 帳號 (`admin@example.com`)
2. `menuApi.ts` 的 `MOCK_PROFILES` 暴露完整角色名稱與權限對應
3. 多個 View 包含大量假資料
4. 唯一使用 `import.meta.env.DEV` 的 `main.ts` 裡的區塊是空的

```typescript
// UserService.ts — 硬編碼帳號
return Promise.resolve({
  id: 1, username: 'Admin', email: 'admin@example.com', roles: ['admin']
})

// menuApi.ts — 暴露角色結構
export const MOCK_PROFILES: Record<string, MockProfile> = {
  ADP_SysAdmin: { label: 'ADP 系統管理員', roles: ['ADP_dashboard', 'ADP_roles', ...] },
  ...
}
```

**PoC**：
1. 開啟 DevTools → Sources
2. 搜尋 `MOCK_PROFILES` 或 `admin@example.com`
3. **結果**：取得完整角色名稱清單，可用於構造 VULN-005 的 localStorage 竄改

**影響**：降低攻擊成本。攻擊者無需猜測即可取得所有有效角色名稱及其權限結構。

**修復建議**：  
1. 所有 mock 邏輯包裹在 `if (import.meta.env.DEV)` 中，確保 tree-shaking 在生產 build 移除
2. `MOCK_PROFILES` 不應 export
3. `UserService.getUserProfile()` 的 mock 應移除

---

## 🟡 Medium 發現

### VULN-008：Open Redirect — 登入後重導向未驗證

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟡 Medium |
| **信心度** | Likely |
| **OWASP** | A01:2021 Broken Access Control |
| **CWE** | CWE-601 |
| **檔案** | `src/views/LoginView.vue` : Line 194 |

**前提條件**：需誘導使用者點擊構造的 URL（社交工程）。Vue Router 行為上可能將 `https://evil.com` 解讀為內部路徑而非外部 URL，需實際測試確認。

**說明**：  
登入成功後從 `route.query.redirect` 取值並直接 `router.push()`，未驗證是否為同域路徑。

```typescript
const redirect = (route.query.redirect as string) || '/'
router.push(redirect)
```

**PoC**：
1. 構造 URL：`https://your-app.com/login?redirect=https://evil.com`
2. 誘導使用者透過此 URL 登入
3. 需測試確認：Vue Router 是否將外部 URL 作為路由路徑處理

**影響**：若可重現，攻擊者可利用合法登入頁面將使用者重導向到釣魚站。

**修復建議**：驗證 redirect 必須以 `/` 開頭且不包含 `://` 或 `//`。

---

### VULN-009：Token 無過期檢查 — ExpiresAt 欄位被忽略

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟡 Medium |
| **信心度** | Confirmed |
| **OWASP** | A07:2021 Identification and Authentication Failures |
| **CWE** | CWE-613 |
| **檔案** | `src/stores/user.ts` : Lines 11, 55-62 |
| **關聯** | `src/types/models.d.ts` (AppEmpLoginResponse.ExpiresAt) |

**前提條件**：需 token 已被竊取（透過 XSS 或其他方式），且後端 JWT 驗證未啟用或寬鬆。

**說明**：  
`AppEmpLoginResponse` 定義了 `ExpiresAt` 欄位，但 user store 的 `login()` 從未讀取或儲存此值。前端不檢查 JWT 過期時間。Axios interceptor 未處理 401 狀態碼。localStorage 中的 token 永久有效直到使用者手動登出。

```typescript
// types/models.d.ts
ExpiresAt?: string  // ← 有定義

// stores/user.ts login()
token.value = user.Token ?? 'token-' + user.UserId  // ← 未讀取 ExpiresAt
```

**影響**：被竊取的 token 可無限期使用。帳號被盜時無法透過 token 過期自動止損。

**修復建議**：  
1. `login()` 中記錄 ExpiresAt，定時檢查並在過期時自動登出
2. Axios response interceptor 處理 401 回應
3. 實作 refresh token 機制

---

### VULN-010：API 路徑參數未 encodeURIComponent

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟡 Medium |
| **信心度** | Likely |
| **OWASP** | A03:2021 Injection |
| **CWE** | CWE-74 |
| **檔案** | `src/api/userApi.ts` : Lines 38-41 |

**前提條件**：需 roleName 包含 `/` 或特殊字元。roleName 通常為後端定義的固定值（如 `ADP_SysAdmin`），但若使用者可控制此值則風險提升。

**說明**：  
`getReportPermissions()` 將 roleName 直接字串拼接到 URL 路徑，未用 `encodeURIComponent`。若 roleName 含 `/` 可造成非預期的 API 路徑解析。

```typescript
export const getReportPermissions = (roleName: string) => {
  return request.get(`${Api.GetReportPermissions}/${roleName}`)
}
```

**影響**：取決於後端路由設計。可能導致 API 路由混亂或觸及非預期端點。

**修復建議**：使用 `encodeURIComponent(roleName)`。

---

### VULN-011：RedirectView 路徑參數未驗證

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟡 Medium |
| **信心度** | Likely |
| **OWASP** | A01:2021 Broken Access Control |
| **CWE** | CWE-601 |
| **檔案** | `src/views/RedirectView.vue` : Lines 8-11 |

**前提條件**：需使用者存取 `/redirect/...` 路由。此路由用於內部重導向，通常不由使用者直接觸發，但可被手動構造。

**說明**：  
從路由 params 取 path 並直接 `router.replace('/' + path)`，未驗證 path 是否匹配合法路由。

```typescript
const { path } = params
router.replace({ path: '/' + (Array.isArray(path) ? path.join('/') : path), query })
```

**影響**：可能被用於非預期的內部路由導向。影響有限（Vue Router 不會導向外部 URL）。

**修復建議**：驗證 path 必須存在於已知路由列表中。

---

## 🟢 Low / Hardening 發現

### VULN-012：敏感資料明文存於 localStorage — XSS 風險放大器

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟢 Low |
| **信心度** | Confirmed |
| **OWASP** | A02:2021 Cryptographic Failures |
| **CWE** | CWE-922 |
| **檔案** | `src/stores/plugins/persistedState.ts` : Lines 27-33 |

**前提條件**：需存在同域 XSS 漏洞才能被直接利用。單獨此項不構成可利用漏洞，但大幅放大 XSS 的影響。

**說明**：  
以下資料以 JSON 明文存於 localStorage：JWT token、完整 PII（UserId, Account, RealName, DepartmentId, Email, EmployeeId, CompanyName）、EAP 角色陣列、報表權限清單。本專案未發現 `v-html` 或 `innerHTML` 使用（XSS 模板面安全），但 VULN-006 的第三方 script 構成潛在 XSS 向量。

```typescript
user: { paths: ['userInfo', 'token', 'eapRoles', 'reportPermissions'] }
```

**影響**：風險放大器 — 若發生 XSS（如 VULN-006），攻擊者可一次竊取所有敏感資料。localStorage 跨分頁共享且不會自動過期。

**修復建議**：  
1. token 改為 httpOnly secure cookie
2. PII 不持久化或僅保留必要最小欄位
3. 結合 CSP 降低 XSS 發生機率

---

### VULN-013：缺少安全 HTTP Headers — CSP / X-Frame-Options 等

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟢 Low |
| **信心度** | Context-dependent |
| **OWASP** | A05:2021 Security Misconfiguration |
| **CWE** | CWE-693 |
| **檔案** | `vite.config.ts` |

**前提條件**：需確認正式部署環境（Nginx / CDN / Cloud provider）是否已在反向代理層配置這些 headers。若已配置則此項不成立。

**說明**：  
前端碼中未見安全 headers 相關設定（CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy）。`vite.config.ts` 未配置 `server.headers`。需檢查正式環境 response headers 才能確認。

**影響**：若生產環境也未配置：缺少 CSP 使 VULN-006 的 XSS 風險無法被瀏覽器阻擋。缺少 X-Frame-Options 可能被 clickjacking。

**修復建議**：  
1. 確認生產環境反向代理已配置安全 headers
2. 若無法確認，在 `index.html` 中加入 meta 標籤作為 fallback
3. 建議至少配置：CSP, `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`

---

### VULN-014：CSRF 風險評估（Context-dependent）

| 屬性 | 值 |
|------|-----|
| **嚴重度** | 🟢 Low |
| **信心度** | Context-dependent |
| **OWASP** | A01:2021 Broken Access Control |
| **CWE** | CWE-352 |
| **檔案** | `src/utils/request.ts` : Lines 1-48 |

**前提條件**：當前使用 header-based Bearer token 認證（`Authorization: Bearer xxx`），瀏覽器不會自動附帶此 header 在跨站請求中，因此傳統 CSRF 攻擊不適用。僅在改為 cookie-based auth 時需關注。

**說明**：  
Axios 未配置 `xsrfCookieName` / `xsrfHeaderName`。POST/PUT/DELETE 請求無 CSRF token。但因認證方式為手動設定的 Authorization header，CSRF 風險極低。

```typescript
config.headers['Authorization'] = `Bearer ${token}`  // header-based, 非 cookie
```

**影響**：目前架構下 CSRF 風險極低。僅在改為 cookie-based auth 時需重新評估。

**修復建議**：維持 header-based token 認證即可。若未來改用 cookie，需配置 `SameSite=Strict` 及 CSRF token。

---

## 🔗 根因分組

### Group A：認證模型未正式化 — 前端可自決身份

> **涉及漏洞**：VULN-001, VULN-002, VULN-003

登入不需密碼、token 可偽造、token 有效性不驗證。整體認證流程停留在開發/測試模式，前端完全控制身份狀態。

**根本修復**：整合企業 SSO/OIDC 或完整的帳號密碼認證。後端簽發 JWT 並在每次 API 呼叫時驗證簽章與過期時間。

---

### Group B：授權僅做 UI 層，無 enforcement

> **涉及漏洞**：VULN-004, VULN-005

路由守衛形同虛設（permission.ts bypass + router guard 不檢查角色）。角色存在可竄改的 localStorage。`permissionStore.hasRoutePermission()` 已寫好但未被路由守衛呼叫。

**根本修復**：在 `permission.ts` 中啟用 `hasRoutePermission()` 檢查。角色應來自後端 JWT claims，不依賴前端 localStorage。

---

### Group C：開發/測試程式碼殘留於生產路徑

> **涉及漏洞**：VULN-007

MOCK_PROFILES、硬編碼 Admin 帳號、多頁面假資料均未用 `import.meta.env.DEV` 保護，會被打包進生產 bundle。

**根本修復**：所有 mock 邏輯用 `if (import.meta.env.DEV)` 包裹。敏感常量不 export。

---

### Group D：前端安全加固不足 — XSS 防線薄弱

> **涉及漏洞**：VULN-006, VULN-012, VULN-013

第三方 script 無 SRI、localStorage 存敏感資料（XSS 放大器）、安全 headers 未確認。雖然 Vue 模板無 `v-html`/`innerHTML`（XSS 模板面安全），但動態 script 注入構成替代攻擊面。

**根本修復**：embed.js 加 SRI。配置 CSP。token 改 httpOnly cookie。確認生產環境安全 headers。

---

### Group E：輸入驗證與導向邏輯

> **涉及漏洞**：VULN-008, VULN-009, VULN-010, VULN-011

Open redirect、API 路徑參數未編碼、RedirectView 未驗證、Token 不檢查過期。影響有限但應作為防禦縱深修復。

**根本修復**：所有外部輸入驗證後使用。redirect 白名單檢查。路徑參數 encodeURIComponent。Token 過期檢查。

---

## ⚔️ 攻擊鏈

### 攻擊鏈 1：零知識完全存取（無需帳號密碼）

```
攻擊者（零知識）
  ↓ 開啟 DevTools Console
  ↓ localStorage.setItem('user', JSON.stringify({
       token:'fake',
       eapRoles:['ADP_SysAdmin','ADP_roles','ADP_reportPerm']
    }))
  ↓ location.reload() → 繞過登入（VULN-002）
  ↓ 以 SysAdmin 角色渲染 UI（VULN-005）
  ↓ 存取 /roles → 無路由攔截（VULN-004）
  ↓ 可修改角色權限、查看所有報表
  ▼
  前端完全接管
```

**利用漏洞**：VULN-001, VULN-002, VULN-004, VULN-005  
**後端依賴**：若後端驗證 JWT 簽章，攻擊者只能操作前端 UI，API 呼叫會被 401 拒絕。

---

### 攻擊鏈 2：帳號冒充（已知 loginId）

```
攻擊者（知道 loginId）
  ↓ 取得目標 loginId（社交工程）
  ↓ 在登入頁輸入帳號 → 不需密碼即登入（VULN-003）
  ↓ 後端回傳真實 JWT + 完整個人資訊
  ↓ 取得目標員工的身份與權限
  ▼
  完整冒充目標員工（真實 JWT，不受前端限制）
```

**利用漏洞**：VULN-003  
**後端依賴**：此鏈不依賴前端漏洞，是後端認證不足。

---

### 攻擊鏈 3：XSS 供應鏈 → 批量竊取

```
攻擊者（入侵 CDN / DNS 劫持）
  ↓ 替換 embed.js 為惡意腳本（無 SRI：VULN-006）
  ↓ 所有存取 ShellView 的使用者自動載入
  ↓ 惡意腳本讀取 localStorage（VULN-012 放大）
  ↓ 竊取 token、PII、角色資訊
  ↓ 缺乏 CSP 無法阻擋（VULN-013）
  ▼
  批量竊取全部使用者認證資料
```

**利用漏洞**：VULN-006, VULN-012, VULN-013  
**後端依賴**：竊取的是真實 JWT token，後端無法區分合法與惡意使用。

---

## 📋 優先修復順序

| 優先級 | 行動 | 解決漏洞 | 根因組 | 工作量 |
|--------|------|----------|--------|--------|
| **P1** | 正式化認證模型：整合 SSO/OIDC 或帳號密碼、移除 fallback token、後端 API 全面驗證 JWT | VULN-001, 002, 003 | A | 大 |
| **P2** | 啟用前端路由守衛角色檢查：permission.ts 呼叫 `hasRoutePermission()`，角色來源改為 JWT claims | VULN-004, 005 | B | 中 |
| **P3** | 加固供應鏈安全：embed.js 加 SRI、配置 CSP、token 改 httpOnly cookie | VULN-006, 012, 013 | D | 中 |
| **P4** | 清理生產 bundle：mock 用 `import.meta.env.DEV` 包裹、移除硬編碼帳號 | VULN-007 | C | 小 |
| **P5** | 輸入驗證與防禦縱深：redirect 白名單、路徑參數編碼、Token 過期檢查 | VULN-008, 009, 010, 011 | E | 小 |

---

> **報告結束** | 審計日期：2026-04-09 | 報告版本：v2.0
