# Claude Code Documentation

This documentation provides a comprehensive analysis of Claude Code's architecture, implementation, and design patterns based on reverse engineering the package.

## Table of Contents

1. [Architecture Overview](./01-architecture-overview.md)
   - Package structure
   - Core components
   - Process model
   - Communication patterns

2. [Message Flow & IPC](./02-message-flow-ipc.md)
   - SDK-CLI communication
   - JSON message protocol
   - Control messages
   - Stream processing

3. [Tool System](./03-tool-system.md)
   - Available tools (21+)
   - Tool categories
   - Permission system
   - Tool execution patterns

4. [Prompt Engineering](./04-prompt-engineering.md)
   - Context building pipeline
   - Prompt template structure
   - Context window management
   - Security constraints

5. [Actual Prompts Extracted](./05-actual-prompts-extracted.md)
   - Complete tool prompts found
   - System instructions
   - Permission messages
   - Error templates

6. [Agentic Flow](./06-agentic-flow.md)
   - User query processing
   - Context engineering
   - Subagent architecture
   - Task orchestration

7. [State Management](./07-state-management.md)
   - Conversation history
   - Session persistence
   - Working memory
   - Context accumulation

8. [Security Architecture](./08-security.md)
   - Permission modes
   - Sandboxing
   - Process isolation
   - Input validation

9. [Development Guide](./09-development-guide.md)
   - SDK usage
   - Programmatic access
   - Extension points
   - MCP integration

10. [Implementation Details](./10-implementation-details.md)
    - Webpack bundling
    - Minification analysis
    - Hidden features
    - Performance optimizations

## Quick Reference

- **Package**: @anthropic-ai/claude-code v1.0.68
- **Type**: ES Module, Node.js 18+
- **Main Files**: 
  - `cli.js` - Minified CLI (8.7MB)
  - `sdk.mjs` - SDK interface
  - `cli-prettified.js` - Prettified CLI (12MB) for analysis
- **Key Features**:
  - 21+ built-in tools
  - Subagent delegation
  - Stream-based IPC
  - Session persistence
  - MCP extensibility

## Related Documents

- [CLAUDE.md](../CLAUDE.md) - Original analysis summary
- [claude-code-exploration-plan.md](../claude-code-exploration-plan.md) - Research roadmap
- [cli-prettified.js](../cli-prettified.js) - Prettified source for analysis