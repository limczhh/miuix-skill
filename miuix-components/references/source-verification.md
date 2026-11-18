# Source Verification

Use for an uncovered beta01 API, an ambiguous overload/behavior, or a target source override. Covered standard-beta01 calls use the maintained contract directly; they do not require another source download. This file defines escalation and evidence boundaries, not an obligatory extra stop for every call.

Contents: [covered path](#covered-contract-path) · [acquire source](#acquire-source-evidence) · [source gate](#api-verification-gate) · [labels](#verification-labels) · [snapshot](#snapshot-scope) · [paths](#path-convention) · [internals](#supporting-internals) · [Example](#example-app).

## Covered-contract path

Confirm the target resolves `0.9.5-beta01`, its platform/source set, and whether substitution/local changes affect the needed API. Reuse this context until build inputs or source change. A declaration-version string alone does not establish resolution.

When the needed call and host/state behavior are covered and no override or contradiction affects them, use that contract as **catalog-verified**. Do not reread source merely to obtain a stronger label. Compile an implementation in the available target; report pending resolution or compilation honestly. Without a target project, the pinned catalog still supports a labeled example.

Only this version is maintained. Another release is a scope mismatch; dependency upgrades require task authorization. A local beta01 customization needs the source gate below for the affected symbols, not an alternate multi-version catalog.

## Acquire source evidence

Use a target artifact's attached source for a customized target claim. For catalog gaps, use the pinned commit `6d2554a850b8960e2616c968263ff64beb5ffb03`: inspect a matching clean checkout, use `git show <commit>:<path>` when the checkout has moved, or open the [pinned raw URL](#path-convention). Do not replace it with rolling rendered docs, untagged snippets, or remembered Material APIs.

Read one declaration and the implementation needed for the uncertain behavior. Docs/Demo may explain intent; Example may explain integration. None is declaration authority. If source cannot be reached, keep covered catalog facts usable, but leave the uncovered or customized claim unresolved. `API verification blocked` applies only to that missing evidence, not the whole task.

## API verification gate

For a gap or customized source:

1. Identify the actual coordinate/source, source set and relevant checkout changes. Keep its deviations separate from the pinned catalog.
2. Verify public package/module, overload, parameters/defaults, required inputs and opt-in for the call. Never merge parameters from different overloads.
3. Inspect host, callback/state direction, insets, dismissal or input behavior only where they affect the task. Refer to docs/Demo/Example only for a separate unresolved usage question.
4. Write the smallest matching call and compile the affected target when available. Source inspection alone does not prove compilation or runtime behavior.

For a complex investigation, record only the necessary facts: resolved source, symbol/path, affected contract and evidence level. Return the resulting call/behavior using the [delivery contract](../SKILL.md#delivery-contract); omit a routine search transcript.

## Verification labels

| Label | Evidence required | Does not prove |
|---|---|---|
| `catalog-verified` | Maintained declaration/behavior at the pinned beta01 commit; reusing its contract is sufficient | Target customization, successful compilation or runtime behavior |
| `target-source-verified` | The affected declaration/behavior in the target's actual resolved source, including relevant substitutions or changes, was inspected | Compilation or device behavior |
| `compile-verified` | The affected target compiled with the delivered call | Visual, input, platform or performance outcomes |
| `runtime-verified` | The named runtime/device behavior was exercised | Unexercised configurations or platforms |
| `API verification blocked` | Required evidence for an uncovered/customized claim cannot be accessed | That a guessed API is usable |

Do not label a reused catalog contract target-source-verified, and do not fetch source solely to change the label. Reuse established evidence until its source/dependency inputs change.

## Snapshot scope

| Evidence role | Pinned snapshot |
|---|---|
| Current catalog and source-audit target | release tag `v0.9.5-beta01` at commit `6d2554a850b8960e2616c968263ff64beb5ffb03` (published prerelease) |

This table describes the Skill's maintained evidence scope. It is not a substitute for checking a target project's actual dependency version.

## Path Convention

All file paths in the tables below are relative to the Miuix repository root. Resolve them using GitHub URLs:

| Source | URL pattern |
|--------|------------|
| Read a version-pinned Markdown doc, demo, or source file | `https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/<file-path>` |
| Browse a **directory** | `https://github.com/compose-miuix-ui/miuix/tree/6d2554a850b8960e2616c968263ff64beb5ffb03/<dir-path>` |

The repository Markdown file is the documentation evidence. The rendered site may represent a later commit and must not replace the pinned `docs/**/*.md` file during a version-sensitive task. Only the pinned beta01 contract is maintained; no historical-release references are bundled.

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
| `miuix-material/.../material/` | `miuix-material/src/commonMain/kotlin/top/yukonga/miuix/kmp/material/` |
| `miuix-glass/.../glass/` | `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/` |
| `miuix-glass-icons/.../icon/glass/` | `miuix-glass-icons/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/glass/` |
| `miuix-window/.../window/` | `miuix-window/src/desktopMain/kotlin/top/yukonga/miuix/kmp/window/` (Desktop JVM only) |
| `miuix-squircle/.../squircle/` | `miuix-squircle/src/commonMain/kotlin/top/yukonga/miuix/kmp/squircle/` |
| `miuix-nav/.../core/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/core/` |
| `miuix-nav/.../gesture/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/gesture/` |
| `miuix-nav/.../runtime/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/runtime/` |
| `miuix-nav/.../state/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/state/` |
| `miuix-nav/.../transition/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/transition/` |
| `docs/components/basic/` | `docs/components/basic/` |
| `docs/components/extended/` | `docs/components/extended/` |
| `docs/components/glass/` | `docs/components/glass/` |
| `docs/guide/` | `docs/guide/` |
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
| `miuix-blur/.../blur/TextureEffect.kt` | Uniform and progressive texture-blur modifier overloads |
| `miuix-blur/.../blur/ProgressiveBlur.kt` | `ProgressiveBlur` descriptor and edge-fade presets |
| `miuix-blur/.../blur/BackdropEffects.kt` | Custom backdrop effect-block APIs, including progressive blur |
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
