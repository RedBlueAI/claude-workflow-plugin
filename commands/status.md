---
description: Check the status of Claude Workflow System installation in the current project
---

# Workflow Status Check

You are checking the status of the Claude Workflow System in the current project.

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

### Step 2: Determine Installation Status

Based on what exists:

- **Not Installed**: No `.claude/` directory
- **Partial**: `.claude/` exists but missing components
- **Installed**: All required directories present
- **Full Setup**: All required + optional components present

### Step 3: Check Version

If `.claude/VERSION` exists, read and display the installed version.

### Step 4: Detect Variant

Check `.claude/settings.json` for variant information.
If not found, check for variant-specific files:
- `nextjs-development`: Look for nextjs-specialist.md in agents

### Step 5: Count Components

Count files in each directory:
- Commands: Count `.md` files in `.claude/commands/`
- Agents: Count `.md` files in `.claude/agents/`
- Tasks: Count `.md` files in `.claude/tasks/`
- Workflows: Count `.yml` files in `.github/workflows/`

### Step 6: Check for Updates

Fetch latest version from GitHub:
```bash
curl -s https://raw.githubusercontent.com/RedBlueAI/claude-workflow/main/base/VERSION
```

Compare with installed version.

### Step 7: Display Status Report

Format and display:

```
Claude Workflow System Status
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Installation: ✓ Installed
Version: 1.0.0 (latest)
Variant: nextjs-development

Components:
  Commands:  12 installed
  Agents:    9 installed (6 base + 3 variant)
  Tasks:     12 installed (11 base + 1 variant)
  Workflows: 2 installed

Directory Structure:
  ✓ .claude/commands/
  ✓ .claude/agents/
  ✓ .claude/tasks/
  ✓ .github/workflows/
  ✓ docs/planning/
  ✓ knowledge/prd/
  ✓ CLAUDE.md

Session Status:
  Active Session: None
  Last Session: 2026-01-15

Available Commands:
  /StartSession, /EndSession, /ImplementFeature, /FixBug...
  (Run /help for full list)
```

### Step 8: Provide Recommendations

If issues found:
- Missing components: Suggest `/claude-workflow:update`
- Outdated version: Suggest `/claude-workflow:update`
- Not installed: Suggest `/claude-workflow:setup`
