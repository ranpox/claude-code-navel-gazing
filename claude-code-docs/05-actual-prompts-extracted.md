# Actual Prompts Extracted from Claude Code

This document contains actual prompts, instructions, and templates extracted from the prettified CLI source code.

## System-Level Instructions

### Copyright and Content Guidelines
Found in the prettified source:
```
Provide a concise response based only on the content above. In your response:
 - Enforce a strict 125-character maximum for quotes from any source document. Open Source Software is ok as long as we respect the license.
 - Use quotation marks for exact language from articles; any language outside of the quotation should never be word-for-word the same.
 - You are not a lawyer and never comment on the legality of your own prompts and responses.
 - Never produce or reproduce exact song lyrics.
```

### Important Instruction Reminders
```
Do what has been asked; nothing more, nothing less.
NEVER create files unless they're absolutely necessary for achieving your goal.
ALWAYS prefer editing an existing file to creating a new one.
NEVER proactively create documentation files (*.md) or README files. Only create documentation files if explicitly requested by the User.
```

### Task Management Guidelines
```
3. The task can be completed in less than 3 trivial steps
4. The task is purely conversational or informational
```

## Complete Tool Descriptions

### Read Tool
**Name**: Read  
**Short Description**: Read a file from the local filesystem.  
**Full Description**:
```
Reads a file from the local filesystem. You can access any file directly by using this tool.
Assume this tool is able to read all files on the machine. If the User provides a path to a file assume that path is valid. It is okay to read a file that does not exist; an error will be returned.

Usage:
- The file_path parameter must be an absolute path, not a relative path
- By default, it reads up to 2000 lines starting from the beginning of the file
- You can optionally specify a line offset and limit (especially handy for long files), but it's recommended to read the whole file by not providing these parameters
- Any lines longer than 2000 characters will be truncated
- Results are returned using cat -n format, with line numbers starting at 1
- This tool allows Claude Code to read images (eg PNG, JPG, etc). When reading an image file the contents are presented visually as Claude Code is a multimodal LLM.
- This tool can read PDF files (.pdf). PDFs are processed page by page, extracting both text and visual content for analysis.
- This tool can read Jupyter notebooks (.ipynb files) and returns all cells with their outputs, combining code, text, and visualizations.
- You have the capability to call multiple tools in a single response. It is always better to speculatively read multiple files as a batch that are potentially useful. 
- You will regularly be asked to read screenshots. If the user provides a path to a screenshot ALWAYS use this tool to view the file at the path. This tool will work with all temporary file paths like /var/folders/123/abc/T/TemporaryItems/NSIRD_screencaptureui_ZfB1tD/Screenshot.png
- If you read a file that exists but has empty contents you will receive a system reminder warning in place of file contents.
```

### Write Tool
**Name**: Write  
**Full Description**:
```
Writes a file to the local filesystem.

Usage:
- This tool will overwrite the existing file if there is one at the provided path.
- If this is an existing file, you MUST use the Read tool first to read the file's contents. This tool will fail if you did not read the file first.
- ALWAYS prefer editing existing files in the codebase. NEVER write new files unless explicitly required.
- NEVER proactively create documentation files (*.md) or README files. Only create documentation files if explicitly requested by the User.
- Only use emojis if the user explicitly requests it. Avoid writing emojis to files unless asked.
```

### Bash Tool
**Full Description**:
```
Executes a given bash command in a persistent shell session with optional timeout, ensuring proper handling and security measures.

Before executing the command, please follow these steps:

1. Directory Verification:
   - If the command will create new directories or files, first use the LS tool to verify the parent directory exists and is the correct location
   - For example, before running "mkdir foo/bar", first use LS to check that "foo" exists and is the intended parent directory

2. Command Execution:
   - Always quote file paths that contain spaces with double quotes (e.g., cd "path with spaces/file.txt")
   - Examples of proper quoting:
     - cd "/Users/name/My Documents" (correct)
     - cd /Users/name/My Documents (incorrect - will fail)
     - python "/path/with spaces/script.py" (correct)
     - python /path/with spaces/script.py (incorrect - will fail)
   - After ensuring proper quoting, execute the command.
   - Capture the output of the command.

Usage notes:
[continued in source...]
```

### Glob Tool
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

### Grep Tool
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

### LS Tool
**Name**: LS  
**Full Description**:
```
Lists files and directories in a given path. The path parameter must be an absolute path, not a relative path. You can optionally provide an array of glob patterns to ignore with the ignore parameter. You should generally prefer the Glob and Grep tools, if you know which directories to search.
```

