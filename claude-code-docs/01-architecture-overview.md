# Architecture Overview

## Package Structure

```
claude-code-package/
├── cli.js          # Minified CLI executable (8.7MB)
├── sdk.mjs         # SDK for programmatic access
├── sdk.d.ts        # TypeScript definitions
├── sdk-tools.d.ts  # Tool type definitions
├── yoga.wasm       # UI layout engine
└── vendor/         # Platform-specific binaries
    └── ripgrep binaries for each platform
```

## Core Components

### 1. CLI Process (`cli.js`)
- Webpack-bundled monolith containing entire application
- Includes:
  - Tool implementations
  - Message processing logic
  - Sentry error tracking
  - UI framework (Yoga WebAssembly)
  - System prompts (embedded, minified)
  - Vendor binary loaders

### 2. SDK Layer (`sdk.mjs`)
- Provides programmatic access to Claude Code
- Key classes:
  - `Query`: Main interaction class with AsyncIterator
  - `Stream`: Custom async stream implementation
- Handles:
  - Process spawning
  - IPC communication
  - Interrupt capability
  - Control message routing

### 3. Process Model

```
┌─────────────┐     spawn()      ┌─────────────┐
│   SDK       │ ───────────────> │   CLI       │
│  Process    │                  │  Process    │
│             │ <─────────────── │             │
└─────────────┘   JSON Stream    └─────────────┘
      ↓                                ↓
  User Code                      Claude API
```

### 4. Communication Architecture

#### Message Types
- **User Messages**: Input queries from users
- **Assistant Messages**: Claude's responses  
- **System Messages**: Initialization, status updates
- **Result Messages**: Final outcomes
- **Control Messages**: Permissions, interrupts

#### Control Flow
1. SDK spawns CLI subprocess
2. Bidirectional JSON stream established
3. Control requests/responses for permissions
4. Tool execution with progressive results
5. Stream closure on completion

## Design Principles

### 1. Process Isolation
- SDK and CLI run in separate processes
- Clean separation of concerns
- Crash isolation
- Security boundaries

### 2. Stream-Based Architecture
- No full buffering of large outputs
- Progressive results
- Real-time communication
- Interrupt support

### 3. Tool-Based Extensibility
- 21+ built-in tools
- Consistent tool interface
- Permission-based execution
- MCP protocol support

### 4. Zero Dependencies
- Everything bundled in cli.js
- No runtime npm dependencies
- Platform binaries included
- Self-contained distribution

## Key Architectural Decisions

### Why Process Separation?
- **Security**: Isolated execution environments
- **Stability**: CLI crashes don't affect SDK
- **Flexibility**: Different runtime configurations
- **Interrupts**: Clean process termination

### Why JSON Streaming?
- **Simplicity**: Line-delimited JSON parsing
- **Compatibility**: Works across all platforms
- **Debugging**: Human-readable protocol
- **Flexibility**: Easy to extend message types

### Why Webpack Bundle?
- **Distribution**: Single file deployment
- **Performance**: Optimized and minified
- **Security**: Source code protection
- **Versioning**: Locked dependency versions

## Platform Support

### Operating Systems
- macOS (Darwin) - ARM64, x64
- Linux - ARM64, x64, ARM32
- Windows - x64

### Runtime Requirements
- Node.js 18.0.0 or higher
- ES Module support
- Optional: Sharp for image processing

## Performance Characteristics

### Startup
- CLI process spawn: ~100-200ms
- Initial handshake: ~50ms
- Tool initialization: Lazy loaded

### Memory Usage
- Base CLI process: ~50-100MB
- SDK overhead: Minimal (~10MB)
- Scales with conversation length

### Streaming
- No message size limits
- Line-by-line processing
- Backpressure handling
- Interrupt latency: <100ms