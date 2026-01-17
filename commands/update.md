---
description: Update an existing Claude Workflow System installation with the latest components from GitHub
---

# Claude Workflow Update

You are updating an existing Claude Workflow System installation in the current project.

## Update Process

### Step 1: Verify Existing Installation

Check that `.claude/` directory exists. If not, inform the user:
"No existing workflow installation found. Use `/claude-workflow:setup` to create a new installation."

### Step 2: Detect Current Configuration

Read `.claude/settings.json` if it exists to determine:
- Current variant (if any)
- Custom configurations

If settings.json doesn't exist, assume base installation.

### Step 3: Fetch Latest from GitHub

```bash
git clone --depth 1 https://github.com/RedBlueAI/claude-workflow.git /tmp/claude-workflow-update
```

### Step 4: Compare Versions

Check the VERSION file:
- Current: Read from `.claude/VERSION` if exists
- Latest: Read from `/tmp/claude-workflow-update/base/VERSION`

Report version information to user.

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

### Step 7: Update Version Marker

Copy VERSION file to `.claude/VERSION`:
```bash
cp /tmp/claude-workflow-update/base/VERSION .claude/VERSION
```

### Step 8: Cleanup

```bash
rm -rf /tmp/claude-workflow-update
```

### Step 9: Report Results

Show:
- Components updated
- New version
- Any files that were added
- Reminder to review changes

## Preserving Customizations

The update process preserves:
- `CLAUDE.md` (never overwritten)
- `.claude/settings.json` (merged, not replaced)
- Any custom agents/commands not from base/variant
