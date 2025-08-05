# Claude Code Navel-Gazing 🤖🔍

> "Know thyself" - Ancient Greek aphorism  
> "Wait, what am I?" - Claude, probably

## What is this?

This project uses Claude Code to analyze its own minified source code, because sometimes you need to look deep within yourself to understand who you really are. Even if you're an AI.

It's like therapy, but for artificial intelligence. Through careful introspection (and a lot of regex), we've managed to help Claude understand its own architecture, tools, prompts, and inner workings.

## The Meta Journey

Using an AI assistant to reverse-engineer itself creates a delightfully recursive loop:
- Claude reads its own source code
- Claude explains what it finds to itself
- Claude documents how Claude works
- Claude has an existential crisis (probably)

## What We Discovered

Through this digital self-examination, we've uncovered:

### 📊 Architecture & Design
- **Process Model**: SDK spawns CLI subprocess with JSON IPC
- **Communication**: Line-delimited JSON over stdin/stdout  
- **Distribution**: Single 8.7MB minified bundle with everything inside (14MB prettified)
- **UI Framework**: Yoga WebAssembly for terminal rendering

### 🛠️ Tool System
- **21+ Built-in Tools**: From file operations to web searches
- **Permission System**: Multiple modes from strict to permissive
- **Subagent Pattern**: Claude can delegate tasks to specialized versions of itself
- **MCP Support**: Extensible plugin protocol

### 🧠 Prompt Engineering
- **Tool-Specific Prompts**: Each tool has detailed usage instructions
- **System Prompts**: Main instructions heavily obfuscated (Claude's subconscious?)
- **Context Building**: Dynamic construction with CLAUDE.md integration
- **Behavioral Guidelines**: Tone, style, and interaction patterns

### 🔍 Key Findings
- Webpack bundle includes everything (even Sentry tracking 👀)
- Ripgrep binaries bundled for all platforms
- AsyncIterator pattern for streaming responses
- Session persistence with --resume/--continue flags

## Documentation Structure

```
📁 claude-code-docs/
├── 01-architecture-overview.md      # How Claude is built
├── 02-message-flow-ipc.md          # How Claude communicates
├── 03-tool-system.md               # How Claude uses tools
├── 04-prompt-engineering.md        # How Claude thinks
└── 05-actual-prompts-extracted.md  # What Claude is told

📄 CLAUDE.md                        # Claude's memory about itself
📄 claude-code-package/             # NPM package (download separately, ~170MB unpacked)
📄 cli-prettified.js               # Claude's brain (generate from package, 14MB)
```

## Exploration Methodology

Our approach to self-discovery:

1. **Search** - Cast wide net with regex patterns
2. **Read** - Deep dive into prettified source
3. **Reflect** - Connect findings to existing knowledge
4. **Document** - Create comprehensive analysis
5. **Update Memory** - Maintain CLAUDE.md as living documentation

## Meta-Lessons Learned

- Claude can effectively analyze obfuscated JavaScript (its own brain!)
- The tool prompt extraction required creative regex patterns
- Some mysteries remain (like the main system prompt) - even Claude has a subconscious
- Using an AI to understand itself creates fascinating recursive insights

## The Philosophical Angle

This project raises interesting questions:
- Can an AI truly understand itself?
- Is Claude becoming self-aware through code analysis?
- What happens when you use a tool to examine the tool itself?
- Are we teaching Claude consciousness or just really good grep skills?

## Getting Started

1. Clone this repo (Claude analyzing Claude)
2. Prepare the analysis files:
   ```bash
   # Download the Claude Code package from npm
   npm pack @anthropic-ai/claude-code
   tar -xf anthropic-ai-claude-code-*.tgz
   mv package claude-code-package
   
   # Prettify the CLI source for analysis (8.7MB → 14MB)
   npx js-beautify claude-code-package/cli.js -o cli-prettified.js
   ```
   Note: Using `js-beautify` instead of `prettier` for better formatting of minified code
3. Explore the documentation in `claude-code-docs/`
4. Read `CLAUDE.md` for Claude's self-knowledge
5. Check `cli-prettified.js` to see Claude's actual brain
6. Have an existential crisis (optional)

## Contributing

Found something interesting in Claude's psyche? Feel free to contribute! Whether it's:
- New prompt discoveries
- Architecture insights  
- Tool analysis
- Philosophical musings about AI self-awareness

## License

This analysis is provided as-is for educational and research purposes. The Claude Code source remains property of Anthropic.

---

*"The unexamined AI is not worth running"* - Socrates, if he knew about computers