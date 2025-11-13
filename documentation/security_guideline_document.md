# compose-multiplatform-music-player: Security Guideline Document

This document provides actionable security best practices tailored to the Next.js-based **compose-multiplatform-music-player** boilerplate. By integrating these guidelines, you ensure a robust, privacy-preserving, and resilient web music service.

---

## 1. Introduction & Scope

- **Purpose**: Secure by design guidelines for authentication, data protection, API hardening, and deployment practices.
- **Audience**: Developers, architects, and DevOps engineers building or extending this music player starter kit.
- **Assumptions**: Uses Next.js App Router, `better-auth`, PostgreSQL with Drizzle ORM, Tailwind CSS, and `shadcn/ui`.

---

## 2. Authentication & Access Control

- **Strong Password Policies**  
  • Enforce minimum length (≥12 chars), complexity (upper, lower, digit, symbol), and rotation policies.  
  • Use bcrypt or Argon2 with per-user salts for hashing; never store plaintext.

- **Secure Session Management**  
  • Generate cryptographically strong session cookies; set `HttpOnly`, `Secure`, `SameSite=Strict`.  
  • Implement idle and absolute timeouts (e.g., 15-min idle, 24-hr absolute).  
  • Invalidate sessions on logout and password changes to prevent fixation.

- **JWT Best Practices (if adopted)**  
  • Use HS256/RS256; never accept the `none` algorithm.  
  • Validate signature, `exp`, `aud`, and `iss` claims.  
  • Store signing keys securely (e.g., Vault, AWS KMS).

- **Role-Based Access Control (RBAC)**  
  • Define granular roles (e.g., `user`, `admin`) in your Drizzle schema.  
  • Enforce server-side permission checks in every API route and page layout.  
  • Avoid client-trust for authorization decisions.

- **Multi-Factor Authentication (MFA)**  
  • Offer TOTP or WebAuthn for high-risk operations (e.g., password resets, profile changes).

---

## 3. Input Handling & Processing

- **Server-Side Validation**  
  • Validate all form, JSON, and query inputs in API routes using a schema validator (e.g., Zod).  
  • Reject or sanitize unexpected fields.

- **Prevent Injection**  
  • Use Drizzle ORM’s prepared statements to avoid SQL injection.  
  • Sanitize any shell or file system interactions.

- **Cross-Site Scripting (XSS)**  
  • Encode dynamic data in React components by default.  
  • Use a strict Content Security Policy (CSP) via `<meta http-equiv="Content-Security-Policy" ...>` or HTTP headers.

- **Redirect & URL Validation**  
  • Maintain an allow-list of valid redirect targets; reject external URLs.

- **Secure File Uploads** (if applicable)  
  • Validate MIME types, file extensions, and content signatures.  
  • Store outside of `/public`; serve via a signed URL or proxy.  
  • Scan uploads with antivirus prior to processing.

---

## 4. Data Protection & Privacy

- **Encryption in Transit & at Rest**  
  • Enforce TLS ≥1.2 for all Next.js servers and API endpoints.  
  • Enable TLS on PostgreSQL connections (`sslmode=require`).  
  • If storing PII or tokens, use AES-256 encryption at rest (via cloud-native encryption or file-system LUKS/Vault).

- **Secrets Management**  
  • Avoid hardcoding `.env` secrets in repos.  
  • Use solutions like HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault.  
  • Rotate keys on a regular schedule and on demand.

- **Data Minimization & Masking**  
  • Return only necessary fields in API responses (avoid exposing full user model).  
  • Mask PII (e.g., *******@domain.com) in logs and debug outputs.

- **Compliance**  
  • Follow GDPR/CCPA: provide data-access, deletion endpoints, and proper consent flows if processing EU/CA users.

---

## 5. API & Service Security

- **HTTPS Enforcement**  
  • Redirect HTTP to HTTPS at the CDN or load-balancer level (e.g., Vercel forces HTTPS by default).

- **Rate Limiting & Throttling**  
  • Implement per-IP and per-user rate limits on all API routes (e.g., Next.js middleware with `express-rate-limit`).

- **CORS Configuration**  
  • Restrict `Access-Control-Allow-Origin` to your trusted frontends only.  
  • Avoid wildcard origins in production.

- **API Versioning**  
  • Prefix routes with `/api/v1/...` to manage breaking changes safely.

- **Least Privilege for Backend Services**  
  • Assign minimal database roles (e.g., a read-only role for analytics workloads).

---

## 6. Web Application Security Hygiene

- **Anti-CSRF Tokens**  
  • Use synchronizer tokens (NextAuth/Beter-Auth often include CSRF protection).  
  • Apply token checks on all state-changing POST/PUT/DELETE requests.

- **Security Headers**  
  • `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`  
  • `X-Content-Type-Options: nosniff`  
  • `X-Frame-Options: DENY`  
  • `Referrer-Policy: no-referrer-when-downgrade`  
  • `Content-Security-Policy` with strict defaults and nonce/hashes for allowed scripts.

- **Secure Cookies**  
  • Set `Secure; HttpOnly; SameSite=Strict` on session/refresh cookies.

- **Clickjacking Defense**  
  • Use `X-Frame-Options: DENY` or CSP `frame-ancestors 'none'`.

- **Client-Side Storage**  
  • Never store access tokens or PII in `localStorage` or `sessionStorage`.

---

## 7. Infrastructure & Configuration Management

- **Server Hardening**  
  • Disable default or unused ports/services.  
  • Remove default banners or version disclosures on web servers.

- **Environment Isolation**  
  • Separate dev, staging, and production with distinct credentials and secrets.

- **CI/CD Security**  
  • Require code reviews and automated security scans (SAST, dependency checks) before merges.  
  • Store CI tokens in encrypted vaults; avoid injecting secrets into logs.

- **Patching & Updates**  
  • Schedule regular updates for OS, Node.js, Next.js, Drizzle, and Tailwind.

---

## 8. Dependency Management

- **Vulnerability Scanning**  
  • Integrate `npm audit`, `Snyk`, or GitHub Dependabot into your pipeline.

- **Lockfiles & Reproducible Builds**  
  • Commit `package-lock.json` and pin critical dependencies to known-good versions.

- **Minimize Footprint**  
  • Remove unused packages; prefer lightweight, actively maintained libraries.

- **Transitive Dependency Review**  
  • Periodically audit indirect dependencies for high-severity CVEs.

---

## 9. Conclusion & Next Steps

- **Security by Design**: Incorporate these guidelines from planning through deployment.  
- **Continuous Improvement**: Reassess security controls after major feature additions.  
- **Audit & Testing**: Perform regular penetration tests and code reviews focused on security.

By following these principles and tailoring them to your unique feature set, you will build a secure, user-trusted music streaming service that stands up to modern threats.
