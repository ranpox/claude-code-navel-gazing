# Extracted Prompts & Tools Navigation

Complete collection of system prompts and tool descriptions extracted from Claude Code's prettified source.

## Structure

### 📁 prompts/
System prompts and behavioral instructions

- **[01-main-system-prompt.md](./prompts/01-main-system-prompt.md)** - Core system prompt with identity, tone, style guidelines
- **[02-task-management.md](./prompts/02-task-management.md)** - TodoWrite tool usage and task management examples
- **[03-output-modes.md](./prompts/03-output-modes.md)** - Insights Mode and Learn by Doing Mode
- **[04-git-workflows.md](./prompts/04-git-workflows.md)** - Git commit and pull request workflows

### 📁 tools/
Tool descriptions organized by category

- **[01-file-operations.md](./tools/01-file-operations.md)** - Read, Write, Edit, MultiEdit, NotebookEdit
- **[02-search-tools.md](./tools/02-search-tools.md)** - Glob, Grep, LS
- **[03-execution-tools.md](./tools/03-execution-tools.md)** - Bash tool and system commands
- **[04-web-tools.md](./tools/04-web-tools.md)** - WebFetch, WebSearch
- **[05-agent-tool.md](./tools/05-agent-tool.md)** - Task/Agent tool for subagent delegation
- **[06-planning-tools.md](./tools/06-planning-tools.md)** - TodoWrite, ExitPlanMode
- **[07-mcp-tools.md](./tools/07-mcp-tools.md)** - MCP resource and IDE tools

## Quick Navigation

### By Category
- **Behavioral Instructions** → [prompts/](./prompts/)
- **Tool Capabilities** → [tools/](./tools/)
- **Technical Analysis** → [README.md](./README.md)

### By Use Case
- **"How does Claude Code behave?"** → Start with [Main System Prompt](./prompts/01-main-system-prompt.md)
- **"What tools are available?"** → Browse [tools/](./tools/) directory
- **"How do I use it programmatically?"** → See SDK examples in [README.md](./README.md#quick-start-guide)
- **"How does it work internally?"** → Read [Architecture Overview](./01-architecture-overview.md)

## Key Findings

### System Prompt Highlights
- Strong emphasis on conciseness and directness
- CLI-optimized output (markdown in monospace)
- Security-first approach (defensive tasks only)
- Task management via TodoWrite tool is critical
- Git workflows fully automated with specific formatting

### Tool Categories
- **21 total tools** identified (not 22 as initially thought)
- File operations heavily restricted (prefer edit over write)
- Search optimized with ripgrep integration
- MCP extensibility for IDE and custom integrations
- Agent tool enables complex task delegation

### Behavioral Guidelines
- No emojis unless requested
- No unnecessary explanations
- Direct, concise responses preferred
- Proactive task management required
- Convention following is paramount

## Extraction Methodology

All prompts and tool descriptions were extracted from:
- **Source**: `cli-prettified.js` (12MB prettified version of cli.js)
- **Method**: Pattern matching for long template literals and description fields
- **Validation**: Cross-referenced with actual tool invocations

## Organization Principles

- **prompts/** - Behavioral instructions that shape Claude Code's personality and approach
- **tools/** - Functional capabilities grouped by category
- **Separation of Concerns**: Prompts (how to behave) vs Tools (what can be done)
- **Practical Focus**: Real examples from the source, not theoretical documentation