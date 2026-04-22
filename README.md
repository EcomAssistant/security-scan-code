# security-scan-code

Claude Code 安全审计插件 — 分层式代码安全审查工具，系统化地从架构设计、代码实现到测试覆盖逐层扫描安全漏洞。

## 功能特性

- **三层深度审计**：架构设计审查 → 代码实现审查 → 测试覆盖审查
- **OWASP Top 10 覆盖**：基于 OWASP 2021 标准识别注入、XSS、越权、SSRF 等常见漏洞
- **中文报告输出**：所有审查报告强制使用中文，遵循统一的中文审计报告模板
- **多语言支持**：PHP / Python / JavaScript / TypeScript / Java / Go / Ruby
- **严重程度分级**：CRITICAL / HIGH / MEDIUM / LOW / INFO 五级分类，附带修复优先级矩阵
- **自动修复建议**：每项发现均提供具体的问题代码和修复代码

## 安装

将本项目放置在 Claude Code 的 skills 目录下即可使用：

```bash
# 克隆到 Claude Code 项目目录
git clone <repo-url> /path/to/security-scan-code
```

## 使用方式

### 完整安全审计

```bash
# 审查当前目录
/code-review

# 审查指定路径
/code-review src/api/

# 审查指定文件
/code-review src/auth/login.py
```

### 快速安全扫描（仅 Critical / High）

```bash
# 快速扫描当前目录
/security-quick-scan

# 快速扫描指定路径
/security-quick-scan src/
```

## 审计流程

### 第一层：架构设计审查

审查信任边界、身份认证架构、权限控制模型、API 安全设计、数据保护策略、第三方集成风险、密钥管理、错误处理策略、日志审计设计等。

### 第二层：代码实现审查

逐文件检查输入验证、注入防护（SQL / 命令 / XSS / 模板）、身份认证实现、授权校验、加密使用、会话管理、文件操作、错误处理、并发安全、配置安全等。

### 第三层：测试覆盖审查

评估安全测试用例的完整性，包括认证测试、授权边界测试、输入验证测试、加密测试、错误处理测试、并发测试、数据保护测试等。

## 报告格式

审计完成后会在目标目录生成 `security-review-report.md`，包含：

| 章节 | 内容 |
|------|------|
| 静态分析概要 | 代码质量评分、问题统计 |
| 审计发现明细 | 每项漏洞的详细描述、问题代码、修复建议 |
| 修复优先级矩阵 | 按严重程度分级的修复计划 |
| 安全亮点 | 值得推广的良好安全实践 |
| 知识共享 | 团队安全编码建议和常见陷阱总结 |

## 项目结构

```
security-scan-code/
├── README.md
├── skills/security-scan-code/
│   ├── SKILL.md                          # 技能定义与审查流程
│   └── references/
│       ├── checklist-design.md           # 架构设计审查清单
│       ├── checklist-implementation.md   # 代码实现审查清单
│       ├── checklist-testing.md          # 测试覆盖审查清单
│       ├── owasp-patterns.md             # OWASP Top 10 漏洞模式速查
│       └── report-template.md            # 中文审计报告模板
├── agents/
│   └── security-reviewer.md              # 安全审查子代理
└── commands/
    ├── code-review.md                    # /code-review 命令
    └── review-design.md                  # /security-quick-scan 命令
```

## 严重程度定义

| 级别 | 说明 |
|------|------|
| **CRITICAL** | 可在生产环境被利用，导致数据泄露、远程代码执行或系统完全沦陷 |
| **HIGH** | 中等难度即可利用，导致显著数据暴露或认证绕过 |
| **MEDIUM** | 需要特定条件才能利用，有限数据暴露或安全控制降级 |
| **LOW** | 轻微安全弱点，纵深防御改进，最佳实践偏离 |
| **INFO** | 观察性建议或值得注意的正面发现 |
