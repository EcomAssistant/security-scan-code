---
description: "Quick security scan for critical vulnerabilities only. Usage: /security-quick-scan [path]"
---

Perform a rapid security scan focusing only on Critical and High severity vulnerabilities.

## Language Requirement (语言要求)

**所有扫描报告必须使用中文输出。** 漏洞描述、风险说明等所有文字内容必须使用中文。严重程度标签可保留英文（CRITICAL/HIGH）以便标准化。

## Instructions

1. **Target**: Use the argument as path, or current directory if none provided.

2. **Focus on high-impact vulnerabilities only**:
   - SQL Injection
   - Command Injection
   - Authentication Bypass
   - Hardcoded Credentials/Secrets
   - Path Traversal
   - Deserialization vulnerabilities
   - SSRF

3. **Quick scan approach**:
   - Search for common vulnerability patterns using grep/glob
   - Check for hardcoded secrets
   - Check for debug mode in production configs
   - Check for missing authentication on endpoints
   - Check for unsafe deserialization
   - Check for direct user input in dangerous functions

4. **Report format** (compact, 必须使用中文):
   - 仅列出严重 (CRITICAL) 和高危 (HIGH) 级别的发现
   - 每项一行: `[严重程度] 文件:行号 - 漏洞描述`
   - 按漏洞类型分组
   - 末尾附上汇总统计
