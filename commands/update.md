---
description: Update an existing Claude Workflow System installation with the latest components from GitHub
---

# Claude Workflow Update

You are updating an existing Claude Workflow System installation in the current project.

## Pre-flight: Check for Plugin Updates

Before starting, check if a newer version of this plugin is available.

Current plugin version: **1.1.0**

```bash
REMOTE_VERSION=$(curl -s https://raw.githubusercontent.com/RedBlueAI/claude-workflow-plugin/main/.claude-plugin/plugin.json 2>/dev/null | grep -o '"version": "[^"]*"' | cut -d'"' -f4)
echo "Remote version: $REMOTE_VERSION"
```

If a newer version is available, display:
```
╭─────────────────────────────────────────────────────────────╮
│  ⚠ Plugin update available: 1.1.0 → {remote_version}        │
│                                                              │
│  The plugin itself has updates. To get new plugin features:  │
│  /plugin update claude-workflow@redblue-workflows            │
│                                                              │
│  Continuing with workflow component update...                │
╰─────────────────────────────────────────────────────────────╯
```

Then continue with the update (don't block).

## Update Process

### Step 1: Verify Existing Installation

Check that `.claude/` directory exists. If not, inform the user:
"No existing workflow installation found. Use `/claude-workflow:setup` to create a new installation."

### Step 2: Detect Current Configuration

Read `.claude/settings.json` if it exists to determine:
- Current variant (if any)
- Custom configurations
- Project metadata settings

If settings.json doesn't exist, assume base installation.

### Step 3: Fetch Latest from GitHub

```bash
rm -rf /tmp/claude-workflow-update
git clone --depth 1 https://github.com/RedBlueAI/claude-workflow.git /tmp/claude-workflow-update
```

### Step 4: Compare Versions

Check the VERSION file:
- Current: Read from `.claude/VERSION` if exists
- Latest: Read from `/tmp/claude-workflow-update/base/VERSION`

Report version information to user:
```
Workflow System Versions:
  Current: 1.0.0
  Latest:  1.1.0

New in this version:
  • Project metadata configuration (Linear, Coda, GitHub)
  • Create custom variants with /CreateVariant
  • Submit variants to community with /SubmitVariant
```

### Step 5: Ask Update Scope

Use AskUserQuestion to ask what to update:

- **All components**: Commands, agents, tasks, and workflows
- **Commands only**: Just update commands
- **Agents only**: Just update agents
- **Tasks only**: Just update tasks
- **Workflows only**: Just update GitHub workflows

### Step 6: Perform Update

For each selected component type, copy files from the temp directory:

Use overlay strategy:
- New files are added
- Existing files are updated
- Local customizations in CLAUDE.md are preserved

Important: Preserve `.claude/settings.json` projectMetadata section if it exists.

### Step 7: Check Project Metadata

After updating, check if project metadata is configured:

```bash
grep -q '"projectMetadata"' .claude/settings.json 2>/dev/null
```

If project metadata is not configured, inform user:
```
New feature available: Project Integrations

Configure Linear, Coda, and GitHub integrations for your project:
  /SetupProjectMeta

This enables:
  • Issue tracking scoped to your Linear project
  • PRD storage in your Coda document
  • Automatic GitHub repository detection
```

### Step 8: Update Version Marker

Copy VERSION file to `.claude/VERSION`:
```bash
cp /tmp/claude-workflow-update/base/VERSION .claude/VERSION
```

### Step 9: Cleanup

```bash
rm -rf /tmp/claude-workflow-update
```

### Step 10: Report Results

Show:
- Components updated
- New version installed
- Any new files that were added
- New commands available (if any)
- Reminder to review changes

```
Update Complete!

  Version: 1.0.0 → 1.1.0

  Components updated:
    ✓ Commands (15 files)
    ✓ Agents (6 files)
    ✓ Tasks (12 files)

  New commands available:
    • /CreateVariant - Create custom variants
    • /SubmitVariant - Submit variants via PR
    • /SetupProjectMeta - Configure integrations

  Next steps:
    • Run /SetupProjectMeta to configure project integrations
    • Review updated commands with /help
```

## Preserving Customizations

The update process preserves:
- `CLAUDE.md` (never overwritten)
- `.claude/settings.json` (merged, not replaced)
- Project metadata configuration
- Any custom agents/commands not from base/variant
