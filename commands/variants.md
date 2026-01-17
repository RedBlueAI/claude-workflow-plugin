---
description: List available Claude Workflow System variants and their features
---

# List Workflow Variants

You are listing the available variants for the Claude Workflow System.

## Process

### Step 1: Fetch Variant Information

Clone the repository to get current variant information:

```bash
git clone --depth 1 https://github.com/RedBlueAI/claude-workflow.git /tmp/claude-workflow-variants
```

### Step 2: Read Variant Manifests

For each directory in `/tmp/claude-workflow-variants/variants/`:
1. Check if `manifest.json` exists
2. Read and parse the manifest
3. Extract key information

### Step 3: Display Variants

Present the variants in a formatted table:

**Available Variants:**

| Variant | Type | Description |
|---------|------|-------------|
| base | generic | Core workflow system - commands, agents, tasks for any project |
| nextjs-development | nextjs | Full-stack Next.js with App Router, React components, API routes |

For each variant, also show:
- **Agents**: List of specialized agents included
- **Commands**: List of additional commands
- **Required dependencies**: What the project needs

### Step 4: Show Variant Details

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

### Step 5: Cleanup

```bash
rm -rf /tmp/claude-workflow-variants
```

### Step 6: Provide Next Steps

Tell the user:
"To set up a project with a variant, run `/claude-workflow:setup` and select your preferred variant."
