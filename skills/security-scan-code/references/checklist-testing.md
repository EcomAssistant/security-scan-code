# Test Coverage Review Checklist

## 1. Security Test Existence

- [ ] Security-specific test suite exists (not just unit tests)
- [ ] Integration tests cover authentication flows
- [ ] Integration tests cover authorization boundaries
- [ ] End-to-end tests cover critical security scenarios
- [ ] Security regression tests exist for previously found vulnerabilities

## 2. Authentication Testing

- [ ] Valid credentials accepted
- [ ] Invalid credentials rejected with appropriate error
- [ ] Brute force protection tested (account lockout after N attempts)
- [ ] Password reset flow tested (token expiration, single use)
- [ ] Session creation on successful login
- [ ] Session destruction on logout
- [ ] Concurrent session handling
- [ ] MFA bypass attempts tested
- [ ] OAuth flow tested (state validation, token handling)
- [ ] Token expiration and refresh tested

## 3. Authorization Testing

- [ ] Role-based access control enforced (each role gets appropriate access)
- [ ] Horizontal access control: user A cannot access user B's resources
- [ ] Vertical access control: regular user cannot access admin functions
- [ ] API endpoint authorization: unauthenticated requests rejected
- [ ] Resource-level permissions enforced
- [ ] Authorization bypass via parameter tampering tested
- [ ] Authorization bypass via HTTP method switching tested (GET → POST)
- [ ] IDOR (Insecure Direct Object Reference) tested for all resources

## 4. Input Validation Testing

- [ ] Boundary values tested (empty, max length, special characters)
- [ ] SQL injection payloads tested on all input points
- [ ] XSS payloads tested on all output points (reflected, stored, DOM)
- [ ] Command injection payloads tested
- [ ] Path traversal payloads tested (`../`, encoded variants)
- [ ] LDAP injection tested (if applicable)
- [ ] Template injection tested (if applicable)
- [ ] XML/XXE injection tested (if applicable)
- [ ] Prototype pollution tested (JavaScript)
- [ ] SSRF payloads tested (if applicable)
- [ ] Large input / payload bomb tested
- [ ] Unicode/encoding attack vectors tested

## 5. API Security Testing

- [ ] Rate limiting verified (requests above limit are blocked)
- [ ] CORS policy tested (cross-origin requests handled correctly)
- [ ] API authentication required on all non-public endpoints
- [ ] API versioning does not break security
- [ ] Pagination prevents data enumeration
- [ ] Batch operations have proper authorization
- [ ] File upload restrictions tested (type, size, content)
- [ ] GraphQL query depth limiting tested (if applicable)

## 6. Cryptographic Testing

- [ ] Encryption/decryption roundtrip tested
- [ ] Token signature validation tested (tampered tokens rejected)
- [ ] Token expiration enforcement tested
- [ ] Weak algorithms not used (MD5, SHA1 for security purposes)
- [ ] Random number generator produces unpredictable values
- [ ] Key rotation does not break existing encrypted data

## 7. Error Handling Testing

- [ ] Invalid input produces user-friendly errors (no stack traces)
- [ ] Server errors do not expose internal details
- [ ] Timeout scenarios handled gracefully
- [ ] Database connection failures handled without info leakage
- [ ] Third-party service failures handled gracefully
- [ ] Error responses are consistent (no information disparity)

## 8. Concurrency Testing

- [ ] Race conditions in financial operations tested
- [ ] Concurrent session handling tested
- [ ] Double-submit prevention tested (idempotency)
- [ ] Database deadlocks handled
- [ ] Shared resource access is thread-safe

## 9. Data Protection Testing

- [ ] Sensitive fields are encrypted in database
- [ ] Sensitive data not returned in API responses (masking)
- [ ] Sensitive data not logged
- [ ] Backup restore maintains encryption
- [ ] Data deletion is complete (not soft-delete that can be recovered)
- [ ] Search does not expose sensitive fields

## 10. Configuration Testing

- [ ] Security headers present in responses (CSP, HSTS, X-Frame-Options)
- [ ] Debug mode is off in test/staging/production
- [ ] Default credentials changed
- [ ] CORS is restrictive, not wildcard
- [ ] HTTP redirects to HTTPS
- [ ] Cookie flags are set correctly (Secure, HttpOnly, SameSite)

## 11. Negative / Abuse Case Testing

- [ ] Application handles malformed input without crashing
- [ ] Application handles extremely large input without DoS
- [ ] Application handles concurrent requests without data corruption
- [ ] Application handles network failures during transactions
- [ ] Application handles expired/invalid tokens gracefully
- [ ] Application handles missing required fields
- [ ] Application handles unexpected HTTP methods

## 12. Test Quality Assessment

- [ ] Tests are deterministic (not flaky)
- [ ] Tests clean up after themselves (no state leakage)
- [ ] Tests mock external dependencies (no real API calls in unit tests)
- [ ] Test coverage metrics exist and meet minimum threshold
- [ ] Security tests run in CI/CD pipeline
- [ ] Test data does not contain real PII or credentials
