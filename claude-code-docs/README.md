# Claude Code Documentation

Comprehensive analysis of Claude Code's architecture, implementation, and extracted prompts from reverse engineering the package.

## Documentation Structure

### Extracted Prompts & Tools
**[index.md](./index.md)** - Complete navigation guide for all extracted prompts and tool descriptions

#### System Prompts (`prompts/`)
- [Main System Prompt](./prompts/01-main-system-prompt.md) - Core identity and behavioral instructions
- [Task Management](./prompts/02-task-management.md) - TodoWrite tool usage examples
- [Output Modes](./prompts/03-output-modes.md) - Insights Mode and Learn by Doing Mode
- [Git Workflows](./prompts/04-git-workflows.md) - Automated git commit and PR workflows

#### Tool Descriptions (`tools/`)
- [File Operations](./tools/01-file-operations.md) - Read, Write, Edit, MultiEdit, NotebookEdit
- [Search Tools](./tools/02-search-tools.md) - Glob, Grep, LS
- [Execution Tools](./tools/03-execution-tools.md) - Bash tool with git integration
- [Web Tools](./tools/04-web-tools.md) - WebFetch, WebSearch
- [Agent Tool](./tools/05-agent-tool.md) - Subagent delegation system
- [Planning Tools](./tools/06-planning-tools.md) - TodoWrite, ExitPlanMode
- [MCP Tools](./tools/07-mcp-tools.md) - MCP resources and IDE integration

### Technical Analysis

1. **[Architecture Overview](./01-architecture-overview.md)**
   - Package structure and components
   - Process isolation model
   - SDK-CLI communication architecture

2. **[Message Flow & IPC](./02-message-flow-ipc.md)**
   - Line-delimited JSON protocol
   - Control messages and interrupts
   - AsyncIterator streaming pattern

3. **[Tool System](./03-tool-system.md)**
   - 21 tools implementation analysis
   - Permission system and modes
   - Tool execution pipeline

4. **[Prompt Engineering](./04-prompt-engineering.md)**
   - Context construction pipeline
   - CLAUDE.md integration
   - Dynamic prompt assembly

5. **[Runtime Behavior Analysis](./05-runtime-behavior-analysis.md)**
   - Session persistence and resume functionality
   - Planning mode and execution transitions
   - Interrupt handling and error recovery

6. **[Model Selection & Advanced Features](./06-model-selection-and-advanced-features.md)**
   - Claude 3.5, 3.7, 4.0 model variants
   - Automatic fallback mechanisms
   - Feature flags and capabilities

7. **[Performance, Limits & Security](./07-performance-limits-security.md)** 🆕
   - Output limits (30K chars Bash, 2K lines Read)
   - Caching strategies (WebFetch 15-min cache)
   - Security model (no sandboxing, direct execution)
   - Performance characteristics and bottlenecks

## Key Discoveries

### System Architecture
- **Package**: @anthropic-ai/claude-code v1.0.68 (ES Module, Node.js 18+)
- **Process Model**: SDK spawns CLI subprocess with JSON IPC
- **Distribution**: Single minified cli.js (8.7MB) with all dependencies bundled
- **Communication**: Line-delimited JSON over stdin/stdout

### Performance & Limits
- **Output Truncation**: 30,000 chars for Bash, 2,000 lines for Read
- **Caching**: Only WebFetch has 15-minute URL cache
- **Parallel Execution**: Batch tool calls supported, but sequential execution
- **No Token Counting**: Relies on Claude API for context management

### Security Model
- **No Sandboxing**: Commands execute directly with user privileges
- **Permission Modes**: default, acceptEdits, bypassPermissions, plan
- **Basic Validation**: Path normalization, minimal input sanitization
- **Trust-Based**: Designed for local development, not production

### Behavioral Guidelines Found
- Extreme conciseness ("answer in 1-3 sentences")
- CLI-optimized markdown output
- Security-first (defensive tasks only)
- Proactive task management via TodoWrite
- Convention following paramount
- No emojis unless requested

### Tool System (21 Tools Total)
- **File Ops**: Prefer Edit over Write, never create docs proactively
- **Search**: Ripgrep-powered Grep, pattern matching with Glob
- **Execution**: Bash with comprehensive git workflow support
- **Web**: Fetch and search with AI processing
- **Delegation**: Agent tool for complex multi-step tasks
- **Planning**: TodoWrite for task tracking, ExitPlanMode for planning mode
- **Extensibility**: MCP protocol for custom integrations

## Quick Start Guide

### Understanding Claude Code's Behavior
1. Read [Main System Prompt](./prompts/01-main-system-prompt.md) for core instructions
2. Review [Task Management](./prompts/02-task-management.md) for TodoWrite patterns
3. Explore [Tool Descriptions](./index.md) for capability details

### Using Claude Code Programmatically
```javascript
import { query } from '@anthropic-ai/claude-code'

const response = query({
  prompt: "Help me implement a feature",
  options: {
    allowedTools: ['Read', 'Write', 'Edit'],
    permissionMode: 'default',  // or 'acceptEdits', 'bypassPermissions'
    canUseTool: (tool, input) => true
  }
})

for await (const message of response) {
  console.log(message)
}
```

## Related Files

- **[../CLAUDE.md](../CLAUDE.md)** - Project memory and exploration methodology
- **[../exploration-plan.md](../exploration-plan.md)** - Master research roadmap (merged from multiple plans)
- **[../cli-prettified.js](../cli-prettified.js)** - Prettified CLI source (12MB) used for extraction