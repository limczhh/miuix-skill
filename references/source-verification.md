# Source Verification

Use this reference after another catalog or workflow identifies an evidence path. It defines how every abbreviated path in this Skill maps to the public Miuix repository and pins the current catalog evidence to the `0.9.4-rc01` candidate snapshot.

Contents: [evidence workflow](#evidence-resolution-workflow) · [snapshot scope](#snapshot-scope) · [path convention](#path-convention) · [supporting internals](#supporting-internals) · [Example app](#example-app)

## Evidence Resolution Workflow

1. Start from the component catalog, [usage-pattern evidence levels](usage-patterns.md#evidence-levels), or the exact file named by another reference.
2. Expand abbreviated paths with the table below.
3. Read the relevant `docs/**/*.md` file from the pinned snapshot for intent, the matching `docs/demo/` file for an isolated call, the closest `example/shared/` section for integration, and source for the API/behavior contract. Do not read every document or source file unless the task is a release audit.
4. For current catalog work, keep all evidence on commit `4a6b750b` / tag `v0.9.4-rc01`. The upstream repository has not created a stable `v0.9.4` tag, so do not present this snapshot as a stable release. Do not use the rolling rendered documentation site or generated Dokka pages for version-sensitive parameters. If the target project uses another version, state that mismatch and verify that version separately.
5. Return to the [Code Delivery Contract](../SKILL.md#code-delivery-contract) before presenting implementation or validation claims.

If the pinned web source cannot be reached, do not silently substitute memory, the rolling documentation site, or an unpinned snippet. State that API verification is blocked, or ask the user to provide the relevant tagged file, before making a version-sensitive claim.

## Snapshot scope

| Evidence role | Pinned snapshot |
|---|---|
| Current catalog and migration target | `v0.9.4-rc01` at commit `4a6b750b578880146876e4ab77097d9b01702413` |
| Historical comparison | `v0.9.3` tag, only for tasks that target or compare that release |
| Other versions | Resolve the target project's exact tag or commit before making version-sensitive claims |

This table describes the Skill's maintained evidence scope. It is not a substitute for checking a target project's actual dependency version.

## Path Convention

All file paths in the tables below are relative to the Miuix repository root. Resolve them using GitHub URLs:

| Source | URL pattern |
|--------|------------|
| Read a version-pinned Markdown doc, demo, or source file | `https://raw.githubusercontent.com/compose-miuix-ui/miuix/4a6b750b578880146876e4ab77097d9b01702413/<file-path>` |
| Browse a **directory** | `https://github.com/compose-miuix-ui/miuix/tree/4a6b750b578880146876e4ab77097d9b01702413/<dir-path>` |

The repository Markdown file is the documentation evidence. The rendered site may represent a later commit and must not replace the pinned `docs/**/*.md` file during a candidate-snapshot task. For historical `v0.9.3` work, substitute the `v0.9.3` tag explicitly.

| Abbreviation | Relative path |
|---|---|
| `miuix-ui/.../basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/` |
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

Read [Example-derived usage patterns](usage-patterns.md) before translating these files into application code. Reuse repeated hierarchy and state patterns, but verify APIs in source and do not treat Example-specific helpers as public Miuix requirements.
