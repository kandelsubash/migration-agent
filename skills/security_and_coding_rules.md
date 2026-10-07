# Angular 22 + Security Coding Standards (SEC-01 to SEC-14)

> Dense reference for Claude 3.5 Sonnet. Enforce all rules during code generation.

---

## 1. Angular 22 Architecture Standards
1. **Standalone Components:** `standalone: true` on all components. No NgModules.
2. **OnPush Change Detection:** `changeDetection: ChangeDetectionStrategy.OnPush` mandatory everywhere.
3. **Signals for Local State:** Use `signal<T>()`, `computed()`, and `effect()`. Avoid manual subscriptions for local UI state.
4. **Dependency Injection:** Use `inject()` function. Never use constructor parameter injection.
5. **Modern Control Flow:** Use `@if`, `@for (...; track ...)`, `@switch`. Never use `*ngIf`, `*ngFor`.
6. **Strict Typing:** No `any`. Use typed interfaces, generics, or `unknown` where indeterminate.
7. **Jasmine Unit Tests:** Every component and service must have a corresponding `.spec.ts` test suite testing initialization, inputs, user actions, and error handling.

---

## 2. API Response Pattern & Service Design
Backend endpoints return standardized JSON envelope:
```typescript
export interface ApiResponse<T> {
  data: T;
  timestamp: string;
  responsecode: string;
  message: string;
  success: boolean;
}
```
- In services, inject `@core/services/api.service` (`ApiService`).
- `ApiService` automatically handles `withCredentials: true` and unwraps `.data`.
- Methods return clean `Observable<T>`.

---

## 3. Mandatory Security Rules (SEC-01 to SEC-14)

| Rule ID | Name | Constraint & Enforcement |
| :--- | :--- | :--- |
| **SEC-01** | No XSS | Never use `[innerHTML]`. Use safe interpolation `{{ }}` or explicit `SafeHtmlPipe` with sanitizer. |
| **SEC-02** | No Hardcoded Secrets | Never hardcode URLs, API keys, passwords, or tokens. Use `environment.apiBaseUrl`. |
| **SEC-03** | CSRF Protection | Ensure `csrfInterceptor` is registered in `app.config.ts`. |
| **SEC-04** | Input Validation | All form controls must declare validators (`Validators.required`, `Validators.maxLength`, etc.). |
| **SEC-05** | No Dynamic Execution | Forbid `eval()`, `new Function()`, `setTimeout(string)`. |
| **SEC-06** | Strict CSP | Content Security Policy meta tag preserved in HTML shell. |
| **SEC-07** | HTTP-Only Auth | Auth tokens stored exclusively as HTTP-only secure cookies. |
| **SEC-08** | Secure Cookies | SameSite=Strict, Secure, HttpOnly enforced. No `document.cookie` assignments. |
| **SEC-09** | No Wildcard CORS | Do not permit `*` origins in proxy configs. |
| **SEC-10** | Clean Dependencies | Zero high or critical vulnerabilities. |
| **SEC-11** | No Unsafe Bypasses | Forbid `bypassSecurityTrust*` unless reviewed and approved. |
| **SEC-12** | Strict TypeScript | `strict: true`, `noImplicitAny: true`. Zero compile warnings. |
| **SEC-13** | No Client Token Storage | Never store tokens, passwords, or credentials in `localStorage` or `sessionStorage`. |
| **SEC-14** | Credentials on Requests | All HTTP calls must include `withCredentials: true` (enforced via `ApiService`). |
