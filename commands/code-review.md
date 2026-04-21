---
description: "Perform a security-focused code review on specified files or directories. Usage: /code-review [path] (reviews current directory if no path given)"
---

Perform a complete security code review on the specified target.

## Language Requirement (语言要求)

**所有审查报告必须使用中文输出。** 报告标题、漏洞描述、修复建议、风险说明等所有文字内容必须使用中文。严重程度标签可保留英文（CRITICAL/HIGH/MEDIUM/LOW/INFO）但需附带中文说明。CWE/OWASP 参考编号保留英文原文。最终报告遵循 `references/report-template.md` 中的中文模板格式。

## Instructions

1. **Determine the target**: Use the argument as the path to review. If no argument is provided, review the current working directory.

2. **Detect the tech stack**: Identify languages, frameworks, databases by scanning for:
   - `composer.json`, `package.json`, `requirements.txt`, `go.mod`, `pom.xml`, `Gemfile`, `Cargo.toml`
   - Framework-specific files (e.g., `app/Http/Kernel.php` for Laravel)

3. **Execute the three-layer review** following the `security-code-review` skill:
   - **Layer 1**: Architecture & Design Review
   - **Layer 2**: Implementation Review
   - **Layer 3**: Test Coverage Review

4. **Generate the final report** using the report template from the skill, output to `security-review-report.md` in the target directory.

5. For each finding, include:
   - Severity (Critical/High/Medium/Low/Info)
   - File path and line number
   - Vulnerable code snippet
   - Recommended fix with code
   - CWE/OWASP reference

6. Present the report to the user and highlight the most critical findings that need immediate attention.
