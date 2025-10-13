# System Architecture Overview

This document provides a comprehensive overview of the Claude Code Agents Marketplace architecture, including plugin structure, agent invocation mechanisms, and component relationships.

## Table of Contents

- [Plugin Marketplace Architecture](#plugin-marketplace-architecture)
- [Agent Invocation Architecture](#agent-invocation-architecture)
- [Component Architecture](#component-architecture)
- [Key Architectural Principles](#key-architectural-principles)
- [References](#references)

## Plugin Marketplace Architecture

This repository functions as a Claude Code plugin marketplace, distributing specialized agents, workflows, and tools through a structured plugin system.

### Architecture Diagram

```mermaid
graph TB
    subgraph "Marketplace Repository"
        MKT[marketplace.json<br/>Marketplace Catalog]

        subgraph "Plugin Definitions"
            P1[Plugin: claude-code-essentials]
            P2[Plugin: full-stack-development]
            P3[Plugin: security-hardening]
            PN[Plugin: ... 21 more plugins]
        end
    end

    subgraph "Plugin Components"
        subgraph "Agents Directory"
            A1[backend-architect.md]
            A2[frontend-developer.md]
            AN[... 84 agents total]
        end

        subgraph "Workflows Directory"
            W1[feature-development.md]
            W2[full-stack-feature.md]
            WN[... 15 workflows total]
        end

        subgraph "Tools Directory"
            T1[api-scaffold.md]
            T2[doc-generate.md]
            TN[... 42 tools total]
        end
    end

    subgraph "Claude Code Installation"
        USER[User runs:<br/>/plugin marketplace add<br/>wshobson/agents]
        BROWSE[/plugin command<br/>Browse available plugins]
        INSTALL[Plugin Installation]
    end

    subgraph "Runtime Environment"
        PROJECT[".claude/agents/<br/>(project-level)"]
        USERLVL["~/.claude/agents/<br/>(user-level)"]
        PRECEDENCE[Project agents<br/>override user agents]
    end

    MKT --> P1
    MKT --> P2
    MKT --> P3
    MKT --> PN

    P1 -.->|references| A1
    P1 -.->|references| T1
    P2 -.->|references| A2
    P2 -.->|references| W1
    P3 -.->|references| A1

    USER --> MKT
    MKT --> BROWSE
    BROWSE --> INSTALL
    INSTALL --> PROJECT
    INSTALL --> USERLVL
    PROJECT --> PRECEDENCE
    USERLVL --> PRECEDENCE

    style MKT fill:#e1f5ff
    style PRECEDENCE fill:#fff4e1
    style PROJECT fill:#e8f5e9
    style USERLVL fill:#f3e5f5
```

### Installation Flow

1. **Marketplace Addition**: User adds marketplace with `/plugin marketplace add wshobson/agents`
2. **Plugin Discovery**: Browse plugins using `/plugin` command
3. **Plugin Installation**: Select and install specific plugin(s)
4. **Agent Registration**: Agents are copied to `.claude/agents/` (project) or `~/.claude/agents/` (user)
5. **Precedence Resolution**: Project-level agents take priority over user-level agents

**Official Documentation**: [Plugin Marketplaces](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)

## Agent Invocation Architecture

Agents can be invoked automatically (based on description matching) or explicitly (direct user request). Each agent runs in a separate context window with configurable model tiers.

### Invocation Flow Diagram

```mermaid
sequenceDiagram
    participant User
    participant Claude Code
    participant Agent Selection
    participant Agent (Separate Context)
    participant Model (haiku/sonnet/opus)

    User->>Claude Code: Request (e.g., "Design an API")

    alt Automatic Invocation
        Claude Code->>Agent Selection: Analyze request + agent descriptions
        Agent Selection->>Agent Selection: Match keywords, intent, "PROACTIVELY"
        Agent Selection->>Claude Code: Select: backend-architect (opus)
    else Explicit Invocation
        User->>Claude Code: "Use backend-architect agent"
        Claude Code->>Agent Selection: Direct selection
    end

    Claude Code->>Agent (Separate Context): Initialize agent with:
    Note over Agent (Separate Context): - System prompt from agent.md<br/>- User request<br/>- Separate context window<br/>- Model tier configuration

    Agent (Separate Context)->>Model (haiku/sonnet/opus): Process with selected model tier
    Model (haiku/sonnet/opus)->>Agent (Separate Context): Generate response

    Agent (Separate Context)->>Claude Code: Return results
    Claude Code->>User: Display agent output

    Note over Agent (Separate Context): Context is isolated<br/>from main conversation
```

### Model Selection Logic

```mermaid
graph TD
    START[Agent Invoked]
    CHECK{Model specified<br/>in frontmatter?}
    INHERIT{Model = inherit?}
    ASSIGN[Use specified model:<br/>haiku/sonnet/opus]
    DEFAULT[Use parent context model]
    EXECUTE[Execute agent with model]

    START --> CHECK
    CHECK -->|Yes| INHERIT
    CHECK -->|No| DEFAULT
    INHERIT -->|Yes| DEFAULT
    INHERIT -->|No| ASSIGN
    ASSIGN --> EXECUTE
    DEFAULT --> EXECUTE

    style ASSIGN fill:#c8e6c9
    style DEFAULT fill:#fff9c4
    style EXECUTE fill:#e1f5ff
```

**Model Distribution**:
- **Haiku** (11 agents): Quick tasks, SEO, search, content
- **Sonnet** (50 agents): Standard development, language expertise
- **Opus** (23 agents): Architecture, security, complex analysis, ML/AI

**Official Documentation**: [Subagents](https://docs.claude.com/en/docs/claude-code/sub-agents)

## Component Architecture

The marketplace provides three types of components that work together to enable complex development workflows.

### Component Hierarchy

```mermaid
graph TB
    subgraph "Component Types"
        direction TB

        subgraph "Workflows (15)"
            WF1[feature-development]
            WF2[full-stack-feature]
            WF3[security-hardening]
            WF4[ml-pipeline]
            WFN[... 11 more]
        end

        subgraph "Agents (84)"
            subgraph "Architecture (7)"
                AG1[backend-architect<br/>opus]
                AG2[cloud-architect<br/>opus]
                AG3[database-architect<br/>opus]
            end

            subgraph "Languages (18)"
                AG4[python-pro<br/>sonnet]
                AG5[typescript-pro<br/>sonnet]
                AG6[rust-pro<br/>sonnet]
            end

            subgraph "Quality (8)"
                AG7[code-reviewer<br/>opus]
                AG8[security-auditor<br/>opus]
                AG9[test-automator<br/>sonnet]
            end

            subgraph "Data/AI (4)"
                AG10[data-scientist<br/>opus]
                AG11[ml-engineer<br/>opus]
                AG12[ai-engineer<br/>opus]
            end

            AGN[... 47 more agents]
        end

        subgraph "Tools (42)"
            T1[api-scaffold.md]
            T2[security-scan.md]
            T3[doc-generate.md]
            TN[... 39 more]
        end
    end

    subgraph "Orchestration Patterns"
        USER_REQ[User Request]
        WORKFLOW_SEL{Workflow or<br/>Direct Agent?}
        TASK_TOOL[Task Tool Invocation]
        AGENT_EXEC[Agent Execution]
        CONTEXT[Context Passing]
    end

    USER_REQ --> WORKFLOW_SEL
    WORKFLOW_SEL -->|Workflow| WF1
    WORKFLOW_SEL -->|Direct| AG1

    WF1 -->|Phase 1| TASK_TOOL
    TASK_TOOL -->|"subagent_type=<br/>backend-architect"| AG1
    AG1 --> AGENT_EXEC
    AGENT_EXEC --> CONTEXT
    CONTEXT -->|Pass to Phase 2| TASK_TOOL

    WF1 -.->|Uses| T1
    WF1 -.->|Uses| T2

    AG1 -.->|Defers to| AG3
    AG7 -.->|Analyzes output from| AG1

    style WF1 fill:#e1f5ff
    style AG1 fill:#fce4ec
    style AG7 fill:#fce4ec
    style AG3 fill:#fce4ec
    style T1 fill:#f3e5f5
    style TASK_TOOL fill:#fff9c4
```

### Agent Specialization and Deference

Agents follow a **deference pattern** where specialized agents delegate specific tasks to domain experts:

- **backend-architect** defers database schema design to **database-architect**
- **cloud-architect** defers container optimization to **kubernetes-architect**
- **performance-engineer** defers query optimization to **database-optimizer**

This ensures deep expertise in each domain while maintaining clear boundaries.

### Workflow Orchestration

Workflows coordinate multiple agents through phases:

**Example: `feature-development` workflow**
1. **Phase 1: Discovery** → `business-analyst`, `architect-review`
2. **Phase 2: Implementation** → `backend-architect`, `frontend-developer`
3. **Phase 3: Testing** → `test-automator`, `security-auditor`
4. **Phase 4: Deployment** → `deployment-engineer`, `observability-engineer`

Each phase uses the **Task tool** to invoke agents:
```markdown
- Use Task tool with subagent_type="backend-architect"
- Prompt: "Implement services for: X. Using architecture from Phase 1..."
```

## Key Architectural Principles

### 1. Separation of Concerns
- **Agents**: Specialized domain expertise
- **Workflows**: Orchestration and coordination
- **Tools**: Focused utilities and code generation

### 2. Context Isolation
- Each agent runs in a separate context window
- Prevents context pollution
- Enables parallel agent execution
- Maintains focus on specific tasks

### 3. Model Tier Optimization
- **Haiku**: Fast, deterministic, low-cost tasks
- **Sonnet**: Balanced performance for most development tasks
- **Opus**: Complex reasoning, architecture, critical analysis

### 4. Progressive Enhancement
- Start with essentials (claude-code-essentials plugin)
- Add workflow orchestration as needed
- Install specialized plugins for specific domains
- Custom plugins for team-specific needs

### 5. Explicit over Implicit
- Clear frontmatter specifications
- Well-defined invocation descriptions
- Documented context passing
- Traceable workflow phases

## References

### Official Claude Code Documentation

- [Claude Code Overview](https://docs.claude.com/en/docs/claude-code/overview)
- [Subagents](https://docs.claude.com/en/docs/claude-code/sub-agents)
- [Plugins](https://docs.claude.com/en/docs/claude-code/plugins)
- [Plugin Marketplaces](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [Plugins Reference](https://docs.claude.com/en/docs/claude-code/plugins-reference)

### Related Documentation in This Repository

- [Agent System Deep Dive](./agent-system.md)
- [Workflow Orchestration Patterns](./workflow-patterns.md)
- [Agent Frontmatter Specification](../api/agent-frontmatter-spec.md)
- [Workflow Specification](../api/workflow-spec.md)
- [Plugin Manifest Specification](../api/plugin-manifest-spec.md)

### Community Resources

- [Repository Homepage](https://github.com/wshobson/agents)
- [Issue Tracker](https://github.com/wshobson/agents/issues)
- [Contributing Guidelines](../../.github/CONTRIBUTING.md)

---

**Next Steps**:
- Review [Agent System Architecture](./agent-system.md) for deeper understanding of agent mechanics
- Explore [Workflow Patterns](./workflow-patterns.md) to learn orchestration strategies
- Check [API Reference](../api/) for specifications and examples
