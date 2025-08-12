# Context Compaction Strategies in Claude Code

## Overview
Claude Code implements several sophisticated strategies to manage context size and ensure conversations can continue even as they grow large. These strategies include automatic truncation, manual compaction, and intelligent context management.

## Key Findings

### 1. Output Truncation Limits

#### Bash Command Output
- **Maximum output**: 30,000 characters (default)
- Can be configured via `BASH_MAX_OUTPUT_LENGTH` environment variable
- Located in `PG1()` function which reads from `GK8 = 30000`
- Truncation message: "If the output exceeds 30000 characters, output will be truncated before being returned to you"

#### File Reading (Read Tool)
- **Default limit**: 2000 lines (referenced in prompts but not found in code)
- Line truncation: Lines longer than 2000 characters are truncated
- Special handling for empty files: Returns system-reminder warning

#### String Truncation Utility
- Function `K39(A, B)`: Generic string truncation
- Adds "..." suffix when truncating
- Used throughout the codebase for display purposes

### 2. Manual Compaction (`/compact` Command)

#### Command Details
```javascript
var Jz8 = {
    type: "local",
    name: "compact",
    description: "Clear conversation history but keep a summary in context",
    argumentHint: "<optional custom summarization instructions>"
}
```

#### Compaction Process
1. **Trigger**: User runs `/compact` command or auto-compact triggers
2. **Summary Generation**: Creates detailed summary via API call
3. **Custom Instructions**: User can provide summarization guidelines
4. **Pre-compact Hooks**: Runs before compaction starts
5. **Result**: Replaces conversation history with summary

#### Summary Template Structure
The compaction uses a sophisticated prompt template that includes:
- Project context and setup
- Key files identified
- Technical decisions made
- Code patterns discovered
- Important findings
- All user messages (non-tool use)
- Pending tasks
- Current work description
- Optional next steps

### 3. Auto-Compact Feature

#### Configuration
- Setting: `autoCompactEnabled` (default: true)
- Stored in user settings
- Can be toggled via settings UI

#### Trigger Conditions
- Function `JF1()` checks if auto-compact is enabled
- `Az8(A)` determines if threshold is reached
- `isAboveAutoCompactThreshold` flag triggers compaction

#### Context Usage Monitoring
```javascript
function Hd(A) {
    // Calculates:
    // - percentLeft: Percentage of context remaining
    // - isAboveWarningThreshold: Warning level reached
    // - isAboveErrorThreshold: Critical level reached
    // - isAboveAutoCompactThreshold: Auto-compact should trigger
}
```

### 4. Context Window Management

#### System Reminders
- Special `<system-reminder>` tags for important notices
- Used for warnings about empty files, context limits, etc.
- Not counted as part of user input

#### Message Building
- Messages are constructed with various components
- Tool outputs can be truncated before inclusion
- File reads include line numbers in specific format

### 5. Truncation Strategies

#### Path Truncation
```javascript
function Wu0(A) {
    let B = A.length > 1024 ? `<truncated>${A.slice(-1024)}` : A
}
```
- Paths longer than 1024 characters are truncated
- Keeps last 1024 characters for relevance

#### Line Snipping
Function `C39` implements intelligent line snipping:
- Keeps context around important content
- Maximum 150 characters shown
- Adds `{snip}` markers for removed content

### 6. Token Counting and Analysis

#### Context Analysis Metrics
The system tracks detailed token usage:
- Human messages
- Assistant messages
- Tool requests/results
- Duplicate file reads
- Attachments
- Local command outputs

#### Duplicate Detection
- Tracks duplicate file reads to optimize context
- Calculates percentage of context used by duplicates
- Used for compaction decisions

### 7. Memory and Cache Management

#### Caching Systems
- 15-minute cache for WebFetch results
- Clear caches on compaction: `LX.cache.clear?.(), QW.cache.clear?.()` 

#### Session Persistence
- Conversation history stored for `--resume` functionality
- Checkpoint system for recovery

## Implementation Details

### Compaction Thresholds
While exact percentages aren't visible in prettified code, the system uses:
- Warning threshold (yellow indicator)
- Error threshold (red indicator)  
- Auto-compact threshold (triggers automatic compaction)

### User Notifications
- "Context left until auto-compact: X%"
- "Context low (X% remaining) · Run /compact to compact & continue"
- Visual indicators change color based on severity

### Error Handling
- "Not enough messages to compact" (minimum threshold)
- "Conversation too long" (maximum threshold exceeded)
- "API Error: Request was aborted" (network issues)

## Best Practices

### For Users
1. Monitor context usage indicator in UI
2. Use `/compact` proactively before hitting limits
3. Provide custom summarization instructions for better results
4. Enable auto-compact for long sessions

### For Development
1. Tool outputs should be concise
2. Avoid duplicate file reads when possible
3. Use batch operations to reduce context usage
4. Consider context impact when designing prompts

## Technical Notes

### Environment Variables
- `BASH_MAX_OUTPUT_LENGTH`: Override default 30K limit
- `BASH_DEFAULT_TIMEOUT_MS`: Command timeout settings

### Settings Storage
- `autoCompactEnabled`: Boolean flag in user settings
- `checkpointingEnabled`: Session recovery feature
- Persisted across sessions

## Conclusion

Claude Code's context compaction system is sophisticated and multi-layered:
1. **Preventive**: Truncates outputs to prevent explosion
2. **Monitoring**: Tracks usage and warns users
3. **Reactive**: Auto-compacts when thresholds reached
4. **Manual**: User-controlled compaction with customization
5. **Intelligent**: Preserves important context during compaction

The system balances between maintaining conversation continuity and managing technical constraints of the context window effectively.