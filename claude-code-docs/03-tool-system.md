# Tool System

## Overview

Claude Code implements a comprehensive tool system with 21+ built-in tools for various operations.

## Tool Categories

### 1. File Operations
- **Read**: Read file contents with optional offset/limit
- **Write**: Create or overwrite files
- **Edit**: Find and replace in files
- **MultiEdit**: Multiple edits in a single operation
- **Glob**: Pattern-based file search
- **Grep**: Content search using ripgrep
- **LS**: List directory contents

### 2. Development Tools
- **Bash**: Execute shell commands
- **NotebookEdit**: Modify Jupyter notebooks
- **TodoWrite**: Task management
- **ExitPlanMode**: Exit planning mode

### 3. Web Tools
- **WebFetch**: Fetch and analyze web content
- **WebSearch**: Search the web

### 4. Subagent Tool
- **Agent**: Delegate tasks to specialized subagents

### 5. MCP Tools
- **ListMcpResources**: List MCP resources
- **ReadMcpResource**: Read MCP resource
- **McpInput**: Generic MCP input

## Tool Implementation Pattern

From the prettified source analysis:

```javascript
const toolCategories = {
  filePatternTools: [
    "Read", "Write", "Edit", "Glob", 
    "MultiEdit", "NotebookRead", "NotebookEdit"
  ],
  bashPrefixTools: ["Bash"],
  customValidation: {
    WebSearch: (input) => { /* validation logic */ },
    WebFetch: (input) => { /* validation logic */ }
  }
};
```

## Permission System

### Permission Modes
1. **default**: User approval required for each tool
2. **acceptEdits**: Auto-approve file editing tools
3. **bypassPermissions**: Skip all permission checks
4. **plan**: Planning mode for task decomposition

### Permission Flow
```
Tool Request
    ↓
Permission Check
    ↓
┌─────────────────┐
│ Permission Mode │
└─────────────────┘
    ↓         ↓
 default   bypass
    ↓         ↓
User Prompt  Execute
    ↓         ↓
 Decision     ↓
    ↓         ↓
 Execute   Execute
```

### Tool Filtering
```javascript
// SDK options
{
  allowedTools: ['Read', 'Write'],      // Whitelist
  disallowedTools: ['Bash'],            // Blacklist
  canUseTool: (toolName, input) => {   // Custom callback
    return true; // or false
  }
}
```

## Tool Schemas (from sdk-tools.d.ts)

### File Tools

#### Read Tool
```typescript
interface FileReadInput {
  file_path: string;    // Absolute path
  offset?: number;      // Starting line
  limit?: number;       // Number of lines
}
```

#### Edit Tool
```typescript
interface FileEditInput {
  file_path: string;
  old_string: string;
  new_string: string;
  replace_all?: boolean;
}
```

#### MultiEdit Tool
```typescript
interface FileMultiEditInput {
  file_path: string;
  edits: Array<{
    old_string: string;
    new_string: string;
    replace_all?: boolean;
  }>;
}
```

### Execution Tools

#### Bash Tool
```typescript
interface BashInput {
  command: string;
  timeout?: number;      // Max 600000ms (10 min)
  description?: string;  // 5-10 word description
  sandbox?: boolean;     // Sandboxed execution
}
```

### Web Tools

#### WebFetch Tool
```typescript
interface WebFetchInput {
  url: string;
  prompt: string;  // AI analysis prompt
}
```

#### WebSearch Tool
```typescript
interface WebSearchInput {
  query: string;
  allowed_domains?: string[];
  blocked_domains?: string[];
}
```

### Agent Tool

```typescript
interface AgentInput {
  description: string;    // 3-5 word summary
  prompt: string;        // Detailed task
  subagent_type: string; // Specialized type
}
```

## Tool Execution Patterns

### 1. Validation Phase
- Schema validation
- Path normalization
- Permission check
- Resource availability

### 2. Execution Phase
- Tool-specific logic
- Error handling
- Progress streaming
- Result formatting

### 3. Result Phase
- Standardized response
- Error propagation
- Context integration
- Stream closure

## Sandboxing

### Bash Sandbox Mode
When `sandbox: true`:
- Read-only filesystem access
- No network access
- Limited system calls
- Timeout enforcement

### Benefits
- Safer exploration
- No user prompts needed
- Prevents accidental damage
- Faster iteration

## Best Practices

### 1. Tool Selection
- Use most specific tool available
- Prefer Glob/Grep over Bash find
- Use MultiEdit for batch changes
- Leverage sandbox mode

### 2. Permission Management
- Start with default mode
- Use acceptEdits for trusted operations
- Implement canUseTool for fine control
- Document permission requirements

### 3. Error Handling
- Always handle tool failures
- Provide fallback strategies
- Log tool errors for debugging
- Validate inputs before execution

## Performance Considerations

### Tool Initialization
- Lazy loaded on first use
- Cached for subsequent calls
- Minimal startup overhead

### Streaming Results
- Progressive output for long operations
- No full buffering
- Memory efficient
- Real-time feedback

### Parallel Execution
- Multiple tools can run concurrently
- Independent tool execution
- Shared process resources
- Careful state management