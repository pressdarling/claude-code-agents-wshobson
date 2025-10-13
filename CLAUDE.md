# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **Claude Code marketplace repository** providing 84 specialized AI agents, 15 multi-agent workflow orchestrators, and 42 development tools. The repository structure organises these into three main directories:

- **agents/** - Individual specialist agents (84 files)
- **workflows/** - Multi-agent orchestration workflows (15 files)
- **tools/** - Focused development utilities (42 files)

## File Format and Structure

All agents, workflows, and tools are defined as **Markdown files with YAML frontmatter**. The frontmatter structure is:

```markdown
---
name: subagent-name
description: Activation criteria for this subagent
model: haiku|sonnet|opus  # Optional: Model selection
tools: tool1, tool2       # Optional: Tool restrictions
---

System prompt defining the subagent's expertise and behaviour
```

### Model Selection Guidelines

- **haiku**: Simple, deterministic tasks with minimal reasoning (11 agents)
- **sonnet**: Standard development and engineering tasks (50 agents)
- **opus**: Complex analysis, architecture, and critical operations (23 agents)

## Key Architecture Patterns

### 1. Agent Frontmatter Requirements

When creating or modifying agent files:

- **name**: Lowercase, hyphen-separated (e.g., `backend-architect`, `frontend-developer`)
- **description**: Clear activation criteria that Claude Code uses to invoke the agent
- **model**: Optional but recommended for performance tuning
- **tools**: Optional restrictions on which tools the agent can use

### 2. Multi-Agent Workflow Structure

Workflows in `workflows/` follow a consistent pattern:

1. **Configuration Options** - Define parameters for methodology, complexity, deployment strategy
2. **Phase-Based Execution** - Break complex tasks into logical phases
3. **Agent Coordination** - Use Task tool with specific subagent_type for each phase
4. **Context Passing** - Each phase passes outputs to subsequent phases
5. **Success Criteria** - Define completion requirements

Example workflow pattern:
```markdown
## Phase 1: Discovery & Requirements
1. Use Task tool with subagent_type="business-analyst"
2. Prompt: "Analyse requirements: $ARGUMENTS..."
3. Expected output: Requirements document
4. Context: Initial feature request

## Phase 2: Implementation
1. Use Task tool with subagent_type="backend-architect"
2. Prompt: "Implement services for: $ARGUMENTS. Follow design: [from Phase 1]..."
```

### 3. Tool Command Format

Tools in `tools/` provide focused utilities with:

- **Context Section** - Explains what the user needs
- **Requirements** - References `$ARGUMENTS` variable
- **Instructions** - Step-by-step guidance with code examples
- **Reference Examples** - Complete implementation patterns

## Common Development Tasks

### Installing as a Marketplace

```bash
/plugin marketplace add https://github.com/wshobson/agents
/plugin install <plugin-name>
```

### Testing Plugin Installations

After adding/modifying agents, test that they can be invoked:

```bash
# Natural language invocation
"Use backend-architect to design an API"
"Have security-auditor scan for vulnerabilities"
```

### Creating New Agents

1. Create a new `.md` file in `agents/`
2. Add frontmatter with name, description, and model
3. Write comprehensive system prompt covering:
   - Core capabilities
   - Behavioural traits
   - Workflow position (dependencies on other agents)
   - Response approach
   - Example interactions
4. Test natural language activation

### Creating New Workflows

1. Create workflow file in `workflows/`
2. Define configuration options
3. Break down into logical phases
4. Specify which agents to invoke for each phase
5. Define context passing between phases
6. Set success criteria and rollback strategy

### Creating New Tools

1. Create tool file in `tools/`
2. Structure with Context, Requirements, Instructions
3. Include reference examples with complete code
4. Add validation checklist
5. Document integration with other commands

## Agent Categories and Specialisations

### Architecture & Design
- backend-architect (opus) - API design, microservices, distributed systems
- cloud-architect (opus) - AWS/Azure/GCP infrastructure
- database-architect (opus) - Schema design from scratch

### Programming Languages
18 language-specific agents (all sonnet):
- javascript-pro, typescript-pro, python-pro, java-pro, etc.
- Specialised in modern patterns, frameworks, and optimisation

### Quality & Security
- code-reviewer (opus) - Production reliability focus
- security-auditor (opus) - OWASP compliance
- test-automator (sonnet) - Comprehensive test suites

### Data & AI
- data-scientist (opus) - SQL, BigQuery, data analysis
- ml-engineer (opus) - ML pipelines, model serving
- ai-engineer (opus) - LLM applications, RAG systems

### SEO & Content
11 SEO specialists (mostly haiku):
- seo-content-auditor, seo-meta-optimiser, seo-keyword-strategist, etc.

## Important Conventions

### Naming Conventions
- Files: `lowercase-with-hyphens.md`
- Agent names: Match filename (e.g., `backend-architect`)
- Avoid uppercase, underscores, or special characters

### Documentation Standards
- Follow Diataxis framework where applicable (Tutorial, Guide, Reference, Understanding)
- Use British/Commonwealth English spellings
- Zero emojis in any project documentation or code comments
- Always use kebab-case for documentation files

### Agent Design Principles

1. **Clear Boundaries** - Each agent has well-defined expertise area
2. **Proactive Invocation** - Description should enable automatic selection
3. **Workflow Awareness** - Agents know their position relative to others
4. **Deference Pattern** - Agents defer to specialists (e.g., backend-architect defers database design to database-architect)
5. **Context Preservation** - Agents receive and pass context through workflow phases

### Model Distribution Strategy

The 84 agents are distributed across models based on task complexity:

| Model | Count | Primary Use Cases |
|-------|-------|-------------------|
| Haiku | 11 | Search, SEO, quick reference, content tasks |
| Sonnet | 50 | Standard development, language expertise, implementation |
| Opus | 23 | Architecture, security, complex analysis, ML/AI |

## Repository Maintenance

### Before Committing Changes

1. Validate frontmatter structure
2. Ensure descriptions enable agent discovery
3. Test agent invocation with natural language
4. Verify model selection is appropriate
5. Check for naming convention compliance

### Common Issues

- **Agent not discovered**: Description too vague or missing key terms
- **Wrong model tier**: Task complexity doesn't match model capabilities
- **Name conflicts**: Multiple agents with similar names/descriptions
- **Missing context**: Workflow phases don't pass outputs properly

## Contributing

See `.github/CONTRIBUTING.md` for detailed contribution guidelines:

- Always create an issue before significant changes
- Follow existing code style and formatting
- Use appropriate issue templates
- Maintain professional, respectful communication
- Focus on specialized domain expertise

## Integration with Claude Code

These agents integrate with Claude Code's Task tool:

```javascript
// Example invocation
Task({
  subagent_type: "backend-architect",
  description: "Design authentication API",
  prompt: "Create RESTful API for user authentication with JWT..."
})
```

Claude Code automatically:
1. Selects appropriate agent based on user request
2. Loads agent definition with frontmatter and prompt
3. Executes with specified model tier
4. Coordinates multiple agents for complex workflows

## Documentation

Comprehensive technical documentation available in `docs/`:

### Quick Links

- **[Documentation Hub](docs/README.md)** - Central navigation for all documentation
- **[System Architecture](docs/architecture/system-overview.md)** - High-level architecture with Mermaid diagrams
- **[Agent System](docs/architecture/agent-system.md)** - Agent lifecycle, invocation, coordination
- **[Workflow Patterns](docs/architecture/workflow-patterns.md)** - Orchestration strategies

### API Specifications

- **[Agent Frontmatter Spec](docs/api/agent-frontmatter-spec.md)** - Complete agent definition format
- **[Workflow Spec](docs/api/workflow-spec.md)** - Complete workflow format and Task tool integration
- **[Plugin Manifest Spec](docs/api/plugin-manifest-spec.md)** - marketplace.json schema

### When to Reference

- **Creating agents**: See [agent-frontmatter-spec.md](docs/api/agent-frontmatter-spec.md)
- **Building workflows**: See [workflow-spec.md](docs/api/workflow-spec.md) and [workflow-patterns.md](docs/architecture/workflow-patterns.md)
- **Understanding architecture**: See [system-overview.md](docs/architecture/system-overview.md)
- **Publishing plugins**: See [plugin-manifest-spec.md](docs/api/plugin-manifest-spec.md)

## Key Files to Reference

- `README.md` - Comprehensive overview of all agents and workflows
- `docs/README.md` - Documentation navigation hub
- `.github/CONTRIBUTING.md` - Contribution guidelines and quality standards
- `agents/backend-architect.md` - Example of detailed agent specification
- `workflows/feature-development.md` - Example of multi-phase workflow
- `tools/api-scaffold.md` - Example of comprehensive tool with code samples
