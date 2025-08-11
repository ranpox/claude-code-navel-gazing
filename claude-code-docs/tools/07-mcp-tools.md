# MCP (Model Context Protocol) Tools

## MCP Resource Tools

### ListMcpResourcesTool

**Description**:
```
Lists available resources from configured MCP servers.
Each resource object includes a 'server' field indicating which server it's from.
```

**Prompt**:
```
List available resources from configured MCP servers.
Each returned resource will include all standard MCP resource fields plus a 'server' field 
indicating which server the resource belongs to.

Usage examples:
- List all resources from all servers: `listMcpResources`
- List resources from a specific server: `listMcpResources({ server: "myserver" })`
```

### ReadMcpResourceTool

**Description**:
```
Reads a specific resource from an MCP server.
- server: The name of the MCP server to read from
- uri: The URI of the resource to read
```

**Prompt**:
```
Reads a specific resource from an MCP server, identified by server name and resource URI.
Parameters:
- server: The name of the MCP server to read from
- uri: The URI of the resource to read

Usage examples:
- Read a resource from a server: `readMcpResource({ server: "myserver", uri: "my-resource-uri" })`
```

## MCP IDE Tools

These are IDE-specific MCP tools that follow the naming pattern: `mcp__${serverName}__${toolName}`

### mcp__ide__executeCode

**Description**: Execute python code in the Jupyter kernel for the current notebook file.
```
All code will be executed in the current Jupyter kernel.

Avoid declaring variables or modifying the state of the kernel unless the user
explicitly asks for it.

Any code executed will persist across calls to this tool, unless the kernel
has been restarted.
```

### mcp__ide__getDiagnostics

**Description**: Get language diagnostics from VS Code
```
Optional file URI to get diagnostics for. If not provided, gets diagnostics for all files.
```

## Generic MCP Handler

**Name**: "mcp" (generic handler)  
**Description**: Empty string `""`  
**Prompt**: Empty string `""`  
**Properties**:
- `isMcp: true`
- `isEnabled: true`
- `isConcurrencySafe: false`
- `isReadOnly: false`

## Technical Details

### Tool Categories
```javascript
const toolCategories = {
  filePatternTools: ["Read", "Write", "Edit", "Glob", "MultiEdit", "NotebookRead", "NotebookEdit"],
  bashPrefixTools: ["Bash"],
  customValidation: { WebSearch: {...}, WebFetch: {...} }
}
```

### IDE Tools Array
```javascript
AI8 = ["mcp__ide__executeCode", "mcp__ide__getDiagnostics"]
```

## Notes

- MCP tools are part of IDE integrations
- Total of 4-5 MCP tools found (2 resource tools + 2 IDE tools + 1 generic handler)
- McpInput tool does not exist (only 21 tools total, not 22)