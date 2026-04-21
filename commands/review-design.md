---
description: "Quick security scan for critical vulnerabilities only. Usage: /security-quick-scan [path]"
---

Perform a rapid security scan focusing only on Critical and High severity vulnerabilities.

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

4. **Report format** (compact):
   - List only Critical and High findings
   - One line per finding: `[SEVERITY] file:line - Description`
   - Group by vulnerability type
   - Summary count at the end
