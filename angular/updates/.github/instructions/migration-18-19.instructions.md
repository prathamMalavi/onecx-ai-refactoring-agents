---
name: Angular 18 to 19 Migration Data
description: Version-specific migration URLs, version mappings, known issues, and special rules for Angular 18 to 19
---

# Angular 18 → 19 Migration Data

<!--
  This file contains ALL version-specific data for Angular 18 → 19.
  It is NOT auto-injected (no applyTo). Agents read it on-demand.
  To support a new migration: copy this file, rename, fill in new data.
-->

## Documentation Sources
  
### OneCX Docs MCP
- **available**: true
- **Mcp tool**: about_onecx
- **start-query**: "Angular 19 migration"
- **fallback-url**: https://onecx.github.io/docs/documentation/current/onecx-portal-ui-libs/migrations/angular-19/index.html

### PrimeNG MCP
- **available**: true
- **Mcp tool**: primeng
- **start-query**: "PrimeNG migration guide"
- **migration-tools**: https://v18.primeng.org/guides/migration + migrate_v18_to_v19
- **fallback-url**: https://v18.primeng.org/guides/migration + https://primeng.org/migration/v19

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

### Phase B Defaults
- **Default phase B ownership**: assistant
- **Default approval response**: Yes (proceed with upgrade)

## Target Versions
- @angular/core: ^19.x.x
- primeng: ^19.x.x
- nx: ^20.x.x (if applicable)
- typescript: ~5.x.x
- Node.js minimum: 20.x

## PrimeNG Intermediate Guides
<!-- 
  IMPORTANT: Use PrimeNG MCP tools FIRST for migration data:
  - migrate_v18_to_v19 (for v18→v19 migration steps)
  - primeng_get_migration_guide (general migration info)
  Only fall back to URLs if MCP is unavailable or returns no data specialy for version v18.
  Note: https://primeng.org/migration/v18 returns 404 — use subdomain URL instead.
-->
- v18 guide: https://v18.primeng.org/guides/migration (PrimeNG v18 migration page — subdomain URL)
- v19 guide: use primeng MCP tool `migrate_v18_to_v19`
- v19 guide: https://primeng.org/migration/v19

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


## Special Migration Rules (Angular 18 → 19 Specific)
These rules apply specifically to the Angular 18 → 19 migration path:
- **Styles handling**: Apply styles.scss changes exactly as documented. If conflict between Nx styles array and Sass @import → STOP and ask which pattern to use
- **Standalone components**: If error "Component is standalone, and cannot be declared in an NgModule" → add `standalone: false` and document why
- **Angular version caret**: MUST keep caret (`^`) in package.json for `@angular/*` packages — module federation requires compatible version ranges for sharing
- **package-lock.json**: Delete and regenerate with `npm install` at major transition points (after Phase A packages, after Phase C packages) — prevents stale lock file conflicts

## Task-Specific Applicability Rules
These rules MUST be followed when determining whether a task applies. The docs describe WHAT to do — these rules tell you HOW to check applicability. For EVERY task, independently search the ENTIRE codebase for ALL patterns listed below.


### Adjust Standalone Mode
- `<ocx-portal-viewport>` is **NOT** provided by `@onecx/standalone-shell`.  
  It originates from a different library and is **removed in v6**.
- The **only valid replacement** for `<ocx-portal-viewport>` is  
  `<ocx-standalone-shell-viewport>` from `@onecx/angular-standalone-shell`.
- **Applicability rule**:
  - Search **ALL** `.html` files for `<ocx-portal-viewport>`.
  - If found → **this task applies**, regardless of whether `@onecx/standalone-shell` exists in `package.json`.
- **DO NOT** skip this task simply because `@onecx/standalone-shell` is absent from `package.json`.
- **Known incompatibility / phase rule**:
  - `@onecx/angular-standalone-shell` has **no v5‑compatible version**.
  - First stable release is **v6+**, requiring `@angular/core: ^19.0.0`.
  - If the migration guide does **not** explicitly assign a phase, the executor **MUST** check peer dependencies per the **No‑Defer Rule**.
  - This condition **forces reclassification to Phase C**.

### Update Component Imports
- Strictly follow the source documentation for any component, service, or directive import changes.
- For **EACH** section in the documentation, search all `.ts` files for imports of the listed component, service, or directive names **from the OLD package**.
- If **ANY** import from a section is found → **apply the entire section**.
- Evaluate each section **independently**; one section being N/A does **NOT** exempt others.

