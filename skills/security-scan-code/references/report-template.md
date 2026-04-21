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
```{language}
// {file_path}:{line_number}
{vulnerable_code_snippet}
```

**修复建议:**
```{language}
{fixed_code_snippet}
```

**参考**: {CWE-ID} / {OWASP 分类}

---

### 2. {漏洞标题}

**[HIGH]** | 文件: `{file_path}` | 行: {line_number}

**漏洞描述**: {详细描述}

**问题代码:**
```{language}
{vulnerable_code_snippet}
```

**修复建议:**
```{language}
{fixed_code_snippet}
```

**参考**: {CWE-ID} / {OWASP 分类}

---

## 修复优先级矩阵

### 立即修复（CRITICAL / HIGH）

| 编号 | 漏洞标题 | 严重程度 | 影响范围 | 修复建议摘要 |
|------|---------|---------|---------|-------------|
| 1 | {title} | CRITICAL | {scope} | {brief fix} |

### 计划修复（MEDIUM）

| 编号 | 漏洞标题 | 严重程度 | 影响范围 | 修复建议摘要 |
|------|---------|---------|---------|-------------|
| {N} | {title} | MEDIUM | {scope} | {brief fix} |

### 长期改进（LOW / INFO）

| 编号 | 建议 | 说明 |
|------|------|------|
| {N} | {title} | {note} |

---

## 安全亮点

{审计过程中发现的良好安全实践，值得团队推广}

---

## 知识共享

{常见安全陷阱总结、团队安全编码建议、值得学习的安全模式}

---

> 本报告由 security-scan-code 自动生成，仅供安全审计参考。
