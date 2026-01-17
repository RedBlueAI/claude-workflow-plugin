---
description: Check the status of Claude Workflow System installation in the current project
---

# Workflow Status Check

You are checking the status of the Claude Workflow System in the current project.

## Pre-flight: Check for Plugin Updates

Current plugin version: **1.1.0**

```bash
REMOTE_VERSION=$(curl -s https://raw.githubusercontent.com/RedBlueAI/claude-workflow-plugin/main/.claude-plugin/plugin.json 2>/dev/null | grep -o '"version": "[^"]*"' | cut -d'"' -f4)
```

Note if an update is available (will be shown in the status report).

## Status Check Process

### Step 1: Check Installation

Verify the following directories and files exist:

**Required:**
- `.claude/` directory
- `.claude/commands/` directory
- `.claude/agents/` directory
- `.claude/tasks/` directory

**Optional but expected:**
- `CLAUDE.md`
- `.github/workflows/`
- `docs/planning/`
- `knowledge/`
- `.claude/settings.json`

### Step 2: Determine Installation Status

Based on what exists:

- **Not Installed**: No `.claude/` directory
- **Partial**: `.claude/` exists but missing components
- **Installed**: All required directories present
- **Full Setup**: All required + optional components present

### Step 3: Check Versions

**Workflow System Version:**
If `.claude/VERSION` exists, read and display the installed version.
Fetch latest from GitHub:
```bash
curl -s https://raw.githubusercontent.com/RedBlueAI/claude-workflow/main/base/VERSION
```

**Plugin Version:**
Compare current (1.1.0) with remote version fetched earlier.

### Step 4: Detect Variant

Check `.claude/settings.json` for variant information.
If not found, check for variant-specific files:
- `nextjs-development`: Look for nextjs-specialist.md in agents

### Step 5: Check Project Metadata

Read `.claude/settings.json` and check for `projectMetadata` section:
- Linear: Check if teamId/projectId configured
- Coda: Check if docId/pageId configured
- GitHub: Check if repo configured

### Step 6: Count Components

Count files in each directory:
- Commands: Count `.md` files in `.claude/commands/`
- Agents: Count `.md` files in `.claude/agents/`
- Tasks: Count `.md` files in `.claude/tasks/`
- Workflows: Count `.yml` files in `.github/workflows/`

### Step 7: Display Status Report

Format and display:

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                    Claude Workflow System Status                           ║
╚═══════════════════════════════════════════════════════════════════════════╝

Plugin Version:    1.1.0 ✓ (up to date)
                   or: 1.1.0 → 1.2.0 available (run /claude-workflow:check-update)

Workflow Version:  1.0.0 ✓ (up to date)
                   or: 1.0.0 → 1.1.0 available (run /claude-workflow:update)

Installation:      ✓ Installed
Variant:           nextjs-development

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Components:
  Commands:        15 installed
  Agents:          9 installed (6 base + 3 variant)
  Tasks:           12 installed
  Workflows:       2 installed

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Project Integrations:
  GitHub:          ✓ Configured (owner/repo-name)
  Linear:          ✓ Configured (Team / Project)
  Coda:            ○ Not configured

  To configure: /SetupProjectMeta

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Directory Structure:
  ✓ .claude/commands/
  ✓ .claude/agents/
  ✓ .claude/tasks/
  ✓ .github/workflows/
  ✓ docs/planning/
  ✓ knowledge/prd/
  ✓ CLAUDE.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Session Status:
  Active Session:  None
  Last Session:    2026-01-15 10:30

Available Commands:
  /StartSession, /EndSession, /ImplementFeature, /FixBug,
  /CreateVariant, /SubmitVariant, /SetupProjectMeta...
  (Run a command to see its usage)
```

### Step 8: Provide Recommendations

Based on status, recommend actions:

**If plugin update available:**
```
Recommendation: Update the plugin to get new features
  /plugin update claude-workflow@redblue-workflows
```

**If workflow update available:**
```
Recommendation: Update workflow components
  /claude-workflow:update
```

**If project metadata not configured:**
```
Recommendation: Configure project integrations
  /SetupProjectMeta
```

**If missing components:**
```
Recommendation: Repair installation
  /claude-workflow:setup (select Update)
```

**If not installed:**
```
Claude Workflow System is not installed in this project.

To install:
  /claude-workflow:setup
```
