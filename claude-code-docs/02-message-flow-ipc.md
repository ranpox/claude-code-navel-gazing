# Message Flow & IPC

## Overview

Claude Code uses a line-delimited JSON protocol for inter-process communication between the SDK and CLI processes.

## Message Protocol

### Format
- Each message is a JSON object on a single line
- Messages are delimited by newline characters (`\n`)
- Bidirectional communication over stdin/stdout

### Message Structure
```typescript
interface Message {
  type: string;
  [key: string]: any;
}
```

## Message Types

### 1. Control Messages

#### Control Request (SDK → CLI or CLI → SDK)
```json
{
  "type": "control_request",
  "request_id": "random-id-123",
  "request": {
    "subtype": "initialize" | "interrupt" | "can_use_tool",
    // Additional fields based on subtype
  }
}
```

#### Control Response
```json
{
  "type": "control_response",
  "response": {
    "subtype": "success" | "error",
    "request_id": "random-id-123",
    "response": { /* response data */ },
    "error": "error message if subtype is error"
  }
}
```

### 2. Conversation Messages

#### User Message
```json
{
  "type": "user",
  "content": "User's input text"
}
```

#### Assistant Message
```json
{
  "type": "assistant",
  "content": "Claude's response"
}
```

#### System Message
```json
{
  "type": "system",
  "content": "System information or context"
}
```

## SDK Implementation Details

### Stream Processing Pipeline

```javascript
// From sdk.mjs
class Stream {
  queue = [];
  readResolve;
  readReject;
  isDone = false;
  hasError;
  
  async next() {
    if (this.queue.length > 0) {
      return Promise.resolve({
        done: false,
        value: this.queue.shift()
      });
    }
    // Wait for new messages
  }
  
  enqueue(value) {
    if (this.readResolve) {
      this.readResolve({ done: false, value });
    } else {
      this.queue.push(value);
    }
  }
}
```

### Query Class Architecture

```javascript
class Query {
  childStdin;
  childStdout;
  pendingControlResponses = new Map();
  
  async readMessages() {
    const rl = createInterface({ input: this.childStdout });
    
    for await (const line of rl) {
      if (line.trim()) {
        const message = JSON.parse(line);
        
        if (message.type === "control_response") {
          // Route to pending handler
          const handler = this.pendingControlResponses.get(
            message.response.request_id
          );
          if (handler) handler(message.response);
        } else if (message.type === "control_request") {
          // Handle control request
          this.handleControlRequest(message);
        } else {
          // Regular message - enqueue for consumer
          this.inputStream.enqueue(message);
        }
      }
    }
  }
}
```

## Control Flow Examples

### 1. Initialization Flow
```
SDK                           CLI
 |                             |
 |-- control_request --------> |
 |   (initialize)              |
 |                             |
 |<-- control_response ------- |
 |    (capabilities)           |
 |                             |
```

### 2. Tool Permission Flow
```
SDK                           CLI
 |                             |
 |                    (tool needed)
 |                             |
 |<-- control_request -------- |
 |    (can_use_tool)           |
 |                             |
 |-- control_response -------> |
 |    (true/false)             |
 |                             |
```

### 3. Interrupt Flow
```
SDK                           CLI
 |                             |
 |-- control_request --------> |
 |    (interrupt)              |
 |                             |
 |<-- control_response ------- |
 |    (acknowledged)           |
 |                             |
 |    (cleanup & exit)         |
```

## Error Handling

### Connection Errors
- Process spawn failures
- Pipe broken errors
- Unexpected process exit

### Protocol Errors
- Invalid JSON
- Missing required fields
- Unknown message types

### Recovery Strategies
1. **Graceful Degradation**: Continue with reduced functionality
2. **Error Propagation**: Surface errors to SDK consumers
3. **Process Restart**: For critical failures
4. **Timeout Handling**: For hung processes

## Performance Optimizations

### Message Queueing
- Buffered queue prevents backpressure
- Async enqueue/dequeue operations
- Memory-efficient for large streams

### Readline Interface
- Efficient line-by-line parsing
- No full stdout buffering
- Handles partial messages

### Control Message Priority
- Control messages processed immediately
- Separate routing from data messages
- Request/response correlation via IDs

## Security Considerations

### Input Validation
- JSON parsing in try-catch blocks
- Message type validation
- Size limits on message fields

### Process Isolation
- No shared memory between processes
- Clean process boundaries
- Controlled communication channel

### Permission Checks
- Tool usage requires explicit permission
- Control messages for authorization
- Bypass modes for trusted contexts