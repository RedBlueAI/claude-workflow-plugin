# Changelog

All notable changes to the Claude Workflow Plugin will be documented in this file.

## [1.1.0] - 2026-01-17

### Added
- **Auto-update notifications**: Plugin now checks for updates and notifies users when a new version is available
- **Create New Variant**: During setup, users can now create custom variants for unsupported project types (Swift, Flutter, Rust, Go, etc.)
- **Project metadata configuration**: Setup now includes Linear, Coda, and GitHub integration configuration
- **New command**: `/claude-workflow:check-update` - Manually check for plugin updates
- **Version display**: All commands now show current plugin version and notify if updates are available

### Changed
- Setup process now includes Step 7 for configuring project integrations
- Variants command now shows option to create custom variants
- Status command now shows project integration status (Linear, Coda, GitHub)
- Update command now checks for and notifies about plugin updates separately from workflow updates

### How to Update
Existing users should update the plugin to get these new features:

```
/plugin update claude-workflow@redblue-workflows
```

Or reinstall:
```
/plugin uninstall claude-workflow
/plugin install claude-workflow@redblue-workflows
```

## [1.0.0] - 2026-01-15

### Initial Release
- `/claude-workflow:setup` - Set up workflow system in a project
- `/claude-workflow:update` - Update existing installation
- `/claude-workflow:variants` - List available variants
- `/claude-workflow:status` - Check installation status
- Support for base and nextjs-development variants
- Automatic fetching from GitHub repository
