# Source Verification and Example Map

Use only when pinned docs lack a needed declaration, source is locally modified/overridden, or Kotlin implementation details decide runtime behavior (`0.9.5-rc02` at `e636bd6c07f14d588fe084f0c3a5b57eafe20230`).

Contents: [escalation gate](#api-verification-gate) · [evidence labels](#verification-labels) · [supporting source](#supporting-source-index) · [Example app](#example-app-map)

## API Verification Gate

1. **Docs first**: Check [Catalog](catalog.md) and read the component's official `docs/...` Markdown before touching Kotlin source. Reuse verified evidence while target build inputs stay unchanged.
2. **Escalate selectively**: Inspect only the conflicting/missing Kotlin declaration and required implementation branch via pinned raw URL, `git show v0.9.5-rc02:<path>`, or attached `-sources.jar` (prefer the target's actual resolved source if customized/substituted). Do not reread whole modules or switch to unpinned `main`.
3. **Check 4 invariants**: Public module/package & source set -> exact overload parameters/defaults & opt-ins -> host/insets/lifecycle/back behavior -> compile affected target when available.

## Verification Labels

| Label | Meaning | Does NOT prove |
|---|---|---|
| `catalog-verified` | Maintained fact checked against pinned `rc02` doc/source | Unlisted params, custom target overrides, compilation, runtime |
| `target-source-verified` | Inspected in the target project's actual resolved source | Compilation or device behavior |
| `compile-verified` | Affected target compiled with the exact call | Visual/gesture/platform rendering |
| `runtime-verified` | Exercised on the target platform/runtime | Unexercised themes/screen sizes/platforms |
| `API verification blocked` | Source/artifact unreachable for an unverified symbol | That a guessed API exists |

## Supporting Source Index

`*Impl` does not imply `internal`; check declaration visibility. Common low-level source areas:

| Path | Public / Supporting Role |
|---|---|
| `miuix-ui/.../basic/Component.kt` | `BasicComponent` row base used by preference & menu rows |
| `miuix-ui/.../basic/SearchClearButton.kt` | Public search clear icon button (`SearchBar`, `GlassSearchOverlay`) |
| `miuix-ui/.../basic/Dropdown.kt`, `ListPopup.kt` | Public `DropdownImpl`, `SpinnerItemImpl`, `ListPopupContent`, `ListPopupColumn` (`rememberListPopupLayoutInfo` became `internal` in `rc02`) |
| `miuix-ui/.../basic/TopAppBar.kt`, `PullToRefresh.kt` | `TopAppBarLayout`, `TopAppBarState`, `PullToRefreshState` |
| `miuix-ui/.../overlay/`, `.../window/WindowComponentHost.kt` | Host wrappers, `WindowComponentFrame` / `LocalWindowComponentFrame` |
| `miuix-ui/.../layout/` | `DialogContentLayout.kt`, `DialogMotion.kt`, `BottomSheetContentLayout.kt` (exports `BottomSheetOverlayLayout`), `SheetMotion.kt`, `ListPopupLayout.kt`, `Cascading*` |
| `miuix-ui/.../utils/` | `MiuixPopupUtils.kt`, `WindowNavigationEventScope.kt`, `Overscroll.kt`, `OverscrollFactory.kt`, `PagerGestureUtils.kt`, `MiuixIndication.kt` |
| `miuix-blur/.../blur/`, `miuix-material/.../material/`, `miuix-squircle/.../squircle/` | Effect pipelines, shaders, caches (`internal/` subpackages are non-public) |
| `miuix-preference/.../popup/` | Lower-level dropdown popups/dialogs & `DropdownEntriesContent.kt` (internal) |
| `miuix-nav/.../core/LocalNavTransitionScope.kt`, `.../transition/NavTransitionSource.kt`, `NavTransitionAnchor.kt`, `.../state/NavEntryViewModel.kt` | `LocalNavTransitionScope`, route-bound `navTransitionSource`, `NavTransitionCorners`, entry `SavedStateHandle`/ViewModel stores |

## Example App Map

`example/` demonstrates integrated composition (helpers are app-specific, **not** mandatory public Miuix APIs); `docs/demo/` provides minimal isolated calls:

| Path | Role |
|---|---|
| `docs/demo/src/commonMain/kotlin/<Component>Demo.kt` | Minimal single-component demo |
| `example/shared/src/commonMain/kotlin/App.kt`, `AppState.kt`, `ui/Theme.kt` | Root theme, state & cross-tree setup |
| `example/shared/src/commonMain/kotlin/AppContent.kt` | Adaptive shell (compact/wide nav, pager destinations) |
| `example/shared/src/commonMain/kotlin/navigation/Route.kt`, `Navigator.kt` | Typed route keys & single stack mutation owner |
| `example/shared/src/commonMain/kotlin/MainPage.kt`, `SettingsPage.kt`, `IconPage.kt` | Integrated pages (tabs, search, `ScrollBar`, `GlassIconDropdownMenu`, settings) |
| `example/shared/src/commonMain/kotlin/component/`, `utils/PageUtils.kt` | Example sections & app-specific adaptive/inset/scroll helpers |
