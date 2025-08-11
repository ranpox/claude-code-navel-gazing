# Model Selection and Advanced Features

## Model Configuration

### Available Models

#### Claude 3.5 Models
- **Claude 3.5 Sonnet** (line 310316)
  - First party: `claude-3-5-sonnet-20241022`
  - Bedrock: `anthropic.claude-3-5-sonnet-20241022-v2:0`
  - Vertex: `claude-3-5-sonnet-v2@20241022`

- **Claude 3.5 Haiku** (line 310321)
  - First party: `claude-3-5-haiku-20241022`
  - Bedrock/Vertex variants available

#### Claude 3.7 Models
- **Claude 3.7 Sonnet**
  - Vertex: `claude-3-7-sonnet@20250219`

#### Claude 4 Models
- **Claude Sonnet 4**
- **Claude Opus 4**

### Model Display Names (line 310338)
```javascript
if (B.includes("claude-sonnet-4")) return "Sonnet 4";
if (B.includes("claude-opus-4")) return "Opus 4";
if (B.includes("claude-3-7-sonnet")) return "Claude 3.7 Sonnet";
if (B.includes("claude-3-5-sonnet")) return "Claude 3.5 Sonnet";
if (B.includes("claude-3-5-haiku")) return "Claude 3.5 Haiku";
```

## Fallback Model System

### Fallback Triggering (line 349423)
```javascript
class FallbackTriggeredError extends Error {
    originalModel;
    fallbackModel;
    constructor(A, B) {
        super(`Model fallback triggered: ${A} -> ${B}`);
        this.originalModel = A;
        this.fallbackModel = B;
        this.name = "FallbackTriggeredError";
    }
}
```

### Fallback Logic (line 349446)
- Triggers when primary model is overloaded
- Automatic retry with fallback model
- Tracks retry attempts (max: `nP8` variable)
- Telemetry event: `tengu_api_opus_fallback_triggered`

### Command Line Support
- `--fallback-model <model>`: Enable automatic fallback
- Only works with `--print` flag
- Seamless switch when primary model overloaded

## Region Configuration

### Vertex AI Regions (line 312734)
Environment variables for regional model deployment:
- `VERTEX_REGION_CLAUDE_3_5_HAIKU`
- `VERTEX_REGION_CLAUDE_3_5_SONNET`
- `VERTEX_REGION_CLAUDE_3_7_SONNET`
- `VERTEX_REGION_CLAUDE_4_0_OPUS`
- `VERTEX_REGION_CLAUDE_4_0_SONNET`

## Advanced Code Features

### Limited Code Analysis
- No AST or syntax tree parsing found
- No semantic analysis capabilities
- No dependency tracking system
- Relies on text-based search (grep/glob)

### Excel Formula Support (line 65064)
Interesting discovery: Extensive Excel formula function names in keywords, suggesting some spreadsheet manipulation capabilities.

## Error Recovery Mechanisms

### Retry Logic
- **Retry-After Headers** (line 3161, 7422, 11414)
  - Respects server retry-after headers
  - Rate limit handling with backoff

### Sentry Integration
- Error reporting to Sentry
- Rate limit parsing from headers
- Queue system for failed events
- Automatic retry of queued events

### Error Handlers
- Fatal error handlers configurable
- Graceful degradation patterns
- No automatic code correction found

## Performance Optimizations

### Request Management
- Rate limiting detection and prevention
- Request queuing with retry
- Timeout management with AbortController
- Connection pooling (suggested by patterns)

## Missing Advanced Features

### Not Found
1. **AST-Based Analysis**: No code parsing or syntax trees
2. **Semantic Understanding**: No language-specific intelligence
3. **Dependency Graphs**: No module dependency tracking
4. **Code Refactoring**: No automated refactoring tools
5. **Type Analysis**: No type inference or checking
6. **Linting Integration**: No built-in linting
7. **Testing Framework**: No test generation or running

### Implications
- Claude Code relies on:
  - Text pattern matching (grep/glob)
  - File-based operations
  - External tool execution (Bash)
  - Model intelligence for understanding

## Security Considerations

### No Code Execution Sandboxing
- Bash commands execute directly
- No apparent containerization
- Relies on permission system

### Path Security
- Path validation patterns exist
- Traversal prevention (needs investigation)

## Key Insights

1. **Model Flexibility**: Multiple model options with regional deployment
2. **Fallback Resilience**: Automatic model switching on overload
3. **Simple Code Analysis**: Text-based rather than semantic
4. **Error Recovery**: Basic retry with backoff, no self-correction
5. **Performance**: Rate limiting and request management
6. **Security**: Permission-based rather than sandboxed

## Next Investigation Areas

1. Permission system implementation details
2. Path validation and security boundaries
3. Request queuing and optimization strategies
4. Integration with external tools (LSP, linters)
5. Custom tool development patterns