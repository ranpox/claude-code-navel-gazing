# Claude Code Package Analysis Summary

**Documentation Structure**: 
- 📁 `claude-code-docs/` - Comprehensive documentation
  - [Architecture Overview](./claude-code-docs/01-architecture-overview.md)
  - [Message Flow & IPC](./claude-code-docs/02-message-flow-ipc.md)
  - [Tool System](./claude-code-docs/03-tool-system.md)
  - [Prompt Engineering](./claude-code-docs/04-prompt-engineering.md)
  - [Actual Prompts Extracted](./claude-code-docs/05-actual-prompts-extracted.md)
- 📄 `claude-code-comprehensive-exploration-plan.md` - Master research roadmap (merged plan)
- 📄 `cli-prettified.js` - Prettified CLI source (12MB)

## Quick Reference

### Package Info
- **Name**: @anthropic-ai/claude-code
- **Version**: 1.0.68
- **Type**: ES Module, Node.js 18+
- **License**: Commercial

### Core Architecture
- **Process Model**: SDK spawns CLI subprocess with JSON IPC
- **Communication**: Line-delimited JSON over stdin/stdout
- **Tools**: 21+ built-in tools with permission system
- **Distribution**: Single minified cli.js (8.7MB) with all dependencies

### Key Features
- **Subagent Delegation**: Via Agent tool for complex tasks
- **Stream-Based SDK**: AsyncIterator pattern
- **Session Persistence**: --resume/--continue flags
- **Interrupt Support**: Real-time cancellation
- **MCP Extensibility**: Plugin protocol support

### SDK Usage
```javascript
import { query } from '@anthropic-ai/claude-code'

const response = query({
  prompt: "Help me write a function",
  options: {
    allowedTools: ['Read', 'Write'],
    permissionMode: 'default',
    canUseTool: (tool, input) => true
  }
})

for await (const message of response) {
  console.log(message)
}
```

## Agentic Flow Summary

### User Query Processing
1. **Reception**: CLI receives input via stdin or SDK
2. **Message Pipeline**: SDK → CLI → Claude API → Response
3. **Context Building**: CLAUDE.md + working directory + tool results
4. **Tool Execution**: Permission check → Execute → Stream results

### Subagent Pattern
```typescript
interface AgentInput {
  description: string;    // Task summary
  prompt: string;        // Detailed task
  subagent_type: string; // Specialized type
}
```

### Permission Modes
- `default` - User approval required
- `acceptEdits` - Auto-approve file edits
- `bypassPermissions` - Skip all checks
- `plan` - Planning mode

## Key Findings

### Architecture Insights
- Process isolation for security and stability
- Webpack bundle includes everything (even Sentry tracking)
- Yoga WebAssembly for terminal UI
- Ripgrep binaries bundled for all platforms

### Tool Categories
- **File Ops**: Read, Write, Edit, MultiEdit, Glob, Grep, LS
- **Dev Tools**: Bash, NotebookEdit, TodoWrite
- **Web Tools**: WebFetch, WebSearch
- **MCP Tools**: ListMcpResources, ReadMcpResource
- **Agent Tool**: Subagent delegation

### From Prettified Analysis
```javascript
// Tool categorization in source
const toolCategories = {
  filePatternTools: ["Read", "Write", "Edit", "Glob", "MultiEdit", "NotebookRead", "NotebookEdit"],
  bashPrefixTools: ["Bash"],
  customValidation: { WebSearch: {...}, WebFetch: {...} }
}
```

## Recent Progress
- ✅ **MAJOR MILESTONE**: Extracted COMPLETE main system prompt from prettified CLI
- ✅ Completed all 7 research phases (Architecture, Message Flow, Tools, Context, Subagents, State, Prompts)
- ✅ Extracted prompts/descriptions for 18/21 tools (only 3 MCP tools remaining)
- ✅ Created 5 comprehensive documentation files
- ✅ Found git workflows, output modes, task management instructions

## Key Discoveries
- **Architecture**: Process isolation with JSON IPC, AsyncIterator SDK pattern
- **Tools**: 21+ tools with sophisticated permission system
- **System Prompt**: ✅ COMPLETE main prompt extracted with all behavioral guidelines
- **Context**: Dynamic construction with CLAUDE.md integration via system-reminder tags
- **Subagents**: Agent tool enables task delegation with detailed examples
- **Git Integration**: Complete workflows for commits and pull requests
- **Output Modes**: Insights Mode and Learn by Doing Mode for educational interactions

## Tool Prompts Status
- **Complete (8)**: Read, Write, Edit, Bash, Glob, Grep, LS, TodoWrite
- **With Full Descriptions (10)**: Above 8 + MultiEdit, NotebookEdit, ExitPlanMode, WebFetch, WebSearch, Agent/Task
- **Missing (3)**: MCP tools only (ListMcpResources, ReadMcpResource, McpInput)

## Exploration Methodology & Memory System

### Memory Hierarchy
- **CLAUDE.md (This File)**: High-level memory for skills, patterns, and meta-knowledge
- **Project Docs**: Detailed memory for comprehensive findings and analysis

### Core Exploration Loop
1. **Search** - Cast wide net with multiple strategies
2. **Read** - Deep dive with full context
3. **Reflect** - Connect to existing knowledge
4. **Update Memory** - CLAUDE.md (skills) + Docs (details)
5. **Capture Meta-Knowledge** - Learn how to learn better
6. **Update Plan** - Update claude-code-comprehensive-exploration-plan.md progress

### Search Patterns That Work
```javascript
// Tool descriptions in prettified CLI
"toolName.*description|Usage:|Use this tool"

// Long template literals (likely prompts)
\`[^`]{200,}

// Function definitions with specific names
"function\\s+\\w*tool\\w*|function\\s+\\w*prompt\\w*"

// Base64 encoded strings
"[A-Za-z0-9+/]{40,}={0,2}"

// Tool-specific patterns
"description".*"[^"]{100,}"  // Long description strings
```

### Exploration Anti-Patterns
- Don't search for "prompt" alone - too many false positives
- Avoid reading minified code directly - prettify first
- Don't use overly broad patterns like "system|instruction"

### Exploration Skills Learned
- For obfuscated code: Check base64, hex, escape sequences
- For nested structures: Read entire sections, not just matches
- For tool prompts: Look for long strings near tool definitions
- Failed searches are valuable - they narrow the search space

## Next Steps
See [comprehensive exploration plan](./claude-code-comprehensive-exploration-plan.md) for:
1. Extract remaining 3 MCP tool prompts (low priority - rarely used)
2. Investigate runtime behavior and dynamic prompt construction
3. Analyze context window management strategies
4. Document advanced features (planning mode, reasoning traces)
5. Create remaining documentation files (6-10) for unexplored areas

