---
name: Angular 19 to 20 Migration Data
description: Version-specific URLs, version mappings, and known issues for Angular 19 → 20. Referenced by migration-config.instructions.md.
---

# Angular 19 → 20 Migration Data

<!--
  This file contains ALL version-specific data for Angular 19 → 20.
  It is NOT auto-injected (no applyTo). Agents read it on-demand.
  To support a new migration: copy this file, rename, fill in new data.
-->

## Documentation Sources
  
### OneCX Docs MCP
- **available**: true
- **Mcp tool**: about_onecx
- **start-query**: "Angular 20 migration"
- **fallback-url**: https://onecx.github.io/docs/documentation/current/onecx-portal-ui-libs/migrations/angular-20/index.html

### PrimeNG MCP
- **available**: true
- **Mcp tool**: primeng
- **start-query**: "PrimeNG migration guide"
- **migration-tools**: migrate_v19_to_v20
- **fallback-url**: https://primeng.org/migration/v20

### Nx MCP
- **available**: false
- **Mcp tool**: nx
- **start-query**: "Nx Angular migration guide"
- **fallback-url**: https://nx.dev/docs/technologies/angular/migrations
- **condition**: Only if workspace uses Nx (nx.json present)

### NPM Sentinel MCP
- **available**: true
- **Mcp tool**: npm-sentinel
- **start-query**: N/A — used for package intelligence
- **key-tools**: npmLatest, npmVulnerabilities, npmDeprecated, npmDeps, npmVersions, npmSize


### Documentation Fallback Priority
1. MCP servers (if available = true above)
2. Fallback URLs (listed in MCP sections above)
3. node_modules CHANGELOGs (`node_modules/<package>/CHANGELOG.md`)

### Phase B Defaults
- **Default phase B ownership**: assistant
- **Default approval response**: Yes (proceed with upgrade)


## Target Versions
<!-- Fill in with exact versions from documentation -->
- @angular/core: ^20.x.x
- primeng: ^20.x.x
- nx: ^21.x.x (if applicable)
- typescript: ~6.x.x
- Node.js minimum: 20.x

## PrimeNG Intermediate Guides
- v20 guide : use primeng MCP tool `migrate_v19_to_v20`
- v20 fallback guide: https://primeng.org/migration/v20

## Known Breaking Changes
<!-- List version-specific breaking changes discovered during planning -->
- [Add entries here during Phase 1 documentation discovery]

## Version-Specific Workarounds
<!-- Known issues and their fixes for this migration path -->
- [Add entries here as discovered during execution]

## @onecx Package Version Map
| Package | Documented Version | Notes |
|---------|-------------------|-------|
| [package] | [version] | [source page] |


## Special Migration Rules (Angular 19 → 20 Specific)
These rules apply specifically to the Angular 19 → 20 migration path:
- **Styles handling**: Apply styles.scss changes exactly as documented. If conflict between Nx styles array and Sass @import → STOP and ask which pattern to use
- **Standalone components**: If error "Component is standalone, and cannot be declared in an NgModule" → add `standalone: false` and document why
- **Angular version caret**: MUST keep caret (`^`) in package.json for `@angular/*` packages — module federation requires compatible version ranges for sharing
- **package-lock.json**: Delete and regenerate with `npm install` at major transition points (after Phase A packages, after Phase C packages) — prevents stale lock file conflicts

## Task-Specific Applicability Rules
These rules MUST be followed when determining whether a task applies. The docs describe WHAT to do — these rules tell you HOW to check applicability. For EVERY task, independently search the ENTIRE codebase for ALL patterns listed below.

### Update Component Imports
- For EACH section in the doc, search `.ts` files for imports of the listed component/service/directive names from the OLD package.
- If ANY import from a section is found → apply that entire section.
- Check each section independently — one section being N/A does NOT mean others are.

### Update Packages
- CHeck for the latest stable release of the target version 
  Example: for onecx/* we upgrade to ^7 so latest stable release of version 7.x.y

### Post‑Migration Import Corrections
- Strictly follow the source documentation for any component, service, or directive import changes.

## PrimeNG-Specific Migration Notes (v19 → v20)
- Query PrimeNG MCP tool `migrate_v19_to_v20` to detect component‑specific API and breaking changes.
- Fallback Fetch https://primeng.org/migration/v20 and extract **v19 → v20** breaking and component changes.
- For any PrimeNG‑related errors:
  1. Trace renamed, removed, or replaced APIs directly in `node_modules/primeng`.
  2. Use the PrimeNG MCP server for component information and compare APIs if needed.
  3. **DO NOT assume anything** — always verify via MCP tools, official docs, and source inspection.

## Nx / Angular Changes (18 → 19)

## Determine Stable Release (^6 vs ^7 Handling)
When documentation specifies a version range, **resolve to the latest STABLE release only**.
| Docs Says | Meaning | Resolution | Command |
|-----------|---------|------------|---------|
| `^20` | Latest 20.x | e.g. 20.2.1 | `npm view @angular/core dist-tags` |
| `^20.2` | Latest 20.2.x | e.g. 20.2.1 | Use latest patch in range |
| `~20.2.1` | Latest 20.2.x | e.g. 20.2.1 | Patch updates only |
| `20.2.1` | Exact version | 20.2.1 | Pin exactly |
| `>=20` | At least 20.0.0 | Latest 20.x | Use latest stable |
| `^7` (single digit) | Latest 7.x | Resolve via npm | `npm view <pkg> dist-tags` |
| (not specified) | Unknown | **ASK USER** | “Docs don’t specify version” |
- **NEVER** use the `@latest` tag (may resolve to beta/RC).
- **NEVER** use an unresolved caret in install commands.
- If a stable release cannot be confirmed → **SKIP the package** (keep current; do not break the build).

## Common Real‑World Patterns
These patterns were observed in real OneCX Angular 19 → 20 migrations:
- If any of the patterns below are found in the codebase, they **MUST be applied**.
- **Detection of a pattern makes it compulsory**, regardless of whether the migration guide explicitly calls it out.
```text
Pattern 1: CSS imports
  Old: @import '~@onecx/...';
  New: @import '@onecx/.../styles.scss';

Pattern 2: Permission mapping
  Common mistake: Mapping everything to #DELETE or #EDIT
  Correct mapping: #SEARCH, #IMPORT, #EXPORT, #EDIT as per action
```
If task relates to above patterns:
1. Search workspace for existing example
2. Copy pattern structure
3. Verify imports and paths match
4. Update MIGRATION_PROGRESS.md with reference
