# Runtime Behavior Analysis

## Dynamic Prompt Construction

### getPrompt Pattern
Found multiple instances of runtime prompt construction:
- `getPrompt()` method at line 321730
- `getPromptForCommand()` pattern used throughout for slash commands
- Dynamic assembly of prompts based on command context

### Context Building
- `createContext()` methods found (React contexts and internal contexts)
- Multiple context keys for OpenTelemetry tracing
- Runtime context assembly for different components

## Interrupt and Cancellation Support

### AbortController Implementation
- Uses native `AbortController` for request cancellation (line 29100)
- Timeout-based abortion with configurable timeouts
- Signal propagation through request pipeline
- Found at lines: 29100, 79098, 153997, 156826

### Signal Handling
- AbortSignal validation and checking
- Graceful cancellation of in-flight requests
- Timeout management with automatic abort

## Session Management

### Session Persistence Flags
- `--resume` and `--continue` flags documented (line 340944)
- Session ID validation with UUID format requirement
- Resume functionality for conversation continuity
- Session state stored somewhere (not found in prettified source)

### Command Line Options (line 370535)
```
-c, --continue: Continue the most recent conversation
-r, --resume [sessionId]: Resume a conversation with optional session ID
--session-id <uuid>: Use specific session ID for conversation
```

## Planning Mode

### ExitPlanMode Tool (line 329630)
- Special tool for transitioning out of planning mode
- Requires user confirmation to exit
- Only used for implementation planning, not research
- Clear distinction between planning and execution phases

### Planning Mode Behavior
- Tool name: `ExitPlanMode`
- Description: "Prompts the user to exit plan mode and start coding"
- Permission check: Always asks user "Exit plan mode?"
- Includes detailed prompt about when to use/not use

## Subagent Architecture

### Discovered Agent Types

#### 1. General-Purpose Agent (line 333282)
- **Type**: `general-purpose`
- **When to use**: "General-purpose agent for researching complex questions, searching for code, and executing multi-step tasks"
- **Tools**: All tools (`["*"]`)
- **System Prompt**: Focused on completing tasks with detailed writeup

#### 2. Theoretical Agent Types (Examples Only)
Found in example descriptions but not implemented:
- `code-reviewer`: Review significant code changes
- `greeting-responder`: Respond to greetings with jokes
- `statusline-setup`: Configure statusLine settings (mentioned in docs)

### Agent Selection
- `subagent_type` parameter required for Task tool
- Color coding for different agent types (red, blue, green, etc.)
- Dynamic agent registration system suggested

## Context Window Management

### Token/Size Management
- `truncate()` function for text truncation (line 698)
- Line snipping and truncation utilities
- Object key truncation for display (line 1128)
- No explicit token counting found in prettified source

## Key Runtime Patterns

### 1. Dynamic Tool Loading
- Tools registered at runtime
- Permission checks before execution
- Tool descriptions loaded dynamically

### 2. Message Pipeline
- Command processing with `getPromptForCommand()`
- Progressive message assembly
- Context injection at multiple points

### 3. State Management
- React contexts for UI state
- OpenTelemetry contexts for tracing
- Session state (location not found)

### 4. Error Recovery
- Timeout handling with AbortController
- Rate limiting detection
- Graceful degradation

## Unresolved Questions

1. **Session Storage**: Where is conversation state persisted?
2. **Token Counting**: How are context window limits enforced?
3. **Agent Registry**: Where are additional agent types defined?
4. **Model Selection**: How is model switching implemented?
5. **Context Priority**: How are files prioritized for inclusion?

## Notable Implementation Details

### Permission Modes
Found reference to `AU1` variable containing permission modes (line 370535)

### MCP Integration
- `--mcp-config` flag for loading MCP servers
- `--permission-prompt-tool` for MCP permission handling
- `--strict-mcp-config` to limit MCP sources

### Debug Options
- `--debug`: Enable debug mode
- `--debug-to-stderr`: Debug output to stderr
- `--verbose`: Override verbose setting
- `--mcp-debug`: Deprecated MCP debug flag

## Next Investigation Areas

1. Search for session storage implementation
2. Find token counting and context window enforcement
3. Locate additional agent type definitions
4. Investigate model switching logic
5. Understand file prioritization algorithms