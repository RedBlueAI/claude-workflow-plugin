---
description: Set up Claude Workflow System in the current project - fetches from GitHub and configures based on your choices
---

# Claude Workflow Setup

You are setting up the Claude Workflow System in the current project directory.

## Setup Process

Follow these steps exactly:

### Step 1: Check Prerequisites

First, verify git is available by running:
```bash
git --version
```

If git is not installed, inform the user they need to install git first.

### Step 2: Determine Setup Type

Check if `.claude/` directory exists in the current working directory.

- If `.claude/` exists: This is an **update/replace** scenario
- If `.claude/` does not exist: This is a **fresh setup**

For update/replace, ask the user using AskUserQuestion:
- **Update**: Sync new components while preserving customizations
- **Replace**: Back up existing and do fresh install
- **Cancel**: Abort setup

### Step 3: Fetch Workflow System from GitHub

Clone or fetch the workflow system repository to a temporary location:

```bash
git clone --depth 1 https://github.com/RedBlueAI/claude-workflow.git /tmp/claude-workflow-setup
```

If the clone fails, try fetching just the necessary files or inform the user of the network issue.

### Step 4: List Available Variants

Read the variants from `/tmp/claude-workflow-setup/variants/` directory.

For each variant directory that contains a `manifest.json`, extract:
- Name
- Description
- Project type

Present the variants to the user using AskUserQuestion with options:
- **base** (Generic): Core workflow system without project-specific additions
- **nextjs-development**: Full-stack Next.js with App Router, specialized agents for React/API
- (Any other variants found)

### Step 5: Gather Project Information

Ask the user using AskUserQuestion:

1. **Project name**: Default to current directory name
2. **Project description**: Brief description of the project

### Step 6: Execute Setup

Based on the choices, copy files from the cloned repo to the current project:

#### For Base Layer (always):

**IMPORTANT**: Only copy from `base/` and `variants/` directories. NEVER copy the `plugin/` directory - the plugin is the installer, not part of the installed workflow system.

```
/tmp/claude-workflow-setup/base/commands/* → .claude/commands/
/tmp/claude-workflow-setup/base/agents/* → .claude/agents/
/tmp/claude-workflow-setup/base/tasks/* → .claude/tasks/
/tmp/claude-workflow-setup/base/github-workflows/* → .github/workflows/
```

Do NOT copy:
- `/tmp/claude-workflow-setup/plugin/` (this is the installer itself)
- `/tmp/claude-workflow-setup/scripts/` (Python scripts are alternatives to the plugin)
- `/tmp/claude-workflow-setup/docs/` (repository documentation, not project docs)
- `/tmp/claude-workflow-setup/mcp-server/` (if exists)

Create directories if they don't exist:
- `.claude/commands/`
- `.claude/agents/`
- `.claude/tasks/`
- `.github/workflows/`
- `docs/planning/`
- `docs/specs/`
- `knowledge/prd/`
- `knowledge/architecture/`

#### For Selected Variant (if not base):
Copy variant-specific files based on the variant's `manifest.json` components section.

#### Process Templates:
For files ending in `.template`:
1. Read the template content
2. Replace `{{PROJECT_NAME}}` with the provided project name
3. Replace `{{PROJECT_DESCRIPTION}}` with the provided description
4. Write to the destination without the `.template` extension

#### Create CLAUDE.md:
If CLAUDE.md doesn't exist or user chose replace:
- Use the variant's template if available
- Otherwise use base template
- Replace all template variables

### Step 7: Cleanup

Remove the temporary clone:
```bash
rm -rf /tmp/claude-workflow-setup
```

### Step 8: Report Results

Provide a summary to the user:

1. **Files created**: List the main directories/files set up
2. **Variant installed**: Which variant was used
3. **Next steps**:
   - Review and customize `CLAUDE.md`
   - Add `ANTHROPIC_API_KEY` to GitHub secrets for CI workflows
   - Run `/StartSession` to begin your first development session

## Error Handling

- If git clone fails: Suggest checking network connection or manually downloading
- If file copy fails: Report which files failed and suggest manual copy
- If directory creation fails: Check permissions

## Important Notes

- This command uses Claude's native Bash and file tools - no external scripts needed
- All operations happen in the current working directory
- Existing files are preserved unless user explicitly chooses "Replace"
- The setup is idempotent - can be run multiple times safely with "Update" option
