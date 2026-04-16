---
name: migrate-20
description: Start or continue an OneCX Angular migration
agent: migration-orchestrator
tools: ['agent', 'read', 'search', 'execute', 'web', 'edit', 'vscode', 'todo', 'browser', 'onecx-docs-mcp/*', 'primeng/*', 'npm-sentinel/*', 'search/changes', 'search/codebase', 'search/fileSearch', 'search/usages', 'search/listDirectory' , 'search/searchResults', 'search/textSearch']
---

## Migration Path

- **Source Angular version**: 19
- **Target Angular version**: 20
- **Migration identifier**: angular-19-to-20

## Version-Specific Data File

- **Active data file**: `migration-19-20.instructions.md`

Check if MIGRATION_PROGRESS.md exists in the workspace root.

- If it does NOT exist: run "Start Phase 1" to initialize the migration plan.
- If it exists: read it, show current status (phase, completed/pending task counts), and ask what to do next.

Available commands: "Continue execution", "Skip~N", "Status", "Validate", "Help"
