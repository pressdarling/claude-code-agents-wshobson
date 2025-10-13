# Agent System Architecture

This document provides a comprehensive deep dive into the Claude Code subagent system, covering agent lifecycle, invocation mechanisms, model selection, coordination patterns, and best practices.

## Table of Contents

- [Agent Fundamentals](#agent-fundamentals)
- [Agent Lifecycle](#agent-lifecycle)
- [Invocation Mechanisms](#invocation-mechanisms)
- [Model Selection Strategy](#model-selection-strategy)
- [Agent Coordination Patterns](#agent-coordination-patterns)
- [Context Management](#context-management)
- [Best Practices](#best-practices)
- [References](#references)

## Agent Fundamentals

### What is a Subagent?

A subagent is a specialized AI assistant within Claude Code with:

- **Dedicated Purpose**: Focused expertise in a specific domain
- **Separate Context**: Isolated context window from the main conversation
- **Custom Behaviour**: Guided by a unique system prompt
- **Configurable Tools**: Optional restrictions on available tools
- **Model Selection**: Ability to use different model tiers (haiku/sonnet/opus)

**Official Reference**: [Claude Code Subagents](https://docs.claude.com/en/docs/claude-code/sub-agents)

### Agent Definition Structure

Agents are defined in Markdown files with YAML frontmatter:

```markdown
---
name: agent-name
description: When and why this agent should be invoked
model: sonnet  # Optional: haiku, sonnet, opus, or inherit
tools: tool1, tool2  # Optional: restrict available tools
---

System prompt defining the agent's expertise, capabilities,
behavioural traits, and response approach.
```

### Storage Locations

Agents can be stored at two levels:

1. **Project-level**: `.claude/agents/` (highest priority)
2. **User-level**: `~/.claude/agents/`

When agent names conflict, **project-level agents override user-level agents**.

## Agent Lifecycle

### Discovery and Selection

```mermaid
graph TD
    START[User Request Received]
    PARSE[Parse User Intent]
    SCAN[Scan Available Agents]

    subgraph "Agent Matching"
        EXPLICIT{Explicit<br/>invocation?}
        AUTO_MATCH[Match description keywords]
        SCORE[Score relevance]
        SELECT[Select highest score]
    end

    LOAD[Load Agent Definition]
    INIT[Initialize Context]
    EXECUTE[Execute Agent]
    RETURN[Return Results]

    START --> PARSE
    PARSE --> SCAN
    SCAN --> EXPLICIT

    EXPLICIT -->|"Yes:<br/>'Use X agent'"| LOAD
    EXPLICIT -->|No| AUTO_MATCH
    AUTO_MATCH --> SCORE
    SCORE --> SELECT
    SELECT --> LOAD

    LOAD --> INIT
    INIT --> EXECUTE
    EXECUTE --> RETURN

    style EXPLICIT fill:#fff4e1
    style AUTO_MATCH fill:#e1f5ff
    style SELECT fill:#e1f5ff
    style EXECUTE fill:#c8e6c9
```

### Agent Initialization Process

When an agent is invoked:

1. **Load Definition**: Read agent frontmatter and system prompt
2. **Model Selection**: Determine model tier (see [Model Selection Strategy](#model-selection-strategy))
3. **Tool Configuration**: Apply tool restrictions if specified
4. **Context Isolation**: Create separate context window
5. **Prompt Construction**: Combine system prompt + user request
6. **Execution**: Process request with selected model
7. **Result Return**: Send output back to Claude Code

## Invocation Mechanisms

### Automatic Invocation

Claude Code automatically selects agents based on description matching:

**Example Agent Description**:
```yaml
description: Expert backend architect specializing in scalable API design.
  Masters REST/GraphQL/gRPC APIs. Use PROACTIVELY when creating new backend
  services or APIs.
```

**Trigger Phrases**:
- **"Use PROACTIVELY"**: Strong signal for automatic selection
- **Domain keywords**: "API design", "scalable", "REST", "GraphQL"
- **Intent matching**: "Create backend service" → triggers backend-architect

### Explicit Invocation

Users can directly request specific agents:

```
"Use the backend-architect agent to design an authentication API"
"Have security-auditor scan this code for vulnerabilities"
"Get performance-engineer to optimize these database queries"
```

### Invocation Decision Tree

```mermaid
graph TD
    REQUEST[User Request]
    CHECK_EXPLICIT{Contains<br/>'use X agent'?}
    DIRECT[Direct Agent Selection]

    CHECK_KEYWORDS{Description<br/>keywords match?}
    CHECK_PROACTIVE{Contains<br/>'PROACTIVELY'?}
    SCORE_RELEVANCE[Score Relevance]

    HIGH_SCORE{Score > threshold?}
    AUTO_SELECT[Automatic Selection]
    FALLBACK[Use Main Claude Context]

    REQUEST --> CHECK_EXPLICIT
    CHECK_EXPLICIT -->|Yes| DIRECT
    CHECK_EXPLICIT -->|No| CHECK_KEYWORDS

    CHECK_KEYWORDS -->|Yes| CHECK_PROACTIVE
    CHECK_KEYWORDS -->|No| FALLBACK

    CHECK_PROACTIVE -->|Yes| SCORE_RELEVANCE
    CHECK_PROACTIVE -->|No| SCORE_RELEVANCE

    SCORE_RELEVANCE --> HIGH_SCORE
    HIGH_SCORE -->|Yes| AUTO_SELECT
    HIGH_SCORE -->|No| FALLBACK

    style DIRECT fill:#c8e6c9
    style AUTO_SELECT fill:#c8e6c9
    style FALLBACK fill:#ffcdd2
```

## Model Selection Strategy

### Model Tiers

This marketplace uses three model tiers based on task complexity:

```mermaid
graph LR
    subgraph "Model Distribution"
        H[Haiku<br/>11 agents<br/>Fast & Economical]
        S[Sonnet<br/>50 agents<br/>Balanced Performance]
        O[Opus<br/>23 agents<br/>Complex Reasoning]
    end

    subgraph "Use Cases"
        H_USE[Quick tasks<br/>SEO optimization<br/>Content planning<br/>Simple searches]
        S_USE[Standard development<br/>Code implementation<br/>Framework expertise<br/>Testing automation]
        O_USE[Architecture design<br/>Security audits<br/>Complex analysis<br/>ML/AI systems]
    end

    H -.-> H_USE
    S -.-> S_USE
    O -.-> O_USE

    style H fill:#fff9c4
    style S fill:#c8e6c9
    style O fill:#e1bee7
```

### Model Selection Algorithm

```mermaid
flowchart TD
    START[Agent Invoked]
    CHECK_FRONTMATTER{Model specified<br/>in frontmatter?}

    CHECK_INHERIT{model:<br/>inherit?}
    USE_PARENT[Use parent<br/>context model]

    USE_SPECIFIED[Use specified model:<br/>haiku/sonnet/opus]

    EXECUTE[Execute with<br/>selected model]

    START --> CHECK_FRONTMATTER
    CHECK_FRONTMATTER -->|No| USE_PARENT
    CHECK_FRONTMATTER -->|Yes| CHECK_INHERIT

    CHECK_INHERIT -->|Yes| USE_PARENT
    CHECK_INHERIT -->|No| USE_SPECIFIED

    USE_PARENT --> EXECUTE
    USE_SPECIFIED --> EXECUTE

    style USE_SPECIFIED fill:#c8e6c9
    style USE_PARENT fill:#fff9c4
    style EXECUTE fill:#e1bee7
```

### Model Tier Guidelines

#### Haiku Agents (11 agents)

**Characteristics**:
- Fast execution
- Lower computational cost
- Deterministic tasks
- Minimal reasoning required

**Examples**:
- `seo-meta-optimizer`: Meta tag optimization
- `seo-keyword-strategist`: Keyword analysis
- `context-manager`: Context tracking
- `search-specialist`: Web searches

#### Sonnet Agents (50 agents)

**Characteristics**:
- Balanced performance and cost
- Standard development tasks
- Framework and language expertise
- Most common use cases

**Examples**:
- `python-pro`: Python development
- `frontend-developer`: React/Next.js implementation
- `test-automator`: Test suite creation
- `deployment-engineer`: CI/CD and deployment

#### Opus Agents (23 agents)

**Characteristics**:
- Complex reasoning
- Architectural decisions
- Security-critical tasks
- Advanced analysis

**Examples**:
- `backend-architect`: API and service architecture
- `security-auditor`: Vulnerability assessment
- `ml-engineer`: Machine learning pipelines
- `code-reviewer`: Production reliability review

## Agent Coordination Patterns

### Deference Pattern

Agents defer to specialists for their areas of expertise:

```mermaid
graph LR
    BA[backend-architect]
    DA[database-architect]
    CA[cloud-architect]
    KA[kubernetes-architect]

    BA -->|"Defers database<br/>schema design"| DA
    BA -->|"Coordinates with<br/>for deployment"| CA
    CA -->|"Defers container<br/>orchestration"| KA

    style BA fill:#e1f5ff
    style DA fill:#c8e6c9
    style CA fill:#c8e6c9
    style KA fill:#c8e6c9
```

**Example from `backend-architect` agent**:
> "Defers database schema design to database-architect (works after data layer is designed)"

### Workflow Orchestration Pattern

Workflows coordinate multiple agents through phases:

```mermaid
sequenceDiagram
    participant User
    participant Workflow
    participant Agent1 (Phase 1)
    participant Agent2 (Phase 2)
    participant Agent3 (Phase 3)

    User->>Workflow: Request feature implementation

    Workflow->>Agent1 (Phase 1): Task tool: business-analyst
    Note over Agent1 (Phase 1): Analyze requirements<br/>Define user stories

    Agent1 (Phase 1)->>Workflow: Requirements document

    Workflow->>Agent2 (Phase 2): Task tool: backend-architect<br/>Context: [requirements from Phase 1]
    Note over Agent2 (Phase 2): Design API<br/>Define architecture

    Agent2 (Phase 2)->>Workflow: Architecture design

    Workflow->>Agent3 (Phase 3): Task tool: test-automator<br/>Context: [architecture from Phase 2]
    Note over Agent3 (Phase 3): Create test suites<br/>Validate implementation

    Agent3 (Phase 3)->>Workflow: Test results
    Workflow->>User: Complete feature with tests
```

### Parallel Execution Pattern

Some workflows execute agents in parallel for independent tasks:

```mermaid
graph TB
    WORKFLOW[Workflow Initiates]

    subgraph "Parallel Phase"
        A1[Agent 1:<br/>code-reviewer]
        A2[Agent 2:<br/>security-auditor]
        A3[Agent 3:<br/>performance-engineer]
    end

    MERGE[Merge Results]
    OUTPUT[Combined Analysis]

    WORKFLOW --> A1
    WORKFLOW --> A2
    WORKFLOW --> A3

    A1 --> MERGE
    A2 --> MERGE
    A3 --> MERGE

    MERGE --> OUTPUT

    style WORKFLOW fill:#fff9c4
    style MERGE fill:#e1f5ff
    style OUTPUT fill:#c8e6c9
```

## Context Management

### Context Isolation

Each agent operates in a **separate context window**:

```mermaid
graph TB
    subgraph "Main Claude Code Context"
        MAIN[Primary Conversation<br/>User History<br/>Project Context]
    end

    subgraph "Agent 1 Context (Isolated)"
        A1_SYS[System Prompt:<br/>backend-architect]
        A1_REQ[User Request]
        A1_HIST[Agent Conversation]
    end

    subgraph "Agent 2 Context (Isolated)"
        A2_SYS[System Prompt:<br/>security-auditor]
        A2_REQ[User Request]
        A2_HIST[Agent Conversation]
    end

    MAIN -->|"Invoke Agent 1"| A1_REQ
    MAIN -->|"Invoke Agent 2"| A2_REQ

    A1_HIST -.->|"Results only"| MAIN
    A2_HIST -.->|"Results only"| MAIN

    style MAIN fill:#e1f5ff
    style A1_SYS fill:#c8e6c9
    style A2_SYS fill:#c8e6c9
```

**Benefits**:
- Prevents context pollution
- Maintains agent focus
- Enables parallel execution
- Clear responsibility boundaries

### Context Passing in Workflows

Workflows explicitly pass context between phases:

```markdown
## Phase 2: Implementation
- Use Task tool with subagent_type="backend-architect"
- Prompt: "Implement backend services for: X.
  Follow technical design: [include architecture from step 2]..."
- Expected output: Backend service code
- Context: Architecture designs from Phase 1, database schema
```

## Best Practices

### 1. Agent Definition

✅ **Do**:
- Write clear, specific descriptions
- Include "use PROACTIVELY" for automatic invocation
- Specify appropriate model tier
- Define clear expertise boundaries
- Document when to defer to other agents

❌ **Don't**:
- Create overly broad agent descriptions
- Overlap responsibilities with existing agents
- Skip model specification (leads to unpredictable behaviour)
- Make agents depend on undocumented context

### 2. Model Selection

✅ **Do**:
- Use **haiku** for simple, deterministic tasks
- Use **sonnet** for standard development work
- Use **opus** for architecture, security, complex reasoning
- Consider cost-performance trade-offs

❌ **Don't**:
- Use opus for simple tasks (unnecessary cost)
- Use haiku for complex reasoning (poor results)
- Leave model unspecified without good reason

### 3. Agent Coordination

✅ **Do**:
- Implement clear deference patterns
- Document agent dependencies
- Pass explicit context between workflow phases
- Use parallel execution when tasks are independent

❌ **Don't**:
- Create circular dependencies
- Assume agents share context
- Skip context passing documentation
- Force sequential execution of independent tasks

### 4. Tool Configuration

✅ **Do**:
- Restrict tools for security-sensitive agents
- Grant minimal necessary tool access
- Document why tools are restricted

❌ **Don't**:
- Give all agents unrestricted tool access
- Over-restrict tools without testing
- Forget to document tool restrictions

## References

### Official Documentation

- [Subagents](https://docs.claude.com/en/docs/claude-code/sub-agents)
- [Plugins](https://docs.claude.com/en/docs/claude-code/plugins)
- [Claude Code Overview](https://docs.claude.com/en/docs/claude-code/overview)

### Related Documentation

- [System Architecture Overview](./system-overview.md)
- [Workflow Orchestration Patterns](./workflow-patterns.md)
- [Agent Frontmatter API Specification](../api/agent-frontmatter-spec.md)

### Agent Examples in This Repository

- [`backend-architect.md`](../../agents/backend-architect.md) - Opus agent with comprehensive architecture
- [`python-pro.md`](../../agents/python-pro.md) - Sonnet agent for language expertise
- [`seo-meta-optimizer.md`](../../agents/seo-meta-optimizer.md) - Haiku agent for focused tasks

---

**Next Steps**:
- Explore [Workflow Orchestration Patterns](./workflow-patterns.md) for multi-agent coordination
- Review [Agent Frontmatter Specification](../api/agent-frontmatter-spec.md) for detailed API reference
- Study example agents in the [agents/](../../agents/) directory
