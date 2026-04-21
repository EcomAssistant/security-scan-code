# Implementation Review Checklist

## 1. Input Validation & Sanitization

- [ ] All user input is validated on the server side (client-side is not sufficient)
- [ ] Input validation uses allowlists (not blocklists) where possible
- [ ] Input length limits enforced on all fields
- [ ] File uploads validate MIME type, extension, and content (magic bytes)
- [ ] Uploaded files stored outside web root with non-guessable names
- [ ] JSON/XML input validated against schema
- [ ] URL parameters validated (no open redirect)
- [ ] HTTP headers treated as untrusted input
- [ ] Cookie values validated before use

### Language-Specific Input Patterns

**PHP:**
- `filter_input()`, `filter_var()` with appropriate flags
- Avoid `$_GET`/`$_POST` direct use without validation
- `htmlspecialchars($str, ENT_QUOTES, 'UTF-8')` for output encoding

**Python:**
- Framework validators (Django forms, Flask-WTF, Pydantic)
- `bleach.clean()` for HTML sanitization
- Type validation with schema libraries

**JavaScript/TypeScript:**
- Joi/Zod/express-validator for API input
- DOMPurify for HTML sanitization
- Never trust `window.location` derived values

**Java:**
- Bean Validation (JSR-380) annotations
- Spring `@Valid` with custom validators
- OWASP ESAPI input validation

## 2. Injection Prevention

### SQL Injection
- [ ] Parameterized queries / prepared statements used exclusively
- [ ] ORM used correctly (no raw query shortcuts with user input)
- [ ] Stored procedures do not concatenate user input
- [ ] Database connections use minimal privilege account
- [ ] No dynamic table/column names from user input

### Command Injection
- [ ] No `system()`, `exec()`, `shell_exec()` with user input
- [ ] `subprocess` uses array form, not `shell=True`
- [ ] `child_process.exec()` avoided for user input (use `spawn`)
- [ ] Input sanitized if command arguments are unavoidable
- [ ] `shlex.quote()` or equivalent used for shell escaping

### LDAP Injection
- [ ] LDAP queries use parameterized APIs
- [ ] Special characters escaped in LDAP filters

### XSS (Cross-Site Scripting)
- [ ] Output encoding applied based on context (HTML, JS, URL, CSS)
- [ ] Content Security Policy (CSP) header set
- [ ] No `innerHTML`, `outerHTML`, `document.write()` with user input
- [ ] No `v-html` (Vue), `dangerouslySetInnerHTML` (React) with untrusted data
- [ ] Template engine auto-escaping enabled

### Template Injection
- [ ] No user-controlled template strings (SSTI prevention)
- [ ] Sandboxed template execution if dynamic templates needed
- [ ] Jinja2: no `|safe` filter on user input; use sandboxed environment
- [ ] Freemarker/Thymeleaf: no dynamic template resolution from user input

## 3. Authentication Implementation

- [ ] Passwords hashed with bcrypt/Argon2/scrypt (never MD5, SHA1, plain SHA)
- [ ] Salt is unique per password (auto-managed by modern hash functions)
- [ ] JWT tokens validated for signature, expiration, issuer, audience
- [ ] Session IDs are cryptographically random, sufficiently long
- [ ] Session fixation prevented (regenerate ID after login)
- [ ] Remember-me tokens are single-use and expire
- [ ] OAuth state parameter is random and verified
- [ ] PKCE used for OAuth public clients (SPA, mobile)
- [ ] API keys not embedded in client-side code

## 4. Authorization Enforcement

- [ ] Every sensitive endpoint has authorization check
- [ ] Resource-level access control (user can only access their own resources)
- [ ] Role checks use server-side role data (not client-provided)
- [ ] No "security through obscurity" (hidden endpoints still need auth)
- [ ] CORS configuration reflects actual authorization policy
- [ ] IDOR prevented: object-level permissions checked
- [ ] Admin functions require re-authentication for sensitive operations
- [ ] Time-based access controls enforced server-side

## 5. Cryptography

- [ ] No custom cryptography algorithms
- [ ] Symmetric: AES-256-GCM (not ECB, not CBC without HMAC)
- [ ] Asymmetric: RSA-2048+ with OAEP padding, or Ed25519
- [ ] Hashing: SHA-256+ for non-password purposes
- [ ] Random: `secrets` module / `crypto.randomBytes` / `SecureRandom`
- [ ] TLS: 1.2+ enforced, weak ciphers disabled
- [ ] IV/nonce is random and never reused
- [ ] Encryption keys are rotated per policy
- [ ] Key derivation uses PBKDF2, scrypt, or Argon2

## 6. Session Management

- [ ] Session timeout configured (idle and absolute)
- [ ] Session cookies: `Secure`, `HttpOnly`, `SameSite=Strict/Lax`
- [ ] Session data minimized (no sensitive data in session store)
- [ ] Concurrent session limits enforced where required
- [ ] Logout invalidates session server-side (not just client cookie)
- [ ] Session store is secure (encrypted, access-controlled)

## 7. File Operations

- [ ] File paths validated (no path traversal with `../`)
- [ ] `realpath()` resolved and checked against allowed directory
- [ ] File permissions follow principle of least privilege
- [ ] Temporary files cleaned up and not in predictable locations
- [ ] Symbolic link attacks mitigated
- [ ] File content validated before processing

## 8. Error Handling in Code

- [ ] Exceptions caught and handled gracefully
- [ ] Error messages to users are generic (no stack traces, DB errors, paths)
- [ ] Errors logged with context for debugging
- [ ] No `try/catch` that silently swallows errors (`catch(e) {}`)
- [ ] Database transactions rolled back on error
- [ ] Resources released in `finally` blocks or using RAII pattern

## 9. Concurrency & Race Conditions

- [ ] Shared resources protected with proper locking
- [ ] TOCTOU (Time-of-check-time-of-use) issues addressed
- [ ] Database operations use appropriate isolation levels
- [ ] Optimistic locking for concurrent updates
- [ ] No double-submit issues (idempotency keys)
- [ ] Thread safety for shared state in multi-threaded environments

## 10. Configuration & Defaults

- [ ] Debug mode disabled in production
- [ ] Default credentials changed
- [ ] Security headers set (CSP, HSTS, X-Frame-Options, X-Content-Type-Options)
- [ ] Directory listing disabled
- [ ] Verbose error pages disabled in production
- [ ] Unnecessary HTTP methods disabled (TRACE, OPTIONS if not needed)
- [ ] Default security configurations reviewed (framework defaults)

## 11. Logging & Monitoring in Code

- [ ] No sensitive data logged (passwords, tokens, PII, credit cards)
- [ ] Log levels used appropriately (ERROR for security events)
- [ ] Audit log entries for sensitive operations
- [ ] Log injection prevented (no unescaped user input in log messages)
- [ ] Structured logging for machine parsing
- [ ] Failed authentication/authorization attempts logged

## 12. Dependency & Supply Chain

- [ ] Dependency versions pinned (lock files used)
- [ ] No known vulnerable dependencies
- [ ] Minimal dependencies (each dependency is attack surface)
- [ ] Dependencies sourced from trusted registries
- [ ] No unnecessary dev dependencies in production image
