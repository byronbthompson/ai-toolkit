# AI Toolkit

Personal collection of AI development tools, workflows, and configurations across multiple AI-powered development environments.

## Overview

This repository contains reusable configurations, custom commands, and workflows for various AI coding assistants and tools. The goal is to maintain a centralized, version-controlled collection of AI development assets that can be easily shared across machines and projects.

## Structure

```
ai-toolkit/
├── cursor/          # Cursor IDE configurations
├── claude-code/     # Claude Code CLI configurations
├── copilot/         # GitHub Copilot configurations
└── prompts/         # Shared prompts across tools
```

## Contents

### Cursor
Custom slash commands and workflows for Cursor IDE. See [cursor/README.md](cursor/README.md) for details.

### Claude Code
CLI configurations, custom commands, and hooks for Claude Code. See [claude-code/README.md](claude-code/README.md) for details.

### Copilot
GitHub Copilot configurations and custom instructions. See [copilot/README.md](copilot/README.md) for details.

### Prompts
Reusable prompts organized by category (engineering, architecture, documentation) that can be used across different AI tools.

## Usage

Clone this repository and symlink the relevant directories to your local AI tool configuration locations:

```bash
# Example for Cursor on Windows
mklink /J "C:\Users\<username>\.cursor\commands" "E:\_projects\ai-toolkit\cursor\commands"
```

## Contributing

This is a personal toolkit, but feel free to fork and adapt for your own use.

## License

MIT License - feel free to use and modify as needed.
