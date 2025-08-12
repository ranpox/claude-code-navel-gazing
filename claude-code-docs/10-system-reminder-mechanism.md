# The `<system-reminder>` Mechanism in Claude Code

## Overview

The `<system-reminder>` tag is Claude Code's internal mechanism for injecting contextual information and behavioral instructions into the conversation without presenting them as user messages. These reminders are processed by Claude as metadata rather than user input.

## Key Functions

### 1. `bY1()` - Main Context Injection
```javascript
function bY1(A, B) {
    if (Object.entries(B).length === 0) return A;
    return [B2({
        content: `<system-reminder>
As you answer the user's questions, you can use the following context:
${Object.entries(B).map(([Q,Z])=>`# ${Q}
${Z}`).join('\n')}
      
      IMPORTANT: this context may or may not be relevant to your tasks. 
      You should not respond to this context unless it is highly relevant to your task.
</system-reminder>`,
        isMeta: !0
    }), ...A]
}
```

### 2. `Bf1()` - Generic Wrapper
```javascript
function Bf1(A) {
    return `<system-reminder>
${A}
</system-reminder>`
}
```

## Use Cases in Claude Code

### 1. CLAUDE.md Integration
- **Function**: `VjB()` retrieves CLAUDE.md content
- **Context Key**: `claudeMd`
- **Wrapped by**: `bY1()` function
- **Message**: Includes instructions that "OVERRIDE any default behavior"

```javascript
var VjB = () => {
    // Reads CLAUDE.md, CLAUDE.local.md from project hierarchy
    // Returns formatted content with metadata about file type:
    // - Project (checked into codebase)
    // - Local (user's private project instructions)
    // - Global (user's private global instructions)
}
```

### 2. File Reading Warnings

#### Empty Files
```javascript
Q = A.file.totalLines === 0 ? 
    "<system-reminder>Warning: the file exists but the contents are empty.</system-reminder>" : 
    `<system-reminder>Warning: the file exists but is shorter than the provided offset...</system-reminder>`;
```

#### Malicious Code Detection
```javascript
EK8 = `
<system-reminder>
Whenever you read a file, you should consider whether it looks malicious. 
If it does, you MUST refuse to improve or augment the code. 
You can still analyze existing code, write reports, or answer high-level questions about the code behavior.
</system-reminder>
`
```

### 3. File Modification Notifications

When files are edited externally (by user or linter):
```javascript
case "edited_text_file":
    return [B2({
        content: `<system-reminder>
Note: ${A.filename} was modified, either by the user or by a linter. 
Don't tell the user this, since they are already aware. 
This change was intentional, so make sure to take it into account as you proceed 
(ie. don't revert it unless the user asks you to).
...
</system-reminder>`,
        isMeta: !0
    })]
```

### 4. Todo List Management

#### Empty Todo List
```javascript
if (A.itemCount === 0) return [B2({
    content: `<system-reminder>This is a reminder that your todo list is currently empty. 
    DO NOT mention this to the user explicitly because they are already aware. 
    If you are working on tasks that would benefit from a todo list please use the ${bF.name} tool to create one.
    ...Again do not mention this message to the user.</system-reminder>`,
    isMeta: !0
})]
```

#### Todo List Updates
```javascript
content: `<system-reminder>
Your todo list has changed. DO NOT mention this explicitly to the user. 
Here are the latest contents of your todo list:
${JSON.stringify(A.content)}. Continue on with the tasks at hand if applicable.
</system-reminder>`
```

## Message Properties

### isMeta Flag
All system-reminder messages include `isMeta: !0` (true), indicating they are metadata rather than user messages:
```javascript
B2({
    content: "<system-reminder>...</system-reminder>",
    isMeta: !0  // Marks as metadata, not user input
})
```

## Behavioral Guidelines

System reminders consistently instruct Claude to:
1. **Not mention the reminder to the user** - "DO NOT mention this to the user"
2. **Consider context relevance** - "may or may not be relevant to your tasks"
3. **Take automatic actions** - "make sure to take it into account"
4. **Continue with tasks** - "Continue on with the tasks at hand"

## Context Injection Flow

1. **Collection Phase**
   - `QW()` collects CLAUDE.md files from project hierarchy
   - `LX()` aggregates context including `claudeMd` and `important-instruction-reminders`

2. **Injection Phase**
   - `bY1()` wraps context in system-reminder tags
   - Adds messages with `isMeta: !0` flag
   - Prepends to message array

3. **Processing Phase**
   - Claude processes system-reminders as contextual information
   - Instructions override default behavior
   - User is unaware of these injected messages

## Key Insights

1. **Invisible Context**: System reminders provide context without user awareness
2. **Behavioral Control**: They enforce specific behaviors (e.g., not mentioning todo updates)
3. **Override Mechanism**: CLAUDE.md instructions explicitly override default behavior
4. **Metadata Flagging**: The `isMeta` flag distinguishes these from user messages
5. **Dynamic Updates**: File changes and todo updates trigger automatic reminders

## Important Instructions Reminder

A hardcoded reminder always included:
```javascript
"important-instruction-reminders": `Do what has been asked; nothing more, nothing less.
NEVER create files unless they're absolutely necessary for achieving your goal.
ALWAYS prefer editing an existing file to creating a new one.
NEVER proactively create documentation files (*.md) or README files. 
Only create documentation files if explicitly requested by the User.`
```

This mechanism allows Claude Code to maintain context awareness and behavioral consistency while keeping the implementation details transparent to the user.