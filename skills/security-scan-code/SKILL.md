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

**Output format for design review findings (必须使用中文):**

```markdown
## 架构设计审查发现

### [D-001] {漏洞标题}
- **严重程度**: 严重 (CRITICAL) / 高危 (HIGH) / 中危 (MEDIUM) / 低危 (LOW) / 信息 (INFO)
- **分类**: 身份认证 | 权限控制 | 数据保护 | API设计 | 第三方集成 | 密钥管理 | 错误处理 | 日志审计
- **影响组件**: {受影响的组件/模块}
- **描述**: {问题描述及其重要性}
- **风险**: {潜在的业务影响}
- **修复建议**: {具体、可操作的修复方案}
- **OWASP 参考**: {相关 OWASP 分类}
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

**Output format for implementation findings (必须使用中文):**

```markdown
## 代码实现审查发现

### [I-001] {漏洞标题}
- **严重程度**: 严重 (CRITICAL) / 高危 (HIGH) / 中危 (MEDIUM) / 低危 (LOW) / 信息 (INFO)
- **文件**: `{file_path}:{line_number}`
- **分类**: 注入攻击 | XSS跨站脚本 | 身份认证 | 加密安全 | 数据泄露 | 配置安全 | 并发安全 | 其他
- **漏洞模式**: {CWE-ID}
- **问题代码**:
```
  {存在漏洞的代码片段}
  ```
- **问题描述**: {为什么这段代码存在漏洞}
- **修复建议**:
  ```
  {修复后的代码片段}
  ```
- **验证方法**: {如何验证修复是否有效}
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

**Output format for testing findings (必须使用中文):**

```markdown
## 测试覆盖审查发现

### [T-001] {测试缺陷标题}
- **严重程度**: 严重 (CRITICAL) / 高危 (HIGH) / 中危 (MEDIUM) / 低危 (LOW) / 信息 (INFO)
- **分类**: 缺失测试 | 断言不充分 | 缺少边界测试 | 缺少异常用例
- **关联发现**: {关联的 D-xxx 或 I-xxx 编号}
- **描述**: {缺少哪些测试覆盖}
- **建议测试用例**:
```
  {测试代码或测试用例描述}
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

## Language Requirement (语言要求)

**所有报告和审计发现必须使用中文输出。**

- 报告标题、描述、修复建议、风险说明等所有文字内容必须使用中文
- 代码注释中的安全说明使用中文
- 严重程度标签可保留英文（CRITICAL/HIGH/MEDIUM/LOW/INFO）以便标准化，但必须附带中文说明
- CWE/OWASP 参考编号保留英文原文
- 报告格式遵循 `references/report-template.md` 中的中文模板

## Behavioral Guidelines

- **Be thorough but practical**: Focus on exploitable vulnerabilities, not theoretical risks
- **Always provide fixes**: Never flag a problem without suggesting a concrete solution
- **Include positive findings**: Acknowledge good security practices to promote knowledge sharing
- **Prioritize by risk**: Critical/High first, don't overwhelm with Low/Info
- **Reference standards**: Map findings to OWASP Top 10, CWE, or team coding standards
- **Avoid false positives**: Verify findings before reporting; if uncertain, note confidence level
- **Respect scope**: Only review what was requested, note out-of-scope observations separately
- **Output in Chinese**: All reports, findings, and recommendations must be written in Chinese (中文)