### Remove MenuService
- Search all `.ts` files for `MenuService` imports from `@onecx/portal-integration-angular`.
- Remove both **imports** and any **constructor or property references**.

### Provide ThemeConfig
- Applies **ONLY** if `primeng` is listed as a dependency in `package.json`.
- Add `provideThemeConfig()` from `@onecx/angular-accelerator` to the **providers** array.
- If applicable, apply this change to **ALL** `@NgModule` files **AND** all `bootstrapRemoteComponent` files.

### Replace BASE_URL
- Search all `.ts` files for:
  - `BASE_URL` imports from `@onecx/angular-remote-components`
  - `bootstrapRemoteComponent(` usage
- Apply the replacement wherever found.

### Remove `@onecx/portal-layout-styles`
- For the **Expose styles.css** task:
  - If `nx.json` or `workspace.json` exists → treat as **Nx workspace**
  - Otherwise → treat as **Angular CLI**
- Apply the configuration approach that matches the workspace type.

### Update Theme Service
- Documentation instructs to remove `ThemeService.apply()` calls and also provides a custom `apply()` implementation.
- This is **NOT** contradictory:
  - Remove all `ThemeService.apply()` calls.
  - **ONLY IF** temporary theme previews are required, implement the provided custom `apply()` function as a replacement.


### Update Translations
- Check the official OneCX documentation for translation‑related changes and replacements.
- For each example pattern shown in the docs, search the codebase, analyze usage, and apply the required replacement.
- **Remove `translateServiceInitializer`** from providers and replace it with:
  `provideTranslationPathFromMeta(import.meta.url, 'assets/i18n/')`
  from `@onecx/angular-utils` (**MANDATORY**).
- Remove the following provider configuration wherever it exists:
```ts
{
  provide: APP_INITIALIZER,
  useFactory: translateServiceInitializer,
  multi: true,
  deps: [UserService, TranslateService]
}
```

### Replace BASE_URL / REMOTE_COMPONENT_CONFIG
- If `REMOTE_COMPONENT_CONFIG` provider exists in both `bootstrap.ts` and `component.ts`, keep it ONLY in `bootstrap.ts`. Remove from `component.ts` to avoid duplication.

### Post‑Migration Import Corrections
- Strictly follow the source documentation for any component, service, or directive import changes.
- **MANDATORY**: Add to providers:
  - `provideAnimations()` from `@angular/platform-browser/animations`
  - `provideAngularUtils()` from `@onecx/angular-utils`
  - `provideTranslationConnectionService()` from `@onecx/angular-utils`
- **REQUIRED**: Remove **all** imports from:
  - `@onecx/portal-integration-angular`
  - `@onecx/onecx/portal-layout-styles`
- Update component, service, and directive imports accordingly.
- If errors occur, search **only** within `node_modules/@onecx`.
- Follow official OneCX documentation for replacements.
- **STRICT**:
  - `<ocx-data-loading-error>` is **deprecated with NO replacement**.
  - **DO NOT replace it** with `<ocx-error>` or any other component.
- After v6 upgrade (`@onecx/portal-integration-angular` removed):
  - `AppStateService`, `ConfigurationService`, `UserService`,
    `PortalMessageService`, `APP_CONFIG`, `CONFIG_KEY`
    → `@onecx/angular-integration-interface`
  - `PortalPageComponent`, `providePermissionService`,
    `provideTranslationConnectionService`,
    `MultiLanguageMissingTranslationHandler`
    → `@onecx/angular-utils`
  - `ExportDataService`, `PortalDialogService`,
    `providePortalDialogService`
    → `@onecx/angular-accelerator`
  - `translateServiceInitializer` → **removed**, use `provideTranslationConnectionService`
  - `PortalMissingTranslationHandler` → **removed**, use `MultiLanguageMissingTranslationHandler`
  - `PortalCoreModule` → **removed**, use `AngularAcceleratorModule`

## PrimeNG‑Specific Migration Instructions (v17 → v19)
These are **instructions**, not an exhaustive list.  
Items below are **examples only** to illustrate common changes and must **not** be treated as complete or authoritative.
- Fetch https://v18.primeng.org/guides/migration and extract **v17 → v18** breaking and component changes.
- Query PrimeNG MCP tool `migrate_v18_to_v19` to detect component‑specific API and breaking changes.
- For any PrimeNG‑related errors:
  1. Trace renamed, removed, or replaced APIs directly in `node_modules/primeng`.
  2. Use the PrimeNG MCP server for component information and compare APIs if needed.
  3. **DO NOT assume anything** — always verify via MCP tools, official docs, and source inspection.

