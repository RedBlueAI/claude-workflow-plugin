---
description: Set up Claude Workflow System in the current project - fetches from GitHub and configures based on your choices
---

# Claude Workflow Setup

You are setting up the Claude Workflow System in the current project directory.

## Pre-flight: Check for Plugin Updates

Before starting setup, check if a newer version of this plugin is available.

Current plugin version: **1.1.0**

```bash
REMOTE_VERSION=$(curl -s https://raw.githubusercontent.com/RedBlueAI/claude-workflow-plugin/main/.claude-plugin/plugin.json 2>/dev/null | grep -o '"version": "[^"]*"' | cut -d'"' -f4)
echo "Remote version: $REMOTE_VERSION"
```

If a newer version is available, display:
```
╭─────────────────────────────────────────────────────────────╮
│  ℹ Plugin update available: 1.1.0 → {remote_version}        │
│  Run: /plugin update claude-workflow@redblue-workflows      │
│  Continuing with setup...                                   │
╰─────────────────────────────────────────────────────────────╯
```

Then continue with setup (don't block on this).

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
rm -rf /tmp/claude-workflow-setup
git clone --depth 1 https://github.com/RedBlueAI/claude-workflow.git /tmp/claude-workflow-setup
```

If the clone fails, try fetching just the necessary files or inform the user of the network issue.

### Step 4: Select Variant

Read the variants from `/tmp/claude-workflow-setup/variants/` directory.

For each variant directory that contains a `manifest.json`, extract:
- Name
- Description
- Project type

Present the variants to the user using AskUserQuestion with options:
- **base** (Generic): Core workflow system without project-specific additions
- **nextjs-development**: Full-stack Next.js with App Router, specialized agents for React/API
- (Any other variants found in the repo)
- **Create New Variant**: Create a custom variant for your project type

**If user selects "Create New Variant":**

1. Ask for variant details using AskUserQuestion:
   - Variant name (lowercase, hyphens, e.g., "swift-ios", "flutter-mobile")
   - Description (e.g., "iOS development with Swift and SwiftUI")
   - Project type (e.g., "swift", "flutter", "rust", "go")

2. Create the variant structure in the temp directory:
   ```bash
   mkdir -p /tmp/claude-workflow-setup/variants/{name}/agents
   mkdir -p /tmp/claude-workflow-setup/variants/{name}/commands
   mkdir -p /tmp/claude-workflow-setup/variants/{name}/tasks
   mkdir -p /tmp/claude-workflow-setup/variants/{name}/templates
   ```

3. Create a basic manifest.json:
   ```json
   {
     "$schema": "../../base/schema/manifest.schema.json",
     "name": "{name}",
     "version": "1.0.0",
     "description": "{description}",
     "projectType": "{projectType}",
     "baseVersion": "1.0.0",
     "components": {
       "agents": [],
       "commands": [],
       "tasks": [],
       "templates": []
     }
   }
   ```

4. Create a basic specialist agent for the project type

5. Continue setup with this new variant selected

6. After setup completes, inform user:
   ```
   Custom variant '{name}' created locally.

   To contribute to the community:
   1. Enhance your variant in .claude/ as needed
   2. Run /SubmitVariant {name} to submit via pull request
   ```

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

### Step 7: Configure Project Integrations

Now configure project-specific integrations for Linear, Coda, and GitHub.

#### GitHub (Auto-detect):
```bash
git remote get-url origin 2>/dev/null || echo "No remote"
```
If a GitHub remote is detected, extract owner/repo and store in settings.

#### Linear Integration:
Ask the user using AskUserQuestion:
- **Configure Linear**: Set up Linear project for issue tracking
- **Skip for now**: Configure later with /SetupProjectMeta

If configuring Linear:
1. Use Linear MCP to list available teams
2. Let user select team
3. Use Linear MCP to list projects in that team
4. Let user select or create project
5. Store team and project IDs in settings

#### Coda Integration:
Ask the user using AskUserQuestion:
- **Configure Coda**: Set up Coda document for requirements
- **Skip for now**: Configure later with /SetupProjectMeta

If configuring Coda:
1. Use Coda MCP to list available documents
2. Let user select document
3. Use Coda MCP to list pages
4. Let user select or create requirements page
5. Store document and page IDs in settings

Update `.claude/settings.json` with the `projectMetadata` section:
```json
{
  "projectMetadata": {
    "linear": {
      "teamId": "...",
      "teamName": "...",
      "projectId": "...",
      "projectName": "..."
    },
    "coda": {
      "docId": "...",
      "docName": "...",
      "pageId": "...",
      "pageName": "..."
    },
    "github": {
      "repo": "owner/repo",
      "owner": "...",
      "name": "..."
    }
  }
}
```

If MCP servers are not available, inform user they can configure later with `/SetupProjectMeta`.

### Step 8: Cleanup

Remove the temporary clone:
```bash
rm -rf /tmp/claude-workflow-setup
```

### Step 9: Report Results

Provide a summary to the user:

1. **Files created**: List the main directories/files set up
2. **Variant installed**: Which variant was used
3. **Project integrations**: Show configured integrations status
   - GitHub: ✓ Configured (owner/repo) or ○ Not configured
   - Linear: ✓ Configured (team/project) or ○ Not configured
   - Coda: ✓ Configured (doc/page) or ○ Not configured
4. **Next steps**:
   - Review and customize `CLAUDE.md`
   - Add `ANTHROPIC_API_KEY` to GitHub secrets for CI workflows
   - Run `/SetupProjectMeta` if integrations were skipped
   - Run `/StartSession` to begin your first development session

If a custom variant was created:
```
Your custom variant '{name}' is ready!
To contribute it to the community, run: /SubmitVariant {name}
```

## Error Handling

- If git clone fails: Suggest checking network connection or manually downloading
- If file copy fails: Report which files failed and suggest manual copy
- If directory creation fails: Check permissions
- If MCP not available: Skip integration, note it can be configured later

## Important Notes

- This command uses Claude's native Bash and file tools - no external scripts needed
- All operations happen in the current working directory
- Existing files are preserved unless user explicitly chooses "Replace"
- The setup is idempotent - can be run multiple times safely with "Update" option
- Custom variants are created locally and can be submitted via /SubmitVariant
