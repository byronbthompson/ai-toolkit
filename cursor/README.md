# Cursor IDE Configuration

Custom slash commands and workflows for Cursor IDE.

## Commands

This directory contains custom slash commands (markdown files) that extend Cursor's capabilities with specialized workflows.

### Entry Point
- `00-WORKFLOW-SELECTOR.md` - **START HERE** - Decision tree to choose the right workflow for your task

### Full Application Development Flow (TDD - Technical Design Documents)
- `full-app-00-start-here-tdd.md` - Entry point for full application development
- `full-app-01-prd-tdd.md` - Product requirements document generation
- `full-app-02-architecture-tdd.md` - Architecture planning
- `full-app-03-data-model-tdd.md` - Data model design
- `full-app-04-api-contract-tdd.md` - API contract definition
- `full-app-05-ui-ux-tdd.md` - UI/UX design
- `full-app-06-design-system-tdd.md` - Design system creation
- `full-app-07-security-nfr-tdd.md` - Security and non-functional requirements
- `full-app-08-testing-release-tdd.md` - Testing and release planning
- `full-app-09-decisions-tdd.md` - Decision logging
- `full-app-10-build-map-tdd.md` - Build roadmap
- `full-app-10a-plan-approval-gate-tdd.md` - Plan approval checkpoint
- `full-app-11-implement-milestone-n.md` - Milestone implementation (execution)
- `full-app-11a-milestone-demo-tdd.md` - Milestone demo preparation
- `full-app-12-generate-tickets-tdd.md` - Generate tickets from specs
- `full-app-completion-summary-tdd.md` - Project completion summary
- `full-app-README-generator-tdd.md` - README generation

### Development Workflows (TDD)
- `brownfield-context-discovery-tdd.md` - Understanding existing codebases
- `feature-doc-builder-tdd.md` - Feature documentation
- `bug-workflow-builder-tdd.md` - Bug tracking and resolution workflow
- `story-doc-builder-tdd.md` - User story documentation

### Infrastructure & Operations (TDD - Ad-hoc)
- `deployment-tdd.md` - Deployment infrastructure design
- `monitoring-tdd.md` - Monitoring strategy design

### Learning & Knowledge Management (TDD)
- `learnings-capture-tdd.md` - Capture learnings from development

### Specialized Workflows (in specialized/ folder)
- `data-lakehouse-builder.md` - Data lakehouse architecture
- `network-flow-builder.md` - Network architecture planning
- `devops-change-builder.md` - DevOps change management
- `ui-pattern-discovery.md` - UI pattern analysis

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
- `/00-WORKFLOW-SELECTOR` to choose the right workflow (START HERE)
- `/full-app-00-start-here-tdd` to begin a new application
- `/bug-workflow-builder-tdd` to start bug resolution
- `/learnings-capture-tdd` to document learnings
- `/deployment-tdd` to design deployment infrastructure
- `/monitoring-tdd` to design monitoring strategy
- etc.

**Note**: Most workflows create Technical Design Documents (TDD) for planning. The only execution workflow is `full-app-11-implement-milestone-n.md`.
