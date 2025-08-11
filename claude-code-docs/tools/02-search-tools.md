# Search and Navigation Tools

## Glob Tool

**Name**: Glob  
**Full Description**:
```
- Fast file pattern matching tool that works with any codebase size
- Supports glob patterns like "**/*.js" or "src/**/*.ts"
- Returns matching file paths sorted by modification time
- Use this tool when you need to find files by name patterns
- When you are doing an open ended search that may require multiple rounds of globbing and grepping, use the Agent tool instead
- You have the capability to call multiple tools in a single response. It is always better to speculatively perform multiple searches as a batch that are potentially useful.
```

## Grep Tool

**Name**: Grep  
**Full Description**:
```
A powerful search tool built on ripgrep

Usage:
- ALWAYS use Grep for search tasks. NEVER invoke `grep` or `rg` as a Bash command. The Grep tool has been optimized for correct permissions and access.
- Supports full regex syntax (e.g., "log.*Error", "function\\s+\\w+")
- Filter files with glob parameter (e.g., "*.js", "**/*.tsx") or type parameter (e.g., "js", "py", "rust")
- Output modes: "content" shows matching lines, "files_with_matches" shows only file paths (default), "count" shows match counts
- Use Task tool for open-ended searches requiring multiple rounds
- Pattern syntax: Uses ripgrep (not grep) - literal braces need escaping (use `interface\{\}` to find `interface{}` in Go code)
- Multiline matching: By default patterns match within single lines only. For cross-line patterns like `struct \{[\s\S]*?field`, use `multiline: true`
```

### Grep Parameters
- **pattern** (required): The regular expression pattern to search for
- **path**: File or directory to search in (defaults to current working directory)
- **glob**: Glob pattern to filter files (e.g. "*.js", "*.{ts,tsx}")
- **type**: File type to search (e.g. "js", "py", "rust")
- **output_mode**: "content", "files_with_matches" (default), or "count"
- **-i**: Case insensitive search
- **-n**: Show line numbers (requires output_mode: "content")
- **-A/-B/-C**: Lines to show after/before/around matches (requires output_mode: "content")
- **head_limit**: Limit output to first N lines/entries
- **multiline**: Enable multiline mode for patterns spanning lines

## LS Tool

**Name**: LS  
**Full Description**:
```
Lists files and directories in a given path. The path parameter must be an absolute path, not a relative path. You can optionally provide an array of glob patterns to ignore with the ignore parameter. You should generally prefer the Glob and Grep tools, if you know which directories to search.
```

### LS Parameters
- **path** (required): The absolute path to the directory to list (must be absolute, not relative)
- **ignore**: List of glob patterns to ignore