---
description: List available Claude Workflow System variants and their features
---

# List Workflow Variants

You are listing the available variants for the Claude Workflow System.

## Process

### Step 1: Check for Plugin Updates

Current plugin version: **1.1.0**

```bash
REMOTE_VERSION=$(curl -s https://raw.githubusercontent.com/RedBlueAI/claude-workflow-plugin/main/.claude-plugin/plugin.json 2>/dev/null | grep -o '"version": "[^"]*"' | cut -d'"' -f4)
```

If a newer version is available, briefly note it:
```
ℹ Plugin update available (1.1.0 → X.Y.Z). Run /claude-workflow:check-update for details.
```

### Step 2: Fetch Variant Information

Clone the repository to get current variant information:

```bash
rm -rf /tmp/claude-workflow-variants
git clone --depth 1 https://github.com/RedBlueAI/claude-workflow.git /tmp/claude-workflow-variants
```

### Step 3: Read Variant Manifests

For each directory in `/tmp/claude-workflow-variants/variants/`:
1. Check if `manifest.json` exists
2. Read and parse the manifest
3. Extract key information

### Step 4: Display Variants

Present the variants in a formatted table:

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                        Available Workflow Variants                         ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ Variant              │ Type     │ Description                              ║
╠══════════════════════╪══════════╪══════════════════════════════════════════╣
║ base                 │ generic  │ Core workflow system for any project     ║
║ nextjs-development   │ nextjs   │ Next.js with App Router, React, TS       ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

For each variant, also show:
- **Agents**: List of specialized agents included
- **Commands**: List of additional commands
- **Required dependencies**: What the project needs

### Step 5: Show Variant Details

For the `nextjs-development` variant, display:

```
nextjs-development (v1.0.0)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Next.js / React / TypeScript

Additional Agents:
  • nextjs-specialist - App Router patterns, server components
  • react-component-writer - Accessible React components
  • api-route-designer - RESTful API routes with validation

Additional Commands:
  • /CreateComponent - Generate React components with tests
  • /CreateAPIRoute - Generate API routes with validation

Project Structure Expected:
  • src/app/ - Next.js App Router
  • src/components/ - React components
  • src/lib/ - Utilities

Requirements:
  • next, react, react-dom, typescript
```

### Step 6: Show Create New Variant Option

After listing available variants, display:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Don't see a variant for your project type?

During /claude-workflow:setup, select "Create New Variant" to build a custom
variant for your technology stack (Swift, Flutter, Rust, Go, etc.)

After creating your variant, you can contribute it to the community:
  /SubmitVariant your-variant-name

All contributions go through branches and pull requests for review.
```

### Step 7: Cleanup

```bash
rm -rf /tmp/claude-workflow-variants
```

### Step 8: Provide Next Steps

Tell the user:
```
To set up a project with a variant:
  /claude-workflow:setup

To check for plugin updates:
  /claude-workflow:check-update
```
