# Claude Code CLI Configuration

Custom commands, hooks, and settings for Claude Code CLI.

## Structure

```
claude-code/
├── commands/     # Custom slash commands
├── hooks/        # Event hooks
└── settings.json # CLI settings
```

## Commands

Place custom slash command markdown files in the `commands/` directory.

## Hooks

Custom hooks for Claude Code events. Examples:
- Pre/post tool execution hooks
- Custom validation hooks
- Integration hooks

## Installation

Symlink or copy these files to your Claude Code configuration directory:

```bash
# macOS/Linux
ln -s /path/to/ai-toolkit/claude-code/commands ~/.claude/commands

# Windows
mklink /J "C:\Users\<username>\.claude\commands" "path\to\ai-toolkit\claude-code\commands"
```

## Usage

Refer to [Claude Code documentation](https://github.com/anthropics/claude-code) for details on creating and using custom commands and hooks.
