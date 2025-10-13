# Documentation

Comprehensive documentation for the Claude Code Agents Marketplace, covering architecture, API specifications, examples, and best practices.

**Official Claude Code Documentation**: [docs.claude.com/claude-code](https://docs.claude.com/en/docs/claude-code/overview)

## Quick Navigation

### For New Users

Start here if you're new to Claude Code agents and workflows:

1. **[System Architecture Overview](./architecture/system-overview.md)** - Understand the marketplace structure and how components work together
2. **[Agent System Architecture](./architecture/agent-system.md)** - Learn how agents are invoked, selected, and coordinated
3. **[Agent Frontmatter Specification](./api/agent-frontmatter-spec.md)** - Create your first custom agent

### For Workflow Developers

Building multi-agent workflows:

1. **[Workflow Orchestration Patterns](./architecture/workflow-patterns.md)** - Learn orchestration strategies
2. **[Workflow Specification](./api/workflow-spec.md)** - Complete workflow format reference
3. **[Examples](../workflows/)** - Browse 15 production workflows

### For Plugin Creators

Creating and distributing plugins:

1. **[Plugin Manifest Specification](./api/plugin-manifest-spec.md)** - Complete marketplace.json reference
2. **[marketplace.json](../.claude-plugin/marketplace.json)** - Live example with 24 plugins
3. **[Contributing Guidelines](../.github/CONTRIBUTING.md)** - Submit plugins to marketplace

### For Claude Code Developers

Working with this codebase:

1. **[CLAUDE.md](../CLAUDE.md)** - Repository-specific guidance for Claude Code instances
2. **[README.md](../README.md)** - Project overview and installation
3. **[CONTRIBUTING.md](../.github/CONTRIBUTING.md)** - Contribution guidelines

## Documentation Structure

```
docs/
├── README.md                          # This file - navigation hub
├── architecture/                      # Conceptual documentation
│   ├── system-overview.md            # High-level architecture with diagrams
│   ├── agent-system.md               # Agent lifecycle and coordination
│   └── workflow-patterns.md          # Orchestration patterns
└── api/                              # Technical specifications
    ├── agent-frontmatter-spec.md     # Agent definition format
    ├── workflow-spec.md              # Workflow format
    └── plugin-manifest-spec.md       # Plugin manifest (marketplace.json)
```

## Architecture Documentation

### System Overview

**[System Architecture Overview](./architecture/system-overview.md)**

High-level architecture of the Claude Code Agents Marketplace, including:

- Plugin marketplace structure
- Agent invocation architecture
- Component relationships (agents, workflows, tools)
- Installation and precedence flows

**Key Diagrams**:
- Plugin Marketplace Architecture
- Agent Invocation Flow with Model Selection
- Component Hierarchy

**Audience**: All users, especially those new to the marketplace

---

### Agent System

**[Agent System Architecture](./architecture/agent-system.md)**

Deep dive into how agents work within Claude Code:

- Agent fundamentals and lifecycle
- Invocation mechanisms (automatic vs explicit)
- Model selection strategy (haiku/sonnet/opus)
- Agent coordination patterns
- Context management and isolation

**Key Diagrams**:
- Agent Discovery and Selection Flow
- Model Selection Algorithm
- Deference Pattern
- Context Isolation Architecture

**Audience**: Agent developers, workflow orchestrators

---

### Workflow Patterns

**[Workflow Orchestration Patterns](./architecture/workflow-patterns.md)**

Multi-agent workflow coordination strategies:

- Workflow fundamentals
- Sequential pipeline pattern
- Parallel execution pattern
- Iterative refinement pattern
- Phase management
- Context passing strategies

**Key Diagrams**:
- Sequential Pipeline Flow
- Parallel Execution Graph
- Iterative Refinement Cycle

**Audience**: Workflow developers, orchestration designers

## API Reference

### Agent Frontmatter Specification

**[Agent Frontmatter API Specification](./api/agent-frontmatter-spec.md)**

Complete technical specification for agent definition format:

- YAML frontmatter structure
- Field definitions (name, description, model, tools)
- Validation rules
- Model selection guidelines
- Tool configuration patterns
- Examples from repository

**Use Cases**:
- Creating custom agents
- Understanding agent configuration
- Model tier selection
- Tool access restrictions

**Audience**: Agent creators, advanced users

---

### Workflow Specification

**[Workflow API Specification](./api/workflow-spec.md)**

Complete technical specification for workflow format:

- File format and structure
- Phase definition format
- Task tool invocation syntax
- Configuration options
- Context passing patterns
- Orchestration patterns
- Validation rules

**Use Cases**:
- Creating multi-agent workflows
- Understanding workflow structure
- Implementing orchestration patterns
- Context management

**Audience**: Workflow developers, orchestration architects

---

### Plugin Manifest Specification

**[Plugin Manifest API Specification](./api/plugin-manifest-spec.md)**

Complete specification for marketplace.json format:

- Marketplace manifest structure
- Plugin definition schema
- Field definitions and validation
- File path references
- Categories and keywords
- Installation flow

**Use Cases**:
- Creating plugin marketplaces
- Publishing plugins
- Understanding installation mechanics
- Plugin organisation

**Audience**: Plugin creators, marketplace maintainers

## Examples

### Agents

Browse 84 specialised agents across domains:

- **[agents/](../agents/)** - All agents directory
- **Architecture**: [backend-architect.md](../agents/backend-architect.md), [cloud-architect.md](../agents/cloud-architect.md)
- **Languages**: [python-pro.md](../agents/python-pro.md), [typescript-pro.md](../agents/typescript-pro.md)
- **Quality**: [code-reviewer.md](../agents/code-reviewer.md), [security-auditor.md](../agents/security-auditor.md)
- **Data/AI**: [data-scientist.md](../agents/data-scientist.md), [ml-engineer.md](../agents/ml-engineer.md)

### Workflows

Browse 15 multi-agent workflows:

- **[workflows/](../workflows/)** - All workflows directory
- **Feature Development**: [feature-development.md](../workflows/feature-development.md)
- **Full-Stack**: [full-stack-feature.md](../workflows/full-stack-feature.md)
- **TDD**: [tdd-cycle.md](../workflows/tdd-cycle.md)
- **Security**: [security-hardening.md](../workflows/security-hardening.md)
- **ML/AI**: [ml-pipeline.md](../workflows/ml-pipeline.md)

### Tools

Browse 42 development tools:

- **[tools/](../tools/)** - All tools directory
- **API**: [api-scaffold.md](../tools/api-scaffold.md)
- **Documentation**: [doc-generate.md](../tools/doc-generate.md)
- **Security**: [security-scan.md](../tools/security-scan.md)
- **Infrastructure**: [k8s-manifest.md](../tools/k8s-manifest.md)

## Official Claude Code Documentation

### Core Concepts

- **[Claude Code Overview](https://docs.claude.com/en/docs/claude-code/overview)** - Introduction to Claude Code
- **[Subagents](https://docs.claude.com/en/docs/claude-code/sub-agents)** - How subagents work
- **[Plugins](https://docs.claude.com/en/docs/claude-code/plugins)** - Plugin system overview
- **[Plugin Marketplaces](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)** - Creating marketplaces

### Reference

- **[Plugins Reference](https://docs.claude.com/en/docs/claude-code/plugins-reference)** - Complete plugin.json schema
- **[Subagents Reference](https://docs.claude.com/en/docs/claude-code/sub-agents)** - Agent capabilities and invocation

### Advanced Topics

- **[Model Selection](https://docs.claude.com/en/docs/claude-code/sub-agents#model-selection)** - Choosing the right model tier
- **[Tool Access](https://docs.claude.com/en/docs/claude-code/sub-agents#tool-access)** - Configuring tool restrictions
- **[Invocation Methods](https://docs.claude.com/en/docs/claude-code/sub-agents#invocation-methods)** - How agents are selected

## Contributing

### Contributing to Documentation

We welcome documentation improvements:

1. **Typos and Clarity**: Open PR with fixes
2. **New Examples**: Add examples to relevant sections
3. **Missing Content**: Open issue describing what's needed
4. **Mermaid Diagrams**: Follow existing diagram patterns

### Contributing Agents, Workflows, Tools

See **[CONTRIBUTING.md](../.github/CONTRIBUTING.md)** for:

- Agent creation guidelines
- Workflow development patterns
- Tool command format
- Submission process
- Quality standards

### Documentation Standards

**Writing Style**:
- Clear, concise, technical
- British/Commonwealth English spelling
- Active voice preferred
- Examples for all concepts

**File Naming**:
- Kebab-case (e.g., `agent-system.md`)
- Descriptive names
- No spaces or underscores

**Markdown Format**:
- Clear heading hierarchy
- Table of contents for long docs
- Mermaid diagrams for complex concepts
- Code examples with syntax highlighting

## Roadmap

### Planned Documentation

- **User Guides**: Tutorials for common workflows
- **Advanced Patterns**: Multi-worktree coordination, complex orchestrations
- **Case Studies**: Real-world usage examples
- **Video Walkthroughs**: Visual learning content

### Feedback

Have suggestions for documentation improvements?

- **GitHub Issues**: [github.com/wshobson/agents/issues](https://github.com/wshobson/agents/issues)
- **Discussions**: [github.com/wshobson/agents/discussions](https://github.com/wshobson/agents/discussions)

## Glossary

**Agent**: Specialised AI assistant with dedicated purpose, separate context, and optional tool restrictions

**Workflow**: Multi-phase orchestration coordinating multiple agents through structured phases

**Tool**: Focused utility or code generation command for specific tasks

**Plugin**: Collection of agents, workflows, and tools distributed as a unit

**Marketplace**: Catalog of plugins defined in marketplace.json

**Frontmatter**: YAML metadata at the top of agent files defining configuration

**Task Tool**: Claude Code mechanism for invoking subagents within workflows

**Model Tier**: Performance level (haiku/sonnet/opus) determining agent capabilities

**Context Isolation**: Separate context windows for each agent execution

**Deference Pattern**: Agents delegating specialised tasks to domain experts

## License

This documentation is part of the Claude Code Agents Marketplace repository.

**License**: MIT - See [LICENSE](../LICENSE) file for details

---

**Repository**: [github.com/wshobson/agents](https://github.com/wshobson/agents)

**Marketplace Addition**: `/plugin marketplace add https://github.com/wshobson/agents`

**Official Claude Code Docs**: [docs.claude.com/claude-code](https://docs.claude.com/en/docs/claude-code/overview)