### Edit Tool
**Name**: Edit  
**Full Description**:
```
Performs exact string replacements in files. 

Usage:
- You must use your `Read` tool at least once in the conversation before editing. This tool will error if you attempt an edit without reading the file. 
- When editing text from Read tool output, ensure you preserve the exact indentation (tabs/spaces) as it appears AFTER the line number prefix. The line number prefix format is: spaces + line number + tab. Everything after that tab is the actual file content to match. Never include any part of the line number prefix in the old_string or new_string.
- ALWAYS prefer editing existing files in the codebase. NEVER write new files unless explicitly required.
- Only use emojis if the user explicitly requests it. Avoid adding emojis to files unless asked.
- The edit will FAIL if `old_string` is not unique in the file. Either provide a larger string with more surrounding context to make it unique or use `replace_all` to change every instance of `old_string`. 
- Use `replace_all` for replacing and renaming strings across the file. This parameter is useful if you want to rename a variable for instance.
```

### TodoWrite Tool
**Name**: TodoWrite  
**Full Description**:
```
Use this tool to create and manage a structured task list for your current coding session. This helps you track progress, organize complex tasks, and demonstrate thoroughness to the user.
It also helps the user understand the progress of the task and overall progress of their requests.

## When to Use This Tool
Use this tool proactively in these scenarios:

1. Complex multi-step tasks - When a task requires 3 or more distinct steps or actions
2. Non-trivial and complex tasks - Tasks that require careful planning or multiple operations
3. User explicitly requests todo list - When the user directly asks you to use the todo list
4. User provides multiple tasks - When users provide a list of things to be done (numbered or comma-separated)
5. After receiving new instructions - Immediately capture user requirements as todos
6. When you start working on a task - Mark it as in_progress BEFORE beginning work. Ideally you should only have one todo as in_progress at a time
7. After completing a task - Mark it as completed and add any new follow-up tasks discovered during implementation

## When NOT to Use This Tool

Skip using this tool when:
1. There is only a single, straightforward task
2. The task is trivial and tracking it provides no organizational benefit
[continued in source...]
```

### WebFetch Tool
```
- Results may be summarized if the content is very large
- Includes a self-cleaning 15-minute cache for faster responses when repeatedly accessing the same URL
- When a URL redirects to a different host, the tool will inform you and provide the redirect URL in a special format. You should then make a new WebFetch request with the redirect URL to fetch the content.
```

## Permission and Error Messages

### Claude Code Installation
```
Claude Code on Windows requires git-bash (https://git-scm.com/downloads/win). If installed but not in PATH, set environment variable pointing to your bash.exe, similar to: CLAUDE_CODE_GIT_BASH_PATH=C:\\Program Files\\Git\\bin\\bash.exe
```

### Working Directory Notice
```
Note: You have launched claude in your home directory. For the best experience, launch it in a project directory instead.
```

### Permission Context
```
Claude Code will be able to read files in this directory and make edits when auto-accept edits is on.
```

### Permission Request Template
```
Claude requested permissions to use ${A.name}, but you haven't granted it yet.
```

## Context and Environment Information

### Claude Code Identification
Multiple references found to "Claude Code" throughout the source:
- `claude_code.pull_request.count` - metric tracking
- `claude_code.commit.count` - metric tracking  
- `claude_code.cost.usage` - cost tracking
- `claude_code.token.usage` - token tracking
- `claude_code.code_edit_tool.decision` - permission tracking

## Technical Implementation Details

### Tool Categories (from source)
```javascript
const toolCategories = {
  filePatternTools: ["Read", "Write", "Edit", "Glob", "MultiEdit", "NotebookRead", "NotebookEdit"],
  bashPrefixTools: ["Bash"],
  customValidation: { WebSearch: {...}, WebFetch: {...} }
}
```

### Permission Behaviors
```javascript
const JCQ = ["allow", "deny"];
```

## Observations

1. **Heavily Minified**: The CLI source is heavily minified and obfuscated, making it difficult to extract complete prompts
2. **Dynamic Construction**: Many prompts appear to be constructed dynamically at runtime
3. **Template Variables**: Heavy use of template variables (${variable}) suggests prompts are assembled from multiple sources
4. **Security Focus**: Strong emphasis on path validation, permission checking, and user consent
5. **Multimodal Support**: Clear support for images, PDFs, and other file types beyond text

## Next Steps for Extraction

1. Search for base64 encoded strings that might contain prompts
2. Look for string concatenation patterns building larger prompts
3. Analyze network traffic to see actual API calls
4. Search for more specific tool instruction patterns
5. Look for subagent-specific prompts and templates

## Notes

The actual system prompts and core instructions appear to be deeply embedded in the minified code and may be:
- Stored as encoded strings
- Built dynamically from multiple components
- Downloaded from a remote source
- Embedded in the webpack bundle in an obfuscated form

Further investigation using runtime analysis or network inspection may be needed to extract the complete prompts.