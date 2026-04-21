---
name: security-code-review
description: Security-focused code review with layered analysis (architecture/design → implementation → testing). Use PROACTIVELY when user asks to "review code", "security audit", "code review", "check security", "find vulnerabilities", "audit code", "scan for issues", "code quality check", "inspect code", or discusses secure coding, code review processes, OWASP, security compliance, penetration testing preparation, or any code analysis for security purposes. Also trigger when user provides code files or directories for review, asks about code security best practices, or wants to ensure their code meets security standards.
version: 1.0.0
---

# Security  Code Review Skill

Layered, security-focused code review that systematically examines code from high-level architecture down to implementation details and test coverage.

## Core Philosophy

Code review is not just bug-hunting — it's a structured process to:
1. Validate architectural security decisions
2. Identify implementation-level vulnerabilities
3. Verify defensive mechanisms through test coverage
4. Ensure adherence to team security standards
5. Share knowledge and elevate team security awareness

## Review Process

### Step 0: Scope & Context Gathering

Before reviewing any code, establish context:

1. **Identify the target**: Determine what code/files/directories are being reviewed
2. **Understand the tech stack**: Language, framework, database, infrastructure
3. **Determine the review scope**: Full audit, specific module, diff review, or targeted vulnerability search
4. **Load reference materials**: Read the relevant reference files from `references/` based on the review layer

### Step 1: Architecture & Design Review (High Level)

Start here. Design flaws are the most expensive to fix and the most damaging.

Read `references/checklist-design.md` for the full checklist.

**Focus areas:**
- Trust boundaries and data flow analysis
- Authentication & authorization architecture
- Data classification and protection strategy
- API design security (rate limiting, input contracts)
- Third-party integration risk assessment
- Secret management approach
- Error handling strategy (fail-secure, no leakage)
- Logging and audit trail design

**Output format for design review findings:**

```markdown
## Design Review Findings

### [D-001] {Finding Title}
- **Severity**: Critical / High / Medium / Low / Info
- **Category**: Authentication | Authorization | Data Protection | API Design | Integration | Secret Management | Error Handling | Logging
- **Component**: {affected component/module}
- **Description**: {what's wrong and why it matters}
- **Risk**: {potential business impact}
- **Recommendation**: {specific, actionable fix}
- **OWASP Reference**: {relevant OWASP category if applicable}
```

### Step 2: Implementation Review (Code Level)

After design review, dive into the code. This is where most vulnerabilities hide.

Read `references/checklist-implementation.md` for the full checklist.
Read `references/owasp-patterns.md` for OWASP vulnerability patterns.

**Focus areas:**
- Input validation and sanitization
- Output encoding (XSS prevention)
- SQL injection prevention
- Command injection prevention
- Path traversal prevention
- Authentication implementation correctness
- Authorization enforcement (IDOR, privilege escalation)
- Cryptographic usage (algorithm choice, key management)
- Session management
- File upload handling
- Deserialization safety
- Race conditions and concurrency
- Error handling in code (stack traces, info leakage)
- Hardcoded credentials and secrets
- Logging sensitive data
- Unsafe defaults

**Per-file review approach:**

For each file under review:
1. Read the complete file
2. Identify entry points (user input, API calls, file I/O, database queries)
3. Trace data flow from entry points to sensitive operations
4. Check each focus area against the code
5. Document findings with file path and line numbers

**Output format for implementation findings:**

```markdown
## Implementation Review Findings

### [I-001] {Finding Title}
- **Severity**: Critical / High / Medium / Low / Info
- **File**: `{file_path}:{line_number}`
- **Category**: Injection | XSS | Auth | Crypto | Data Exposure | Config | Concurrency | Other
- **Vulnerability Pattern**: {CWE-ID if applicable}
- **Current Code**:
```
  {vulnerable code snippet}
  ```
- **Issue**: {why this code is vulnerable}
- **Fix**:
  ```
  {corrected code snippet}
  ```
- **Testing**: {how to verify the fix works}
  ```

### Step 3: Test Coverage Review

After implementation review, assess whether existing tests adequately cover security scenarios.

Read `references/checklist-testing.md` for the full checklist.

**Focus areas:**
- Security-specific test cases existence
- Boundary value testing
- Negative/abuse case testing
- Authentication bypass testing
- Authorization boundary testing
- Input validation test coverage
- Error handling test coverage
- Rate limiting test verification
- Integration test security scenarios

