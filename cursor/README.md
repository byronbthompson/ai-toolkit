# Cursor IDE Configuration

Custom slash commands and workflows for Cursor IDE.

## Commands

This directory contains custom slash commands (markdown files) that extend Cursor's capabilities with specialized workflows.

### Full Application Development Flow
- `full-app-00-start-here.md` - Entry point for full application development
- `full-app-01-prd.md` - Product requirements document generation
- `full-app-02-architecture.md` - Architecture planning
- `full-app-03-data-model.md` - Data model design
- `full-app-04-api-contract.md` - API contract definition
- `full-app-05-ui-ux.md` - UI/UX design
- `full-app-06-design-system.md` - Design system creation
- `full-app-07-security-nfr.md` - Security and non-functional requirements
- `full-app-08-testing-release.md` - Testing and release planning
- `full-app-09-decisions.md` - Decision logging
- `full-app-10-build-map.md` - Build roadmap
- `full-app-10a-plan-approval-gate.md` - Plan approval checkpoint
- `full-app-11-implement-milestone-n.md` - Milestone implementation
- `full-app-11a-milestone-demo.md` - Milestone demo preparation
- `full-app-completion-summary.md` - Project completion summary
- `full-app-flow-selection.md` - Flow selection helper
- `full-app-README-generator.md` - README generation

### Development Workflows
- `bug-workflow-builder.md` - Bug tracking and resolution workflow
- `devops-change-builder.md` - DevOps change management
- `feature-doc-builder.md` - Feature documentation
- `story-doc-builder.md` - User story documentation
- `refactor-gate.md` - Refactoring approval gate
- `verify-only.md` - Verification-only mode

### Discovery & Analysis
- `brownfield-context-discovery.md` - Understanding existing codebases
- `ui-pattern-discovery.md` - UI pattern analysis
- `network-flow-builder.md` - Network architecture planning
- `data-lakehouse-builder.md` - Data lakehouse architecture

### Learning & Knowledge Management
- `learnings-capture.md` - Capture learnings from development
- `learnings-review.md` - Review and consolidate learnings

## Installation

### Option 1: Symlink (Recommended)
Create a junction/symlink from your Cursor config directory to this repo:

```bash
# Windows (run as Administrator or use mklink /J for junction)
mklink /J "C:\Users\<username>\.cursor\commands" "path\to\ai-toolkit\cursor\commands"
```

### Option 2: Direct Copy
Copy the commands directory to your Cursor configuration:

```bash
cp -r cursor/commands/* ~/.cursor/commands/
```

## MCP Configuration

The `mcp.json` file contains Model Context Protocol server configurations. Review and customize based on your needs before using.

## Usage

Once installed, use these commands in Cursor with the `/` prefix:
- `/full-app-00-start-here` to begin a new application
- `/bug-workflow-builder` to start bug resolution
- `/learnings-capture` to document learnings
- etc.
