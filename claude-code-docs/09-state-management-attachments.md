# State Management and Attachment System

## Overview

Claude Code uses an **attachment-based state management system** to maintain conversation context and provide visual feedback. This system bridges internal state tracking with the Claude API's message format through two key functions: `MlB` (UI rendering) and `OC8` (message transformation).

## Core Components

### Attachment Types

Attachments represent various state changes and contextual information:

| Type | Purpose | UI Display |
|------|---------|------------|
| `new_file` | File read operations | Shows filename and line/size count |
| `new_directory` | Directory listings | Shows directory path |
| `edited_text_file` | File modifications | Hidden (null display) |
| `edited_image_file` | Image modifications | Hidden (null display) |
| `selected_lines_in_ide` | IDE selections | Shows selection range and filename |
| `opened_file_in_ide` | IDE file opens | Hidden (null display) |
| `todo` | Todo list state | Shows item count (post-compact) |
| `todo_reminder` | Todo usage reminder | Hidden (null display) |
| `plan_mode` | Planning mode active | Hidden (null display) |
| `output_mode` | Output mode settings | Hidden (null display) |
| `diagnostics` | Code diagnostics | Shows issue count and files |
| `mcp_resource` | MCP resource reads | Shows resource name and server |
| `command_permissions` | Tool permissions | Shows allowed tools |
| `autocheckpointing` | Session checkpoints | Shows checkpoint status |
| `nested_memory` | CLAUDE.md content | Shows file path |
| `queued_command` | Pending commands | Shows command text |
| `ultramemory` | Extended memory | Hidden (null display) |
| `agent_mention` | Agent references | Hidden (null display) |

## Key Functions

### `MlB` Function (UI Rendering)
**Location**: cli-prettified.js:354065-354159

Renders attachment state in the terminal UI:

```javascript
function MlB({
    attachment: A,  // Attachment object with type field
    addMargin: B,   // Layout boolean
    verbose: Q      // Detail level boolean
}) {
    switch (A.type) {
        case "new_file":
            // Returns React element showing file info
            return lY.default.createElement(QH, {
                text: `Read ${A0.bold(filename)} (${lines} lines)`
            });
        case "todo":
            // Conditional rendering based on context
            if (A.context === "post-compact") 
                return createElement(QH, {text: `Todo list (${count} items)`});
            return null;
        // ... other cases
    }
}
```

**Key Characteristics**:
- React component for terminal UI
- Selective rendering (some attachments return null)
- Verbose mode affects detail level
- Uses bold formatting for emphasis

### `OC8` Function (Message Transformation)
**Location**: cli-prettified.js:330268-330427

Transforms attachments into Claude API messages:

```javascript
function OC8(A) {
    switch (A.type) {
        case "edited_text_file":
            return [B2({
                content: `<system-reminder>
Note: ${A.filename} was modified...
</system-reminder>`,
                isMeta: true
            })];
        case "todo":
            return [B2({
                content: `<system-reminder>
Your todo list has changed...
${JSON.stringify(A.content)}
</system-reminder>`,
                isMeta: true
            })];
        // ... other cases
    }
}
```

**Key Patterns**:
1. **System Reminders**: Wraps context in `<system-reminder>` tags
2. **Meta Messages**: Uses `isMeta: true` for system information
3. **Tool Integration**: Formats tool calls/results with `mG1()` and `uG1()`
4. **Conditional Logic**: Different handling based on attachment state

## State Flow Architecture

```
User Action / Tool Result
        ↓
Attachment Created
        ↓
    ┌───┴───┐
    ↓       ↓
  MlB     OC8
    ↓       ↓
Terminal  API Messages
Display   (with context)
```

### Message Construction Helpers

| Function | Purpose | Example Output |
|----------|---------|----------------|
| `B2()` | Creates user message objects | `{type: "user", message: {...}}` |
| `mG1()` | Formats tool input | `"Called the Read tool with input: {...}"` |
| `uG1()` | Formats tool results | `"Result of calling Read tool: {...}"` |
| `Bf1()` | Wraps in system-reminder | `"<system-reminder>...</system-reminder>"` |

## State Management Patterns

### 1. File Operation Tracking
When files are read or modified, attachments track:
- Filename and path
- Content type (text, image, notebook, PDF)
- Size/line count
- Truncation status
- Modification source (user vs linter)

### 2. Todo List Synchronization
Todo attachments maintain task state:
- Empty list triggers usage reminders
- Changes update Claude's context
- JSON serialization preserves structure
- Post-compact mode shows counts only

### 3. Mode State Enforcement
Special modes inject behavioral constraints:
- **Plan Mode**: Prevents execution, forces planning
- **Output Mode**: Activates specific response guidelines
- **Checkpointing**: Tracks session persistence

### 4. IDE Integration Context
IDE events become contextual hints:
- Selected lines may relate to current task
- Opened files provide potential context
- Diagnostics highlight new issues

## System Reminder Protocol

The `<system-reminder>` tag protocol enables:
1. **Invisible Context**: Information for Claude without user visibility
2. **State Synchronization**: Keeps Claude aware of system changes
3. **Behavioral Constraints**: Enforces mode-specific rules
4. **Meta-Information**: Provides operational guidance

Example system reminder:
```xml
<system-reminder>
Plan mode is active. You MUST NOT make any edits...
Instead, you should:
1. Answer the user's query comprehensively
2. Present your plan by calling the ExitPlanMode tool
</system-reminder>
```

## Implementation Details

### Attachment Creation Points
Attachments are created at various system points:
- Tool execution results
- File system operations
- IDE event handlers
- Mode transitions
- Session checkpoints

### Message Pipeline Integration
```javascript
// Attachment flows into message array
case "attachment": {
    let Z = OC8(Q.attachment),  // Transform to messages
        D = AY(B);               // Get last user message
    if (D?.type === "user") {
        // Merge with existing user message
        B[B.indexOf(D)] = Z.reduce((G, F) => qC8(G, F), D);
    }
}
```

### Rendering Decision Logic
The `MlB` function makes rendering decisions based on:
- Attachment type
- Context flags (`post-compact`, `file-watch`)
- Verbose mode settings
- Content availability

## Best Practices

1. **Attachment Design**: Keep attachments focused on single state aspects
2. **System Reminders**: Use clear, directive language in reminders
3. **Null Returns**: Hide technical details from user view when appropriate
4. **Meta Flags**: Mark system information with `isMeta: true`
5. **Context Preservation**: Maintain full state in attachment objects

## Related Systems

- **Tool System**: Tools generate attachments for results
- **Message Flow**: Attachments flow through IPC pipeline
- **UI Rendering**: Terminal display via React/Yoga
- **Session Management**: Checkpointing uses attachment state
- **Permission System**: Command permissions as attachments