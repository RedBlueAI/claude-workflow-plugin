# Claude Workflow Plugin

A Claude Code plugin that sets up and manages the Claude Workflow System in your projects. This plugin acts as an installer - it fetches the workflow system from GitHub and configures it in your target project.

## Table of Contents

- [Overview](#overview)
- [Installation](#installation)
- [Commands Reference](#commands-reference)
- [Quick Start Guide](#quick-start-guide)
- [How It Works](#how-it-works)
- [Troubleshooting](#troubleshooting)
- [Development](#development)

---

## Overview

The Claude Workflow Plugin provides a streamlined way to set up the Claude Workflow System in any project. Instead of manually cloning repositories or running scripts, you simply install the plugin once and then run setup commands in any project directory.

### Key Features

- **One-time installation**: Install the plugin once, use it across all your projects
- **Interactive setup**: Guided prompts help you choose the right configuration
- **Variant selection**: Choose from multiple project-type variants (base, Next.js, etc.)
- **Update support**: Keep your workflow installation current with the latest changes
- **Status checking**: Verify your installation and see what's configured

### Important Distinction

**The plugin is the installer, NOT the installed content.**

When you run `/claude-workflow:setup`, the plugin:
1. Fetches the workflow system from GitHub
2. Copies only the `base/` and `variants/` content to your project
3. Does NOT copy the plugin itself or other installer components

Your project receives:
- `.claude/commands/` - Workflow commands
- `.claude/agents/` - AI agents
- `.claude/tasks/` - Orchestration tasks
- `.github/workflows/` - CI/CD workflows
- `docs/` and `knowledge/` - Documentation structure
- `CLAUDE.md` - Project guidance

---

## Installation

### Method 1: From Plugin Marketplace (Recommended)

First, add the RedBlue workflows marketplace to Claude Code:

```
/plugin marketplace add https://github.com/RedBlueAI/claude-workflow-plugin.git
```

Then install the plugin:

```
/plugin install claude-workflow@redblue-workflows
```

### Method 2: Local Development/Testing

If you've cloned the repository locally:

```bash
claude --plugin-dir /path/to/claude-workflow/plugin
```

### Verify Installation

After installation, verify the plugin is available:

```
/claude-workflow:status
```

If you see the status output (even if it says "Not Installed"), the plugin is working correctly.

---

## Commands Reference

### `/claude-workflow:setup`

**Description**: Set up the Claude Workflow System in the current project directory.

**Usage**:
```
/claude-workflow:setup
```

**Process**:
1. Checks for git (required for fetching from GitHub)
2. Detects if this is a fresh setup or update/replace
3. Fetches the latest workflow system from GitHub
4. Presents available variants for selection
5. Asks for project name and description
6. Copies and configures all workflow components
7. Generates customized CLAUDE.md

**Options during setup**:
- **Fresh setup**: Creates new `.claude/` directory structure
- **Update**: Syncs new components while preserving your customizations
- **Replace**: Backs up existing setup and performs fresh install

---

### `/claude-workflow:update`

**Description**: Update an existing workflow installation to the latest version.

**Usage**:
```
/claude-workflow:update
```

**Options**:
- **Full update**: Updates all components (commands, agents, tasks, workflows)
- **Selective update**: Choose specific components to update
- **Preserve customizations**: Your local changes are preserved by default

---

### `/claude-workflow:variants`

**Description**: List all available project variants.

**Usage**:
```
/claude-workflow:variants
```

**Output**: Displays each variant with:
- Name and description
- Project type it's designed for
- Components included (agents, commands, tasks)
- Extends information (what it builds upon)

**Current Variants**:

| Variant | Type | Description |
|---------|------|-------------|
| `base` | Generic | Core workflow system for any project type |
| `nextjs-development` | Next.js | Full-stack Next.js with App Router, React, TypeScript |

---

### `/claude-workflow:status`

**Description**: Check the current workflow installation status.

**Usage**:
```
/claude-workflow:status
```

**Output includes**:
- Installation status (Not Installed / Partial / Installed / Full Setup)
- Installed version
- Detected variant
- Component counts (commands, agents, tasks, workflows)
- Directory structure verification
- Session status (if any active sessions)
- Available commands list
- Update recommendations (if outdated)

---

## Quick Start Guide

### Step 1: Install the Plugin

In Claude Code, add the marketplace and install the plugin:

```
/plugin marketplace add https://github.com/RedBlueAI/claude-workflow-plugin.git
/plugin install claude-workflow@redblue-workflows
```

### Step 2: Navigate to Your Project

```bash
cd /path/to/your/project
```

This can be:
- A new, empty directory
- An existing project you want to add workflows to
- A project with an older workflow installation you want to update

### Step 3: Run Setup

```
/claude-workflow:setup
```

Follow the interactive prompts:

1. **Setup Type** (if `.claude/` exists):
   - Update - Sync new components
   - Replace - Fresh install with backup
   - Cancel - Abort

2. **Select Variant**:
   - `base` - For any project type
   - `nextjs-development` - For Next.js projects

3. **Project Information**:
   - Project name (defaults to directory name)
   - Project description

### Step 4: Configure GitHub (Optional)

If you want CI/CD workflows for automated code review:

1. Go to your GitHub repository Settings
2. Navigate to Secrets and variables → Actions
3. Add `ANTHROPIC_API_KEY` with your API key

### Step 5: Start Using Workflows

Run your first workflow command:
```
/StartSession
```

This initializes a development session with context loading.

---

## How It Works

### Architecture

```
Claude Code Plugin System
         │
         ▼
┌─────────────────────────────┐
│   claude-workflow plugin    │  ← Installed in Claude Code
│   (installer/manager)       │
└─────────────────────────────┘
         │
         │ Fetches from GitHub
         ▼
┌─────────────────────────────┐
│  RedBlueAI/claude-workflow  │  ← GitHub Repository
│  ├── base/                  │
│  ├── variants/              │
│  ├── plugin/ (NOT copied)   │
│  └── scripts/ (NOT copied)  │
└─────────────────────────────┘
         │
         │ Copies base/ + selected variant/
         ▼
┌─────────────────────────────┐
│     Your Project            │  ← Target Project
│  ├── .claude/               │
│  │   ├── commands/          │
│  │   ├── agents/            │
│  │   └── tasks/             │
│  ├── .github/workflows/     │
│  ├── docs/                  │
│  ├── knowledge/             │
│  └── CLAUDE.md              │
└─────────────────────────────┘
```

### Setup Flow

1. **Prerequisites Check**: Verifies git is available
2. **Fetch**: Clones workflow repo to `/tmp/claude-workflow-setup`
3. **Analysis**: Detects existing installation, reads variant manifests
4. **Selection**: User chooses variant and provides project info
5. **Copy**: Transfers base layer + variant layer to project
6. **Template Processing**: Replaces `{{PROJECT_NAME}}` and similar variables
7. **Cleanup**: Removes temporary clone

### What Gets Copied

**Always copied (base layer)**:
- 12 commands (StartSession, EndSession, ImplementFeature, etc.)
- 6 agents (code-writer, reviewer, test-planner, etc.)
- 11 tasks (session-management, ci-integration, etc.)
- 2 GitHub workflows (code-review, security-review)
- Project templates and directory structure

**Never copied**:
- `plugin/` - The installer itself
- `scripts/` - Python installation scripts
- `docs/` - Repository documentation (not project docs)

### Variant Layer

If you select a variant other than `base`, additional components are copied:

**nextjs-development adds**:
- 3 agents: nextjs-specialist, react-component-writer, api-route-designer
- 2 commands: CreateComponent, CreateAPIRoute
- 1 task: nextjs-build
- Next.js-specific CLAUDE.md template

---

## Troubleshooting

### Plugin Not Found

**Symptom**: Commands like `/claude-workflow:setup` aren't recognized

**Solutions**:
1. Verify plugin installation: `/plugin list`
2. Check marketplace is added: `/plugin marketplace list`
3. Re-add marketplace and reinstall:
   ```
   /plugin marketplace add https://github.com/RedBlueAI/claude-workflow-plugin.git
   /plugin install claude-workflow@redblue-workflows
   ```
4. Check Claude Code version - plugins require recent versions

### Git Clone Fails

**Symptom**: "Failed to clone repository" error during setup

**Solutions**:
1. Check internet connection
2. Verify git is installed: `git --version`
3. Check GitHub access: `git ls-remote https://github.com/RedBlueAI/claude-workflow-plugin.git`
4. If behind a firewall, configure git proxy settings

### Permission Denied

**Symptom**: Can't create directories or copy files

**Solutions**:
1. Check write permissions on target directory
2. Run from a directory you own
3. Don't run setup in system directories

### Partial Installation

**Symptom**: Some components missing after setup

**Solutions**:
1. Run `/claude-workflow:status` to see what's missing
2. Run `/claude-workflow:update` to fill in missing components
3. Or run `/claude-workflow:setup` with "Replace" option for fresh install

### GitHub Actions Not Working

**Symptom**: PR reviews not triggering

**Solutions**:
1. Verify `ANTHROPIC_API_KEY` is set in repository secrets
2. Check Actions are enabled in repository settings
3. Ensure workflow files are in `.github/workflows/`
4. Review Actions tab for error messages

### Variant-Specific Issues

**Next.js variant not detecting project**:
- Ensure `package.json` contains `next` dependency
- Or `next.config.js`/`next.config.mjs` exists

---

## Development

### Plugin Structure

```
plugin/
├── .claude-plugin/
│   └── plugin.json      # Plugin manifest
├── commands/
│   ├── setup.md         # Main setup command
│   ├── update.md        # Update command
│   ├── variants.md      # List variants
│   └── status.md        # Status check
├── agents/
│   └── workflow-setup.md # Intelligent setup agent
└── README.md            # This file
```

### Plugin Manifest

The `plugin.json` defines the plugin:

```json
{
  "name": "claude-workflow",
  "description": "Set up and manage Claude Workflow System in your projects",
  "version": "1.0.0",
  "author": {
    "name": "RedBlue AI",
    "url": "https://github.com/RedBlueAI"
  },
  "repository": "https://github.com/RedBlueAI/claude-workflow"
}
```

### Testing Locally

1. Clone the repository:
   ```bash
   git clone https://github.com/RedBlueAI/claude-workflow-plugin.git
   ```

2. Run Claude Code with the plugin:
   ```bash
   claude --plugin-dir /path/to/claude-workflow/plugin
   ```

3. Test in a sample project directory:
   ```bash
   mkdir /tmp/test-project && cd /tmp/test-project
   /claude-workflow:setup
   ```

### Contributing

To contribute to the plugin:

1. Fork the repository
2. Make changes in the `plugin/` directory
3. Test locally using `--plugin-dir`
4. Submit a pull request

See [CONTRIBUTING.md](../CONTRIBUTING.md) for full guidelines.

---

## Requirements

| Component | Requirement |
|-----------|-------------|
| Claude Code | Latest version with plugin support |
| Git | Required for fetching from GitHub |
| Internet | Required during setup (fetches from GitHub) |

---

## Support

- [GitHub Issues](https://github.com/RedBlueAI/claude-workflow-plugin/issues)
- [Main Documentation](../docs/)
- [Architecture Guide](../docs/architecture.md)
