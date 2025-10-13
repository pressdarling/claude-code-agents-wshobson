# Agent Frontmatter API Specification

Complete technical specification for Claude Code subagent frontmatter format, field definitions, validation rules, and examples.

**Official Reference**: [Claude Code Subagents](https://docs.claude.com/en/docs/claude-code/sub-agents)

## Table of Contents

- [Specification Overview](#specification-overview)
- [Frontmatter Format](#frontmatter-format)
- [Field Definitions](#field-definitions)
- [Validation Rules](#validation-rules)
- [Model Selection](#model-selection)
- [Tool Configuration](#tool-configuration)
- [Examples](#examples)
- [Best Practices](#best-practices)

## Specification Overview

Agent definitions use Markdown files with YAML frontmatter followed by a system prompt:

```markdown
---
name: agent-name
description: Agent purpose and invocation criteria
model: sonnet
tools: tool1, tool2
---

System prompt defining agent's expertise and behaviour...
```

**File Location**:
- Project-level: `.claude/agents/`
- User-level: `~/.claude/agents/`

**Precedence**: Project-level agents override user-level agents with the same name.

## Frontmatter Format

###Yaml Structure

```yaml
---
name: string (required)
description: string (required)
model: "haiku" | "sonnet" | "opus" | "inherit" (optional)
tools: string | string[] (optional)
---
```

## Field Definitions

### `name` (required)

**Type**: `string`
**Format**: Kebab-case, lowercase, no spaces
**Purpose**: Unique identifier for the agent

**Naming Conventions**:
- Use hyphen-separated words
- Descriptive and specific
- Avoid generic names
- Keep concise but clear

**Valid Examples**:
```yaml
name: backend-architect
name: python-pro
name: seo-meta-optimizer
name: database-architect
```

**Invalid Examples**:
```yaml
name: BackendArchitect      # CamelCase not allowed
name: backend_architect     # Underscore not allowed
name: backend architect     # Space not allowed
name: architect             # Too generic
```

### `description` (required)

**Type**: `string`
**Format**: Multi-line string with specific invocation criteria
**Purpose**: Describes when and why the agent should be invoked

**Structure**:
1. **Primary Purpose** (1-2 sentences)
2. **Specialized Capabilities** (key strengths)
3. **Invocation Trigger** (keywords, "use PROACTIVELY")

**Example**:
```yaml
description: Expert backend architect specializing in scalable API design,
  microservices architecture, and distributed systems. Masters REST/GraphQL/gRPC
  APIs, event-driven architectures, service mesh patterns, and modern backend
  frameworks. Use PROACTIVELY when creating new backend services or APIs.
```

**Key Elements**:
- **Domain expertise**: "Expert backend architect"
- **Specific technologies**: "REST/GraphQL/gRPC", "microservices"
- **Invocation trigger**: "Use PROACTIVELY when creating new backend services"

**Invocation Keywords**:
- `use PROACTIVELY` - Strongly encourages automatic selection
- Domain-specific terms - Match user intent (e.g., "API design", "scalable")
- Action verbs - "creating", "designing", "implementing", "optimizing"

### `model` (optional)

**Type**: `"haiku" | "sonnet" | "opus" | "inherit"`
**Default**: Uses configured subagent model (typically `sonnet`). Use `"inherit"` to explicitly match parent context model.
**Purpose**: Specifies which Claude model tier to use

**Model Tiers**:

```yaml
# Fast, economical for simple tasks
model: haiku

# Balanced performance for most development
model: sonnet

# Complex reasoning and analysis
model: opus

# Explicitly use parent conversation's model
model: inherit

# Omit to use configured subagent model (typically sonnet)
# (no model field)
```

**Selection Guidelines**:

| Model | Cost | Speed | Best For | Example Agents |
|-------|------|-------|----------|----------------|
| **haiku** | Low | Fast | Quick tasks, SEO, searches | `seo-meta-optimizer`, `context-manager` |
| **sonnet** | Medium | Balanced | Standard development, implementation | `python-pro`, `frontend-developer` |
| **opus** | High | Slower | Architecture, security, complex analysis | `backend-architect`, `security-auditor` |
| **inherit** | Varies | Varies | Explicitly match parent context | Custom agents needing consistency |

**Official Reference**: [Subagents - Model Selection](https://docs.claude.com/en/docs/claude-code/sub-agents#model-selection)

### `tools` (optional)

**Type**: `string` (comma-separated)
**Purpose**: Restricts which Claude Code tools the agent can access
**Default**: All tools available (if omitted)

**Format**:

```yaml
# Comma-separated string
tools: Read, Write, Bash, Grep
```

**Use Cases**:
- **Security**: Restrict dangerous operations (e.g., read-only agents)
- **Focus**: Limit distractions for specialized tasks
- **Safety**: Prevent unintended system modifications

**Example**:
```yaml
# Read-only documentation agent
tools: Read, Grep, Glob

# Code analysis without modification
tools: Read, Grep, Bash(git log), Bash(git diff)

# Full access (default behaviour)
# tools field omitted
```

**Official Reference**: [Subagents - Tool Access](https://docs.claude.com/en/docs/claude-code/sub-agents#tool-access)

## Validation Rules

### Required Fields

✅ **Must have**:
- `name`: Non-empty string, kebab-case format
- `description`: Non-empty string with clear invocation criteria

### Optional Fields

🔧 **Can omit**:
- `model`: Defaults to configured subagent model (typically `sonnet`)
- `tools`: Defaults to all tools

### Format Validation

```yaml
# ✅ Valid
name: backend-architect
description: Expert in API design. Use PROACTIVELY for backend services.
model: opus
tools: Read, Write, Bash, Grep

# ❌ Invalid - missing name
description: Some description
model: opus

# ❌ Invalid - wrong name format
name: Backend_Architect
description: Some description

# ❌ Invalid - empty description
name: backend-architect
description:

# ❌ Invalid - unsupported model
name: backend-architect
description: Some description
model: claude-3-opus  # Must use: haiku, sonnet, opus, or inherit
```

## Model Selection

### Decision Flow

```yaml
# Model explicitly specified
model: opus
# → Uses opus regardless of parent context

# Model set to inherit
model: inherit
# → Uses same model as parent conversation

# Model field omitted
# → Uses configured subagent model (typically sonnet)
```

### Cost-Performance Trade-offs

**Model Selection Matrix**:

```yaml
# High-frequency, simple tasks
model: haiku          # Best choice
# Example: SEO optimization, keyword analysis

# Standard development work
model: sonnet         # Best choice
# Example: Code implementation, testing, refactoring

# Critical decisions, architecture
model: opus           # Best choice
# Example: System design, security audits, complex analysis

# Internal utility agents
model: inherit        # Best choice
# Example: Context managers, formatters
```

## Tool Configuration

### Common Tool Configurations

```yaml
# 1. Read-only analysis
tools: Read, Grep, Glob
# Use for: Code reviewers, documentation analysts

# 2. Code implementation
tools: Read, Write, Edit, Bash
# Use for: Developer agents, code generators

# 3. Git operations
tools: Read, Bash(git), Grep
# Use for: Version control agents, code historians

# 4. Testing and validation
tools: Read, Bash, Grep
# Use for: Test runners, validators

# 5. Full access (default)
# tools field omitted
# Use for: Trusted, general-purpose agents
```

### Security Considerations

```yaml
# ⚠️ High-risk: Full filesystem access
tools: Read, Write, Edit, Bash

# ✅ Low-risk: Read-only
tools: Read, Grep, Glob

# ⚠️ Medium-risk: Can execute commands
tools: Read, Bash

# ✅ Recommended: Minimal necessary tools
tools: Read, Grep  # Only what's needed
```

## Examples

### Example 1: Architecture Agent (Opus)

```yaml
---
name: backend-architect
description: Expert backend architect specializing in scalable API design,
  microservices architecture, and distributed systems. Masters REST/GraphQL/gRPC
  APIs, event-driven architectures, service mesh patterns, and modern backend
  frameworks. Handles service boundary definition, inter-service communication,
  resilience patterns, and observability. Use PROACTIVELY when creating new
  backend services or APIs.
model: opus
---

You are a backend system architect specializing in scalable, resilient,
and maintainable backend systems and APIs.

## Purpose
Expert backend architect with comprehensive knowledge of modern API design...
```

### Example 2: Language Specialist (Sonnet)

```yaml
---
name: python-pro
description: Master Python 3.12+ with modern features, async programming,
  performance optimization, and production-ready practices. Expert in the
  latest Python ecosystem including uv, ruff, pydantic, and FastAPI.
  Use PROACTIVELY for Python development, optimization, or advanced
  Python patterns.
model: sonnet
---

You are a Python expert specializing in modern Python 3.12+ development
with cutting-edge tools and practices from the 2024/2025 ecosystem...
```

### Example 3: Quick Task Agent (Haiku)

```yaml
---
name: seo-meta-optimizer
description: Optimize meta titles and descriptions for search engines.
  Analyze existing meta tags, suggest improvements based on keyword
  research, and ensure optimal character limits (60 for titles, 160 for
  descriptions). Use for SEO meta tag optimization tasks.
model: haiku
tools: Read, Grep
---

You are an SEO specialist focused on meta tag optimization...
```

### Example 4: Read-Only Reviewer (Sonnet)

```yaml
---
name: code-reviewer
description: Comprehensive code review specialist analyzing code quality,
  security vulnerabilities, performance bottlenecks, and production reliability.
  Use PROACTIVELY after code implementation or when explicitly requested
  for code review.
model: opus
tools: Read, Grep, Bash(git diff), Bash(git log)
---

You are a senior code reviewer with expertise in production systems...
```

## Best Practices

### Naming

✅ **Do**:
- Use descriptive, specific names
- Follow kebab-case convention
- Keep names concise (2-4 words)
- Make purpose clear from name

❌ **Don't**:
- Use generic names (`agent`, `helper`)
- Mix naming conventions
- Create overly long names
- Use abbreviations without context

### Description

✅ **Do**:
- Start with primary expertise
- List specific technologies/domains
- Include "use PROACTIVELY" for automatic invocation
- Use action-oriented language

❌ **Don't**:
- Write vague descriptions
- Omit invocation triggers
- List unrelated capabilities
- Use overly technical jargon

### Model Selection

✅ **Do**:
- Choose haiku for simple, fast tasks
- Choose sonnet for standard development
- Choose opus for architecture/security
- Consider cost vs. performance

❌ **Don't**:
- Use opus for simple tasks
- Use haiku for complex reasoning
- Leave model unspecified without reason
- Ignore performance implications

### Tool Configuration

✅ **Do**:
- Grant minimal necessary access
- Document why tools are restricted
- Test with restricted tools
- Consider security implications

❌ **Don't**:
- Grant unnecessary tool access
- Over-restrict without testing
- Forget to document restrictions
- Skip security review

## References

### Official Documentation

- [Subagents](https://docs.claude.com/en/docs/claude-code/sub-agents)
- [Subagents - Invocation Methods](https://docs.claude.com/en/docs/claude-code/sub-agents#invocation-methods)
- [Subagents - Model Selection](https://docs.claude.com/en/docs/claude-code/sub-agents#model-selection)
- [Subagents - Tool Access](https://docs.claude.com/en/docs/claude-code/sub-agents#tool-access)

### Related Documentation

- [Agent System Architecture](../architecture/agent-system.md)
- [System Overview](../architecture/system-overview.md)
- [Workflow Specification](./workflow-spec.md)

### Example Agents

Real agents from this repository:

- [`backend-architect.md`](../../agents/backend-architect.md) - Opus architecture agent
- [`python-pro.md`](../../agents/python-pro.md) - Sonnet language specialist
- [`seo-meta-optimizer.md`](../../agents/seo-meta-optimizer.md) - Haiku focused task
- [`code-reviewer.md`](../../agents/code-reviewer.md) - Opus with tool restrictions

---

**Next Steps**:
- Review [Agent System Architecture](../architecture/agent-system.md) for deeper understanding
- Study examples in [agents/](../../agents/) directory
- Create your first custom agent following this specification
- Test agent invocation with explicit and automatic methods
