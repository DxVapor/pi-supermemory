# 🧠 Pi SuperMemory Extension

[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=DxVapor_pi-supermemory&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=DxVapor_pi-supermemory)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=DxVapor_pi-supermemory&metric=bugs)](https://sonarcloud.io/summary/new_code?id=DxVapor_pi-supermemory)
[![Vulnerabilities](https://sonarcloud.io/api/project_badges/measure?project=DxVapor_pi-supermemory&metric=vulnerabilities)](https://sonarcloud.io/summary/new_code?id=DxVapor_pi-supermemory)
[![Security Rating](https://sonarcloud.io/api/project_badges/measure?project=DxVapor_pi-supermemory&metric=security_rating)](https://sonarcloud.io/summary/new_code?id=DxVapor_pi-supermemory)
[![Maintainability Rating](https://sonarcloud.io/api/project_badges/measure?project=DxVapor_pi-supermemory&metric=sqale_rating)](https://sonarcloud.io/summary/new_code?id=DxVapor_pi-supermemory)
[![Reliability Rating](https://sonarcloud.io/api/project_badges/measure?project=DxVapor_pi-supermemory&metric=reliability_rating)](https://sonarcloud.io/summary/new_code?id=DxVapor_pi-supermemory)
[![Code Smells](https://sonarcloud.io/api/project_badges/measure?project=DxVapor_pi-supermemory&metric=code_smells)](https://sonarcloud.io/summary/new_code?id=DxVapor_pi-supermemory)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=DxVapor_pi-supermemory&metric=coverage)](https://sonarcloud.io/summary/new_code?id=DxVapor_pi-supermemory)
[![npm version](https://img.shields.io/npm/v/pi-supermemory)](https://www.npmjs.com/package/pi-supermemory)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Persistent memory for [Pi](https://github.com/mariozechner/pi) - the AI coding agent that remembers context across sessions using [SuperMemory](https://supermemory.ai).

**Note:** This extension is compatible with SuperMemory SDK v2. API parameters have changed from earlier versions (`containerTag` instead of `containerTags`, `limit` instead of `topK`, `q` instead of `context`, `forget` instead of `delete`).

## Overview

This extension gives Pi long-term memory capabilities, allowing it to:

- **Remember user preferences** across all projects (coding style, favorite tools, etc.)
- **Maintain project context** between sessions (architecture decisions, pending tasks)
- **Learn from conversations** and recall relevant information when needed
- **Build a user profile** that accumulates facts about you over time

## Features

### 🔄 Automatic Context Injection
- Relevant memories are automatically injected into prompts based on semantic similarity
- No manual searching required - memories surface when they're needed

### 🎯 Two Memory Scopes
- **User scope**: Cross-project knowledge that follows you everywhere
- **Project scope**: Project-specific knowledge tied to the current directory

### 🤖 LLM Tool Access
The `supermemory` tool gives the AI direct access to:
- `add` - Store new memories with type classification
- `search` - Find relevant memories by query
- `profile` - View accumulated user profile
- `list` - Browse recent memories
- `forget` - Delete specific memories

### ⌨️ Slash Commands
- `/remember <text>` - Quick-save something to memory
- `/recall <query>` - Search your memories
- `/memory-profile` - View your user profile

### 💾 Automatic Conversation Capture
Conversations are automatically stored (when substantial) for future reference.

## Installation

### 1. Get a SuperMemory API key

Sign up at [supermemory.ai](https://supermemory.ai) and generate an API key from your dashboard.

### 2. Set your API key

```bash
export SUPERMEMORY_API_KEY="your-api-key-here"
```

Add this to your shell profile (`~/.zshrc`, `~/.bashrc`, etc.) so it persists across sessions.

### 3. Install the extension

Install globally (available in all projects):

```bash
pi install npm:pi-supermemory
```

Or install for a specific project only:

```bash
pi install -l npm:pi-supermemory
```

That's it — Pi will load the extension automatically on next start.

> **Try before you install:** Run for a single session without saving:
> ```bash
> pi -e npm:pi-supermemory
> ```

### Verify Installation

Start Pi and look for the 🧠 indicator in the status bar, or check that no `SUPERMEMORY_API_KEY not set` warning appears.

### Optional: Customize container prefix

```bash
# Default prefix is "pi" — change if you want org-scoped tags
export SUPERMEMORY_CONTAINER="myorg"
```

## Usage

### Let the AI Remember Things

Simply tell Pi to remember something:

> "Remember that I prefer 2-space indentation in TypeScript"
> "Save this for later - the database connection string is..."
> "Remember this project uses a monorepo structure"

The AI will use the `supermemory` tool to store this information with the appropriate scope and type.

### Manual Commands

```bash
# Quick save
/remember I always use pnpm instead of npm

# Search memories
/recall database schema

# View your profile
/memory-profile
```

### Memory Types

When storing memories, they can be classified as:

| Type | Description |
|------|-------------|
| `preference` | Personal preferences (coding style, tools, etc.) |
| `project-config` | Project configuration details |
| `architecture` | System design and architecture decisions |
| `error-solution` | Solutions to problems you've encountered |
| `learned-pattern` | General patterns and insights |
| `conversation` | Automatically captured conversations |

### Memory Scopes

- **`user`**: Available across all projects (good for personal preferences)
- **`project`**: Only available in the current project directory (default)

## Configuration

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `SUPERMEMORY_API_KEY` | Yes | - | Your SuperMemory API key |
| `SUPERMEMORY_CONTAINER` | No | `pi` | Prefix for container tags |

### How Container Tags Work

The extension uses container tags to organize memories:
- User memories: `{SUPERMEMORY_CONTAINER}_user_{username}`
- Project memories: `{SUPERMEMORY_CONTAINER}_project_{project_name}`

This keeps different users' and projects' memories separate.

### Per-Project Container Tag

Override the project container tag via `.pi/settings.json` in your repository:

```json
{
  "supermemory": {
    "containerTag": "my-custom-project-tag"
  }
}
```

This is useful when you want multiple directories to share the same memory space, or when you want a stable tag regardless of the folder name.

## API Reference

### Tool: `supermemory`

The LLM can call this tool with the following parameters:

```typescript
{
  mode: "add" | "search" | "profile" | "list" | "forget" | "help",
  content?: string,      // For "add" mode
  query?: string,        // For "search" or "profile" modes
  type?: MemoryType,     // For "add" mode
  scope?: "user" | "project",  // For "add", "search", "list" modes
  memoryId?: string,     // For "forget" mode
  limit?: number         // For "search" and "list" modes
}
```

**Note:** The SuperMemory SDK v2 API uses `containerTag` (singular), `limit` (instead of `topK`), and `q` (instead of `context`). This extension has been updated to match the latest SDK.

### Example Tool Calls

```typescript
// Store a preference
{ mode: "add", content: "Prefers dark mode themes", type: "preference", scope: "user" }

// Search for relevant memories
{ mode: "search", query: "database connection", scope: "project", limit: 5 }

// View user profile
{ mode: "profile", query: "coding preferences" }

// List recent memories
{ mode: "list", scope: "project", limit: 10 }

// Forget a memory
{ mode: "forget", memoryId: "mem_abc123", scope: "project" }
```

## Architecture

The extension works by:

1. **Before each agent turn**: Searches SuperMemory for context relevant to the user's prompt, injects matching memories into the system prompt

2. **After each agent turn**: Captures the conversation text and stores it in project scope for future reference

3. **During tool execution**: Provides the LLM with tools to actively manage memory (add, search, delete)

4. **On session start**: Shows a 🧠 status indicator when memory is active

## Development

```bash
# Clone the repo
git clone git@github.com:DxVapor/pi-supermemory.git
cd pi-supermemory

# Install dependencies
npm install

# Type check
npx tsc --noEmit --strict --target ES2022 --moduleResolution bundler --module ES2022 index.ts

# Load locally in Pi for testing
pi install ./path/to/pi-supermemory
# or for a single session:
pi -e ./index.ts
```

## Troubleshooting

### "SUPERMEMORY_API_KEY not set - extension disabled"
- Make sure you've set the `SUPERMEMORY_API_KEY` environment variable
- Restart Pi after setting environment variables

### Memories not appearing
- Check that the SuperMemory API key is valid
- Verify network connectivity to supermemory.ai
- Check Pi's console for error messages

### Wrong project scope
- The project scope is derived from the current working directory name
- Make sure you're in the correct project directory

## Related

- [Pi Coding Agent](https://github.com/mariozechner/pi) - The AI coding agent this extension is for
- [SuperMemory](https://supermemory.ai) - The memory service powering this extension

## License

MIT

## Contributing

Contributions welcome! Please open an issue or PR on [GitHub](https://github.com/DxVapor/pi-supermemory).
