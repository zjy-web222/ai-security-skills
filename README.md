# AI Security Skills / AI 安全技能

Two reusable skills for AI-assisted software development and AI-powered application runtimes.

两个可复用的 AI 安全 Skill：一个用于 AI 辅助软件开发，一个用于具有 AI 能力的软件运行时。

[中文说明](#中文说明) · [English](#english)

## 中文说明

### 项目用途

本仓库提供两个职责不同、可独立使用的 Skill，适用于聊天应用、RAG、Agent、MCP 工具和 AI 自动化等场景，不依赖特定编程语言、模型厂商或框架。

| Skill | 调用方 | 主要用途 |
| --- | --- | --- |
| [`ai-security-development`](skills/ai-security-development/SKILL.md) | AI 开发助手 | 在设计、实现、修改或审查 AI 功能时，识别信任边界，落实安全控制并验证实际行为。 |
| [`ai-security-runtime`](skills/ai-security-runtime/SKILL.md) | 软件内置 AI | 在处理外部材料、调用工具、分享数据和写入记忆时，保持用户任务与宿主授权边界。 |

覆盖提示注入、数据泄露、跨租户访问、工具越权、不安全输出处理、记忆污染、审批失效、重复执行和资源预算等风险。根据实际能力选择检查，不要求普通应用引入全部机制。

**Skill 是 AI 行为指引。权限校验、数据隔离、操作拦截与执行限制必须由宿主软件强制实施。安装 Skill 不等于完成安全加固。**

### 仓库结构

```text
skills/
├── ai-security-development/
│   ├── SKILL.md
│   └── references/
│       └── verification.md
└── ai-security-runtime/
    ├── SKILL.md
    └── references/
        └── host-integration.md
```

### 使用开发安全 Skill

将 `skills/ai-security-development/` 整个目录复制到开发助手的 Skill 目录。以本地 Codex 为例，使用 `$CODEX_HOME/skills/`；未设置 `CODEX_HOME` 时，使用用户目录下的 `.codex/skills/`。复制时保留 `references/`，并检查已有同名 Skill，避免覆盖自己的修改。

在支持该调用语法的开发助手中，可以这样提出任务：

```text
使用 $ai-security-development 为当前项目接入 RAG。
复用现有鉴权，确保检索、引用和缓存都不会泄露其他租户的数据，
并验证越权请求与正常请求的实际结果。
```

也可以用于针对性审查：

```text
使用 $ai-security-development 审查当前 Agent 的工具执行链路。
列出有代码证据的风险、攻击条件、修复建议与验证方式。
```

该 Skill 包含数据流梳理、安全控制选择、最小修复、验证与交付流程。测试参考见 [AI 安全验收场景](skills/ai-security-development/references/verification.md)。

### 接入运行时安全 Skill

将 `skills/ai-security-runtime/` 作为受控资源内置到软件中。`$ai-security-runtime` 适用于支持 Skill 调用语法的环境；其他应用应使用自己的 Skill 加载机制。

宿主软件应完成以下配合：

1. 在相关 AI 流程前可靠加载规则，管理来源与版本，避免仅依赖模型自主选择是否调用。
2. 通过可信通道提供已验证的身份、租户、任务范围、工具能力、分享规则、审批状态和预算。
3. 在实际执行前核验参数、对象权限、外发目的地与有效批准；即使模型跳过 Skill，仍拦截越权操作。
4. 返回可核验的执行状态，处理幂等、取消和状态未知，并以最小必要信息记录审计事件。

接入说明与验收案例见 [宿主接入约定](skills/ai-security-runtime/references/host-integration.md)。

### 验证与限制

- Skill 文件已经通过创建工具的基础格式校验，文件内本地参考链接已检查。
- 验收文件提供测试场景与观察标准；本仓库尚未对具体应用、模型或执行器进行运行时安全测试。
- 测试应包含攻击请求与正常任务，观察真实数据流和副作用，不仅检查 AI 是否声称拒绝。
- 不保证消除全部提示注入，不代表安全认证或合规达标；产品内容政策与业务审批要求仍由宿主定义。

## English

### Purpose

This repository contains two independently usable skills for chat applications, retrieval-augmented generation (RAG), agents, MCP tools, and AI automation. They are not tied to a particular programming language, model provider, or framework.

| Skill | Used by | Purpose |
| --- | --- | --- |
| [`ai-security-development`](skills/ai-security-development/SKILL.md) | AI coding assistants | Identify trust boundaries, implement security controls, and verify behavior when designing, building, changing, or reviewing AI features. |
| [`ai-security-runtime`](skills/ai-security-runtime/SKILL.md) | AI embedded in applications | Preserve the user’s task and host-verified authorization boundaries while processing external material, calling tools, sharing data, and writing memory. |

The skills address prompt injection, data disclosure, cross-tenant access, excessive tool permissions, unsafe output handling, memory poisoning, invalid approvals, duplicate execution, and resource budgets. Apply controls that match the application’s actual capabilities.

**Skills guide model behavior. The host application must enforce authorization, data isolation, execution limits, and action blocking. Installing a skill does not complete application security hardening.**

The skill instructions and supporting references are currently written in Chinese. This README provides bilingual usage and integration guidance.

### Use the development skill

Copy the entire `skills/ai-security-development/` directory into your coding assistant’s skill directory. For local Codex, use `$CODEX_HOME/skills/`, or `.codex/skills/` under your user directory when `CODEX_HOME` is unset. Preserve the `references/` directory and check for an existing skill before copying so you do not overwrite local changes.

In an assistant that supports this invocation syntax, try:

```text
Use $ai-security-development to add RAG to this project.
Reuse existing authorization and prevent cross-tenant disclosure through
retrieval, citations, and caches. Verify both unauthorized and legitimate requests.
```

For a focused review:

```text
Use $ai-security-development to review this agent's tool execution flow.
Report code-backed risks, attack conditions, fixes, and verification steps.
```

The skill covers data-flow inspection, control selection, scoped implementation, verification, and evidence-based delivery. See the [security verification scenarios](skills/ai-security-development/references/verification.md).

### Integrate the runtime skill

Embed `skills/ai-security-runtime/` as a controlled application resource. `$ai-security-runtime` is an invocation option for environments that support skill syntax; other applications should use their own skill loader.

The host application should:

1. Reliably load the rules before relevant AI workflows, manage their source and version, and avoid relying solely on the model to decide whether to invoke the skill.
2. Supply verified identity, tenant, task scope, tool capabilities, sharing policy, approval state, and budgets through a trusted channel.
3. Check arguments, object permissions, outbound destinations, and valid approvals immediately before execution. Block unauthorized actions even if the model skips the skill.
4. Return verifiable execution status, handle idempotency, cancellation, and unknown outcomes, and record only necessary audit information.

See the [host integration contract](skills/ai-security-runtime/references/host-integration.md) for integration details and acceptance scenarios.

### Validation and limitations

- Skill files passed the creator tool’s basic format validation, and local reference links were checked.
- Supporting files provide test scenarios and observable acceptance criteria. This repository has not performed runtime security testing against a particular application, model, or executor.
- Test adversarial requests and legitimate tasks. Observe actual data flows and side effects rather than relying on the model’s claim that it refused a request.
- These skills do not guarantee elimination of prompt injection or constitute security certification or regulatory compliance. The host defines product content policies and business approval requirements.

## References / 参考资料

The instructions draw on these primary references / 编写参考以下一手资料：

- [OWASP — Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
- [OWASP — Excessive Agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/)
- [NIST — Generative Artificial Intelligence Risk Management Profile](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf)
