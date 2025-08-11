# Claude Code Package Analysis Summary

**Documentation Structure**: 
- 📁 `claude-code-docs/` - Comprehensive documentation
  - **[README.md](./claude-code-docs/README.md)** - Main documentation hub (technical analysis + quick start)
  - **[index.md](./claude-code-docs/index.md)** - Navigation for extracted prompts/tools
  - `prompts/` - System prompts (main, task management, output modes, git workflows)
  - `tools/` - Tool descriptions by category (7 files covering all 21 tools)
  - Technical docs: 01-architecture, 02-message-flow, 03-tool-system, 04-prompt-engineering
- 📄 `exploration-plan.md` - Master research roadmap (merged plan)
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
- ✅ Extracted prompts/descriptions for ALL 21 tools (MCP investigation complete)
- ✅ Created comprehensive documentation structure (README + index + 13 organized files)
- ✅ Reorganized docs: README.md as main hub, index.md for prompt/tool navigation
- ✅ Found git workflows, output modes, task management instructions
- ✅ **NEW**: Runtime behavior analysis complete (session persistence, planning mode, interrupt handling)
- ✅ **NEW**: Model selection and fallback system documented (Claude 3.5, 3.7, 4.0 variants)

## Key Discoveries
- **Architecture**: Process isolation with JSON IPC, AsyncIterator SDK pattern
- **Tools**: 21+ tools with sophisticated permission system
- **System Prompt**: ✅ COMPLETE main prompt extracted with all behavioral guidelines
- **Context**: Dynamic construction with CLAUDE.md integration via system-reminder tags
- **Subagents**: General-purpose agent confirmed, examples for code-reviewer, greeting-responder
- **Git Integration**: Complete workflows for commits and pull requests
- **Output Modes**: Insights Mode and Learn by Doing Mode for educational interactions
- **Model System**: Claude 3.5 (Sonnet/Haiku), 3.7, 4.0 (Sonnet/Opus) with automatic fallback
- **Planning Mode**: ExitPlanMode tool for transitioning from planning to execution
- **Session Persistence**: --resume/--continue flags for conversation continuity
- **Error Recovery**: Basic retry with backoff, rate limiting, no self-correction
- **Code Analysis**: Text-based only (grep/glob), no AST or semantic understanding

## Tool Prompts Status  
- **Complete (18/21)**: Read, Write, Edit, Bash, Glob, Grep, LS, TodoWrite, MultiEdit, NotebookEdit, ExitPlanMode, WebFetch, WebSearch, Agent/Task, ListMcpResources, ReadMcpResource, mcp__ide__executeCode, mcp__ide__getDiagnostics
- **Generic Handler (1)**: MCP generic handler with empty prompts
- **Non-existent**: McpInput tool does not exist (only 21 tools total, not 22)

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
6. **Update Plan** - Update exploration-plan.md progress

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

## Documentation Organization

### Current Structure
- **README.md**: Main entry point with technical analysis, key discoveries, quick start
- **index.md**: Focused navigation for extracted prompts and tools
- **prompts/**: Behavioral instructions extracted from source (4 files)
- **tools/**: Tool descriptions organized by category (7 files)
- **Technical Docs**: Architecture and implementation analysis (7 files)
  - 01-architecture-overview.md
  - 02-message-flow-ipc.md
  - 03-tool-system.md
  - 04-prompt-engineering.md
  - 05-runtime-behavior-analysis.md (NEW)
  - 06-model-selection-and-advanced-features.md (NEW)

### Next Steps
See [comprehensive exploration plan](./exploration-plan) for:
1. ✅ COMPLETE: MCP tool prompts extracted (only 2 resource tools + 2 IDE tools exist)
2. ✅ COMPLETE: Documentation reorganized for clarity (README vs index separation)
3. ✅ COMPLETE: Runtime behavior and dynamic prompt construction investigated
4. ⚠️ PARTIAL: Context window management (found truncation, no token counting)
5. ⚠️ PARTIAL: Advanced features documented (planning mode found, no reasoning traces)
6. ❌ NOT FOUND: AST-based code analysis, dependency tracking, semantic understanding
7. ⚠️ LIMITED: Performance optimization, caching strategies, parallel execution
8. ⚠️ BASIC: Security implementation, no sandboxing, direct command execution

