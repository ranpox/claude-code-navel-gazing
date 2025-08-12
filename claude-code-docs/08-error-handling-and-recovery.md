# Error Handling and Recovery

> Analysis of error handling patterns in Claude Code

## Overview

This document examines error handling and recovery mechanisms in Claude Code based on the prettified CLI source code analysis.

## Key Findings

### Retry Mechanisms
- **Exponential backoff**: Found retry logic with backoff strategies
- **Max attempts limit**: Configurable retry limits (e.g., `retry_max_attempts_limit`)
- **Retry throttling**: Throttling policy to prevent excessive retries
- **Transparent retry**: Automatic retry for certain failure types
- **Retry quota management**: Token-based retry quota system

### Error Recovery Patterns
- **Sandbox fallback**: Falls back to regular methods when sandbox creation fails
- **Network resilience**: Built-in retry for network operations
- **Graceful degradation**: Continues with reduced functionality on partial failures

## Patterns Found

1. **Basic try-catch blocks**
2. **Promise rejection handling**
3. **Stream error events**
4. **Tool execution failures**
5. **Retry with exponential backoff**
6. **Throttled retry attempts**
7. **Sandbox iframe failures with fallback**
8. **gRPC retry configuration**
9. **Retry quota token management**

## Implementation Details

### Retry Configuration
```javascript
// Retry configuration found in gRPC/network layer
this.initialRetryBackoffSec = 0;
this.nextRetryBackoffSec = 0;
this.maxAttempts = 1; // Default when retries disabled
this.attempts = 0;
this.transparentRetryUsed = false;

// Configurable retry limit
const maxAttempts = channel.getOptions()["grpc-node.retry_max_attempts_limit"] ?? defaultLimit;
```

### Retry Decision Logic
```javascript
// Throttling check before retry
if (this.retryThrottler?.canRetryCall() ?? true) {
    this.attempts += 1;
    this.startNewAttempt();
} else {
    this.trace("Retry attempt denied by throttling policy");
}
```

### Retry Quota System
```javascript
// Token-based retry management
this.retryQuota.releaseRetryTokens(tokenCount);
metadata.attempts = attemptNumber + 1;
metadata.totalRetryDelay = totalDelay;
```

### Sandbox Fallback Pattern
```javascript
try {
    // Create sandbox iframe for security checks
    // Used for pure fetch operations
} catch (error) {
    DEBUG_BUILD && logger.warn(
        "Could not create sandbox iframe for pure fetch check, bailing to window.fetch"
    );
    // Fallback to regular fetch
}
```

## Error Categories

### Network Errors
- Connection failures
- Timeout errors
- Rate limiting responses
- Network retry with backoff

### Tool Execution Errors
- Permission denied
- Resource not found
- Validation failures
- Execution timeouts

### System Errors
- Out of memory
- Process crashes
- File system errors
- Sandbox creation failures

## Recovery Strategies

### 1. Automatic Retry
- Network operations with transient failures
- Configurable max attempts
- Exponential backoff between attempts
- Throttling to prevent retry storms

### 2. Fallback Mechanisms
- Sandbox → regular execution
- Primary API → fallback endpoints
- Feature degradation on errors

### 3. User Intervention
- Permission prompts on failures
- Error messages with recovery suggestions
- Manual retry options

## Limitations

### No Advanced Recovery
- No circuit breaker pattern
- Limited self-healing capabilities
- No automatic error correction
- Basic retry without sophisticated backoff algorithms

### Error Reporting
- Basic error messages
- Limited context in error reports
- No automatic error aggregation
- Minimal debugging information

## Best Practices Observed

1. **Defensive Programming**: Null checks and optional chaining
2. **Graceful Degradation**: Continue with reduced functionality
3. **Resource Cleanup**: Proper cleanup in error paths
4. **User Communication**: Clear error messages
5. **Logging**: Debug logging in development builds

## Recommendations for Users

1. **Monitor retry behavior** in logs when debugging issues
2. **Configure retry limits** appropriately for your use case
3. **Handle tool failures** gracefully in automation scripts
4. **Implement additional error handling** in SDK usage
5. **Use permission modes** to reduce interactive error prompts