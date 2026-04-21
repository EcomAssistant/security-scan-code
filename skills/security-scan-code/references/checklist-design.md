# Design & Architecture Review Checklist

## 1. Trust Boundaries & Data Flow

- [ ] Identify all trust boundaries (internet → DMZ → internal → database)
- [ ] Map complete data flow for sensitive data (PII, credentials, financial)
- [ ] Verify data classification at each boundary crossing
- [ ] Check that trust boundary transitions have proper controls
- [ ] Validate that data flow does not bypass security controls
- [ ] Ensure sensitive data is not logged or exposed in error flows

## 2. Authentication Architecture

- [ ] Single source of truth for authentication (no multiple auth mechanisms)
- [ ] Password storage uses modern hashing (bcrypt, Argon2, scrypt)
- [ ] Multi-factor authentication available for sensitive operations
- [ ] Account lockout/throttling for brute force protection
- [ ] Secure password reset flow (time-limited tokens, not reversible)
- [ ] OAuth/OIDC implementation follows current best practices
- [ ] API authentication strategy is consistent (JWT, API keys, OAuth)
- [ ] Session management architecture is sound (server-side vs client-side)
- [ ] Credential rotation mechanisms exist for service accounts

## 3. Authorization Architecture

- [ ] Authorization model defined (RBAC, ABAC, ReBAC)
- [ ] Permission granularity appropriate for business requirements
- [ ] Principle of least privilege applied to all roles
- [ ] Horizontal access controls prevent IDOR (user A cannot access user B's data)
- [ ] Vertical access controls prevent privilege escalation
- [ ] Admin functions have additional authorization layer
- [ ] Authorization checks are centralized, not scattered
- [ ] Failed authorization attempts are logged

## 4. API Design Security

- [ ] Rate limiting on all public endpoints
- [ ] Input validation contract defined (what's accepted, rejected)
- [ ] Output serialization does not leak internal fields
- [ ] Pagination prevents data enumeration
- [ ] API versioning strategy does not break security
- [ ] CORS policy is restrictive, not wildcard
- [ ] GraphQL query depth limiting (if applicable)
- [ ] File upload endpoints have type/size restrictions
- [ ] Async operations have proper authorization on result retrieval

## 5. Data Protection

- [ ] Sensitive data encrypted at rest (database, file storage)
- [ ] TLS enforced for all data in transit (no HTTP fallback)
- [ ] Encryption key management strategy (rotation, storage, access)
- [ ] PII handling follows data classification policy
- [ ] Database field-level encryption for highly sensitive data
- [ ] Backup encryption and access controls
- [ ] Data retention and deletion policies implemented
- [ ] Search indexing excludes sensitive fields

## 6. Third-Party Integration

- [ ] All third-party dependencies identified and tracked
- [ ] Dependency vulnerability scanning in CI/CD pipeline
- [ ] Third-party API calls have timeout and retry handling
- [ ] Supply chain attack mitigation (lock files, integrity checks)
- [ ] Third-party service authentication uses minimal scope credentials
- [ ] Data shared with third parties is minimized and classified
- [ ] Third-party service availability does not break core functionality

## 7. Secret Management

- [ ] No secrets in source code or configuration files
- [ ] Secrets managed through dedicated system (Vault, cloud KMS)
- [ ] Secret rotation policy exists and is automated
- [ ] Different secrets for different environments (dev/staging/prod)
- [ ] CI/CD pipeline does not expose secrets in logs
- [ ] Secret access is auditable

## 8. Error Handling Strategy

- [ ] Global error handler returns generic messages to users
- [ ] Detailed errors logged server-side only
- [ ] Stack traces never exposed to end users
- [ ] Error responses do not reveal system architecture
- [ ] Graceful degradation maintains security controls
- [ ] Timeout handling does not leave resources in insecure state

## 9. Logging & Audit Trail

- [ ] Security events are logged (auth success/failure, access denied)
- [ ] Logs contain sufficient context for investigation (who, what, when, where)
- [ ] Logs do not contain sensitive data (passwords, tokens, PII)
- [ ] Log integrity protection (tamper detection)
- [ ] Log retention policy defined
- [ ] Centralized logging for distributed systems
- [ ] Alerting on suspicious patterns (brute force, privilege escalation)

## 10. Infrastructure & Deployment

- [ ] Infrastructure as Code with security review
- [ ] Container images use minimal base images
- [ ] Network segmentation between application tiers
- [ ] Secrets not in container images or environment variables exposed to code
- [ ] Health check endpoints do not expose internal state
- [ ] Debug/Admin endpoints not accessible from public network
- [ ] Database access restricted to application layer only