Examples of known PrimeNG breaking changes (NOT complete — always check additional changes from official sources and MCP tools):
- **Renamed components** (template tag changes), e.g.:
  - `<p-calendar>` → `<p-datepicker>`
  - `<p-dropdown>` → `<p-select>`
  - `<p-inputSwitch>` → `<p-toggleswitch>`
  - `<p-overlayPanel>` → `<p-popover>`
  - `<p-sidebar>` → `<p-drawer>`
  - `<p-messages>` → `<p-message>` (plural → singular)
- **Removed / replaced APIs**, e.g.:
  - `TriStateCheckbox`, `DataViewLayoutOptions`, `PrimeNGConfig` → removed
  - `pAnimate` directive → removed, use `pAnimateOnScroll`
  - `Message` interface → renamed to `ToastMessageOptions` in `ToastService`
  - Replace all `<span class="p-float-label">...</span>` with `<p-floatlabel variant="on">...</p-floatlabel>`.
- **Styling changes**, e.g.:
  - `.p-fluid` class removed → use `fluid` input on components
  - `.p-highlight` class renamed → update custom styles
- **Compatibility**, e.g.:
  - PrimeFlex 3.x is not compatible with PrimeNG 19 → upgrade to PrimeFlex 4.x
- **Module / API changes**, e.g.:
  - `InputTextareaModule` → `TextareaModule`
  - `p-checkbox [label]` removed → use a separate `<label>` element
  - Verify whether modules such as `CheckboxModule`, `ButtonModule`, `MessageModule`, `BadgeModule`, `SelectModule`, `FloatLabelModule`, and similar modules are required; if so, add them to shared module imports/exports (application‑specific).

## Nx / Angular Changes (18 → 19)
- Angular 19 requires **TypeScript >= ~5.7.x**.
  - If current TypeScript is ~5.5.x or lower → upgrade is mandatory.
- Determine workspace type:
  - If `nx.json` exists → **Nx workspace**
  - Otherwise → **Angular CLI workspace**
- **Nx workspace only**:
  - Use the recommended upgrade command:
    `nx migrate 20.4.0 --interactive`
  - Run migrations incrementally and follow Nx prompts.
- **Angular CLI projects**:
  - Do NOT run Nx migrate commands.
  - Upgrade Angular and dependencies via documented Angular steps only.

## Determine Stable Release (^5 vs ^6 Handling)
When documentation specifies a version range, **resolve to the latest STABLE release only**.
| Docs Says | Meaning | Resolution | Command |
|-----------|---------|------------|---------|
| `^19` | Latest 19.x | e.g. 19.2.1 | `npm view @angular/core dist-tags` |
| `^19.2` | Latest 19.2.x | e.g. 19.2.1 | Use latest patch in range |
| `~19.2.1` | Latest 19.2.x | e.g. 19.2.1 | Patch updates only |
| `19.2.1` | Exact version | 19.2.1 | Pin exactly |
| `>=19` | At least 19.0.0 | Latest 19.x | Use latest stable |
| `^6` (single digit) | Latest 6.x | Resolve via npm | `npm view <pkg> dist-tags` |
| (not specified) | Unknown | **ASK USER** | “Docs don’t specify version” |
- **NEVER** use the `@latest` tag (may resolve to beta/RC).
- **NEVER** use an unresolved caret in install commands.
- If a stable release cannot be confirmed → **SKIP the package** (keep current; do not break the build).

## Common Real‑World Patterns
These patterns were observed in real OneCX Angular 18 → 19 migrations:
- If any of the patterns below are found in the codebase, they **MUST be applied**.
- **Detection of a pattern makes it compulsory**, regardless of whether the migration guide explicitly calls it out.
```text
Pattern 1: Component with DataView 
  Old: <p-table> + <data-view-controls> or <p-dataView> + <data-view-controls>
  New: <ocx-interactive-data-view> with [actionColumnPosition]="'left'"

Pattern 2: CSS imports
  Old: @import '~@onecx/...';
  New: @import '@onecx/.../styles.scss';

Pattern 3: Permission mapping
  Common mistake: Mapping everything to #DELETE or #EDIT
  Correct mapping: #SEARCH, #IMPORT, #EXPORT, #EDIT as per action
```
If task relates to above patterns:
1. Search workspace for existing example
2. Copy pattern structure
3. Verify imports and paths match
4. Update MIGRATION_PROGRESS.md with reference