**Output format for testing findings:**

```markdown
## Test Coverage Findings

### [T-001] {Finding Title}
- **Severity**: Critical / High / Medium / Low / Info
- **Category**: Missing Test | Weak Assertion | Missing Edge Case | Missing Abuse Case
- **Related Finding**: {links to D-xxx or I-xxx findings}
- **Description**: {what test coverage is missing}
- **Recommended Test**:
```
  {test code or test case description}
  ```

  ```

### Step 4: Generate Final Report

After all three layers are complete, compile the final report using the Chinese audit report template.

Read `references/report-template.md` for the full template.

**Report structure** (matches 代码审计报告 format):

```markdown
# 代码审计报告

> **报告编号**: SAR-{YYYYMMDD}-{NNN}
> **生成时间**: {YYYY-MM-DD HH:mm:ss}
> **审计范围**: {项目/模块名称}
> **技术栈**: {语言, 框架, 数据库}
> **审计方法**: 分层审计（架构设计 → 代码实现 → 测试覆盖）

---

## 静态分析概要

| 项目 | 结果 |
|------|------|
| 代码质量评分 | **{score}/100** |
| 问题总数 | **{total}** |
| 严重 (CRITICAL) | **{n}** |
| 高危 (HIGH) | **{n}** |
| 中危 (MEDIUM) | **{n}** |
| 低危 (LOW) | **{n}** |
| 信息 (INFO) | **{n}** |
| 审计耗时 | {duration} |

---

## 审计发现明细 ({total})

### 1. {漏洞标题}

**[CRITICAL]** | 文件: `{file_path}` | 行: {line_number}

**漏洞描述**: {详细描述该漏洞的成因、攻击路径及影响范围}

**问题代码:**
```
{vulnerable_code_snippet}
```

**修复建议:**
```
{fixed_code_snippet}
```

**参考**: {CWE-ID} / {OWASP 分类}

---

## 修复优先级矩阵

### 立即修复（CRITICAL / HIGH）
{按优先级排列的修复项表格}

### 计划修复（MEDIUM）
{中危修复项表格}

### 长期改进（LOW / INFO）
{低危改进建议}

---

## 安全亮点
{良好安全实践}

## 知识共享
{团队安全编码建议}

---

> 本报告由 security-scan-code 自动生成，仅供安全审计参考。
```

Output the report to `security-review-report.md` in the target directory.

## Severity Rating Guide

| Rating | Criteria |
|--------|----------|
| **Critical** | Exploitable in production, leads to data breach, RCE, or complete system compromise |
| **High** | Exploitable with moderate effort, significant data exposure, or auth bypass |
| **Medium** | Requires specific conditions to exploit, limited data exposure, or degradation of security controls |
| **Low** | Minor security weakness, defense-in-depth improvement, best practice deviation |
| **Info** | Observation, recommendation, or positive finding worth noting |

## Language-Specific Focus

Adapt the review focus based on the detected language:

- **PHP**: SQL injection, XSS, file inclusion, session management, `eval()`, unsafe deserialization, CSRF
- **Python**: Template injection, pickle/yaml deserialization, command injection, SQL injection via ORM misuse, debug mode
- **JavaScript/TypeScript**: XSS (DOM-based, stored, reflected), prototype pollution, SSRF, unsafe regex, npm dependency risks
- **Java**: Deserialization, SQL injection, XXE, insecure JNDI, path traversal, Spring Security misconfiguration
- **Go**: Command injection, SSRF, race conditions, unsafe CGO, integer overflow, path traversal
- **Ruby**: SQL injection (ActiveRecord), YAML deserialization, mass assignment, CSRF, SSRF
- **Go**: Command injection via exec, SSRF, race conditions, unsafe CGO

## Behavioral Guidelines

- **Be thorough but practical**: Focus on exploitable vulnerabilities, not theoretical risks
- **Always provide fixes**: Never flag a problem without suggesting a concrete solution
- **Include positive findings**: Acknowledge good security practices to promote knowledge sharing
- **Prioritize by risk**: Critical/High first, don't overwhelm with Low/Info
- **Reference standards**: Map findings to OWASP Top 10, CWE, or team coding standards
- **Avoid false positives**: Verify findings before reporting; if uncertain, note confidence level
- **Respect scope**: Only review what was requested, note out-of-scope observations separately
