# Source Verification

Use this reference when an exact API, behavior, dependency, or version claim needs pinned Miuix evidence. It can be the primary reference for an uncovered API or a version mismatch. It defines how abbreviated paths in this Skill map to the public Miuix repository and pins the current catalog evidence to the stable `v0.9.4` snapshot.

Read only the evidence workflow, API gate, path table, or supporting-internals entry required by the claim. Do not load the Example-app inventory during an ordinary API check.

Contents: [evidence workflow](#evidence-resolution-workflow) · [API verification gate](#api-verification-gate) · [snapshot scope](#snapshot-scope) · [path convention](#path-convention) · [supporting internals](#supporting-internals) · [Example app](#example-app)

## Evidence Resolution Workflow

1. Start from the exact declaration or evidence path required by the task. Use a component catalog or Example evidence only when the task does not already identify the symbol or integration point.
2. Expand abbreviated paths with the table below.
3. Read only the evidence required by the claim: choose the relevant docs, demo, Example section, or pinned source path; do not load all evidence types by default. The pinned source is required for an API/behavior contract, while docs, demos, and Example code are optional evidence for the specific claim.
4. For current catalog work, keep all evidence on commit `39c40f99844227b853f0049a0933b1f3ae6c00ba` / tag `v0.9.4`. Do not use the rolling rendered documentation site or generated Dokka pages for version-sensitive parameters. If the user explicitly requests the `v0.9.3 → v0.9.4` migration, follow the single migration reference and use its comparison source only for that task.
5. Return to the [Delivery Contract](../SKILL.md#delivery-contract) before presenting implementation or validation claims.

If the pinned web source cannot be reached, do not silently substitute memory, the rolling documentation site, or an unpinned snippet. State that API verification is blocked, or ask the user to provide the relevant tagged file, before making a version-sensitive claim.

## API verification gate

Use this gate whenever delivered code contains a Miuix symbol, dependency, or version-sensitive call. It is a correctness gate, not a component catalog:

1. **Pin the target.** Confirm the project's Miuix version, platform, and source set. Do not call a `v0.9.4` declaration verified for a different target version.
2. **Locate the declaration.** In the pinned source, confirm the public package/module, symbol name, overload, full parameter list, parameter order, required/optional parameters, defaults, and any experimental or platform opt-in.
3. **Match intended usage when needed.** Use the matching Markdown doc for intent or the demo for an isolated call only when that claim matters. Use Example code only to understand integrated composition; never use Example code as the API contract.
4. **Check behavior assumptions.** Confirm state/callback direction, required ancestor or host, insets, lifecycle, and disabled/dismissal behavior when they affect the call.
5. **Compile the affected target.** Run the target project's relevant source-set/module compilation after the change when the project is available. A source check without compilation is `source-verified`, not `compile-verified`.
6. **Report the boundary.** If compilation cannot run, state `source-verified; compilation pending` and do not describe the code as fully verified. If the pinned source or target version cannot be checked, state `API verification blocked` and stop the version-sensitive claim.

Use this compact record when the API risk is non-trivial:

```text
Miuix target:       version / platform / source set
Declaration:        pinned source path + symbol
Call contract:      import / overload / required state and callbacks
Host contract:      ancestor / inset / lifecycle requirement
Verification:       source-verified / compile-verified / pending
```

`references/api-contracts.md` is a maintained, source-verified subset of common public calls. Treat it as a copyable index into this gate, not as a second API authority: the pinned declaration wins, and every target version change invalidates the contract until it is rechecked.

## Snapshot scope

| Evidence role | Pinned snapshot |
|---|---|
| Current catalog and migration target | `v0.9.4` at commit `39c40f99844227b853f0049a0933b1f3ae6c00ba` |
| Migration comparison | `v0.9.3` tag, only through `references/release-v0.9.4.md` |

This table describes the Skill's maintained evidence scope. It is not a substitute for checking a target project's actual dependency version.

## Path Convention

All file paths in the tables below are relative to the Miuix repository root. Resolve them using GitHub URLs:

| Source | URL pattern |
|--------|------------|
| Read a version-pinned Markdown doc, demo, or source file | `https://raw.githubusercontent.com/compose-miuix-ui/miuix/39c40f99844227b853f0049a0933b1f3ae6c00ba/<file-path>` |
| Browse a **directory** | `https://github.com/compose-miuix-ui/miuix/tree/39c40f99844227b853f0049a0933b1f3ae6c00ba/<dir-path>` |

The repository Markdown file is the documentation evidence. The rendered site may represent a later commit and must not replace the pinned `docs/**/*.md` file during a version-sensitive task. The current Skill does not maintain standalone references for older Miuix versions.

| Abbreviation | Relative path |
|---|---|
| `miuix-ui/.../basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/` |
| `miuix-ui/.../anim/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/anim/` |
| `miuix-ui/.../color/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/color/` |
| `miuix-ui/.../interfaces/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/interfaces/` |
| `miuix-ui/.../icon/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/` |
| `miuix-ui/.../overlay/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/` |
| `miuix-ui/.../window/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/window/` |
| `miuix-ui/.../layout/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/layout/` |
| `miuix-ui/.../theme/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/` |
| `miuix-ui/.../utils/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/utils/` |
| `miuix-blur/.../blur/` | `miuix-blur/src/commonMain/kotlin/top/yukonga/miuix/kmp/blur/` |
| `miuix-ui/.../icon/basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/basic/` |
| `miuix-preference/.../preference/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/` |
| `miuix-preference/.../menu/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/menu/` |
| `miuix-preference/.../popup/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/popup/` |
| `miuix-core/.../icon/` | `miuix-core/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/` |
| `miuix-icons/.../extended/` | `miuix-icons/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/extended/` |
| `miuix-shader/.../shader/` | `miuix-shader/src/commonMain/kotlin/top/yukonga/miuix/kmp/shader/` |
| `miuix-squircle/.../squircle/` | `miuix-squircle/src/commonMain/kotlin/top/yukonga/miuix/kmp/squircle/` |
| `miuix-nav/.../core/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/core/` |
| `miuix-nav/.../gesture/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/gesture/` |
| `miuix-nav/.../runtime/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/runtime/` |
| `miuix-nav/.../state/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/state/` |
| `miuix-nav/.../transition/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/transition/` |
| `docs/components/` | `docs/components/` |
| `docs/demo/` | `docs/demo/src/commonMain/kotlin/` |
| `example/shared/.../` | `example/shared/src/commonMain/kotlin/` |
| `example/shared/.../navigation/` | `example/shared/src/commonMain/kotlin/navigation/` |

## Supporting Internals

These files underpin multiple components. They're read-on-demand, not listed per component above:

| File | Role |
|------|------|
| `miuix-ui/.../basic/Component.kt` | `BasicComponent` — universal base for all preference and overlay components |
| `miuix-ui/.../utils/MiuixPopupUtils.kt` | Popup position/anchor utilities for all overlay and window popups |
| `miuix-ui/.../utils/WindowNavigationEventScope.kt` | Rebinds predictive/system back handling to a focused platform window |
| `miuix-ui/.../layout/DialogContentLayout.kt` | Shared dialog content layout, including `maxWidth`, large-screen mode, and corner-radius resolution |
| `miuix-ui/.../layout/BottomSheetContentLayout.kt` | Shared bottom sheet content layout |
| `miuix-ui/.../layout/ListPopupLayout.kt` | Shared list popup content layout |
| `miuix-ui/.../layout/CascadingListPopupLayout.kt` | Shared cascading list popup layout |
| `miuix-ui/.../layout/CascadingMorphContent.kt` | Shared cascading morph content |
| `miuix-ui/.../layout/MorphHeaderRow.kt` | Shared morph header row |
| `miuix-preference/.../popup/DropdownEntriesContent.kt` | Internal dropdown entries content |
| `miuix-ui/.../basic/Dropdown.kt` | `DropdownImpl` and `SpinnerItemImpl` — internal dropdown/spinner building blocks used by preference selectors |
| `miuix-ui/.../basic/ListPopup.kt` | `ListPopupContent`, `ListPopupColumn`, `rememberListPopupLayoutInfo` — internal list popup building blocks |
| `miuix-ui/.../basic/PullToRefresh.kt` | `PullToRefreshState` threshold/progress state machine and real-gesture gating |
| `miuix-ui/.../basic/TopAppBar.kt` | `SmallTopAppBar` pinned-state handling and `TopAppBarState.Saver` |
| `miuix-blur/.../TextureEffect.kt` | Uniform and progressive texture-blur modifier overloads |
| `miuix-blur/.../ProgressiveBlur.kt` | `ProgressiveBlur` descriptor and edge-fade presets |
| `miuix-blur/.../BackdropEffects.kt` | Custom backdrop effect-block APIs, including progressive blur |
| `miuix-ui/.../utils/Overscroll.kt` | Modifier overscroll API, real-gesture gating, and stale-offset recovery |
| `miuix-ui/.../utils/OverscrollFactory.kt` | Theme-level `MiuixOverscrollFactory` / `MiuixOverscrollEffect` behavior |
| `miuix-ui/.../utils/PagerGestureUtils.kt` | Horizontal pager conflict modes, nested-list interception, and spring page navigation |
| `miuix-nav/.../core/LocalNavTransitionScope.kt` | Entry-local live transition scope CompositionLocal |
| `miuix-nav/.../state/NavEntryViewModel.kt` | Entry ViewModel stores and saved-state creation context |

## Example App

`example/` is a full Compose Multiplatform app built with Miuix. Use it to study integrated composition after selecting components; use `docs/demo/` for isolated component calls.

| Path | What's inside |
|------|---------------|
| `docs/demo/src/commonMain/kotlin/<Component>Demo.kt` | Minimal, isolated usage for one component |
| `example/shared/.../App.kt`, `AppState.kt`, `ui/Theme.kt` | Application theme, theme inputs, and cross-tree state setup |
| `example/shared/.../AppContent.kt` | Adaptive app shell, compact/wide navigation, and shared controls |
| `example/shared/.../MainPage.kt` | Integrated home page with Scaffold, app bar actions, search, scrolling sections, and popup menus |
| `example/shared/.../SettingsPage.kt` | Integrated settings page with titled Cards, Preference groups, dependent settings, and adaptive padding |
| `example/shared/.../component/` | `LazyListScope` sections and popup examples composed into `MainPage`; these are sections, not one screen per component |
| `example/shared/.../utils/PageUtils.kt` | Example-specific adaptive app bar, insets, scroll, haptic, and blur helpers |

Use the `miuix-guidelines` Skill only when translating integrated Example composition into application code. Reuse repeated hierarchy and state patterns there, but verify APIs in this Skill's pinned source and do not treat Example-specific helpers as public Miuix requirements.
