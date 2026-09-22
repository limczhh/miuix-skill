# v0.9.3 → v0.9.4 Migration Notes

This is the only version-migration reference maintained by this Skill. It
describes the changes a project must make when moving from `v0.9.3` to the
stable `v0.9.4` contract. For new code and normal API questions, use the
current `v0.9.4` guidance in `SKILL.md` and the component references; do not
apply this document as a substitute for current source verification.

Contents: [baseline](#dependency-and-platform-baseline) · [navigation migration](#1-navigation-module-and-route-migration) · [new public content](#2-new-public-content) · [breaking changes](#3-breaking-and-removed-content) · [behavior changes](#4-behavior-changes) · [tooling](#5-tooling-baseline) · [checklist](#migration-checklist) · [evidence](#evidence)

## Dependency and platform baseline

```kotlin
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.4")
implementation("top.yukonga.miuix.kmp:miuix-preference:0.9.4") // optional
implementation("top.yukonga.miuix.kmp:miuix-icons:0.9.4") // optional
implementation("top.yukonga.miuix.kmp:miuix-blur:0.9.4") // optional; Android requires API 33
implementation("top.yukonga.miuix.kmp:miuix-nav:0.9.4") // optional
```

- Shared Android artifacts require `minSdk = 24` instead of 23.
- `miuix-blur-android` remains a separate API-33 target; do not add it to a
  lower-minSdk application without an explicit platform decision.
- The stable build uses Compose Multiplatform `1.12.0`, Kotlin `2.4.20`, AGP
  `9.4.1`, Lifecycle `2.11.0`, and the Gradle `9.7.1` wrapper.
- The new navigation runtime uses Lifecycle/ViewModel, `kotlinx.serialization`,
  immutable collections, Compose Foundation, and `androidx.navigationevent`.

## 1. Navigation module and route migration

| `v0.9.3` | `v0.9.4` | Migration |
|---|---|---|
| `miuix-navigation3-ui` | `miuix-nav` | Replace the dependency and migrate imports/API together |
| `androidx.navigation3.*` | `top.yukonga.miuix.kmp.nav.*` | Replace the old scene model with Miuix's runtime |
| `docs/guide/navigation3.md` | `docs/guide/miuix-nav.md` | Use the stable Miuix navigation guide |
| Example `navigation3/` path | Example `navigation/` path | Follow the new route/entry structure |

This is not an artifact-only rename. The old `Scene`, `SceneStrategy`,
`OverlayScene`, `DialogSceneStrategy`, scene decorators, `rememberSceneState`,
and old `NavDisplay` overloads are gone. The stable runtime uses `NavKey`,
`rememberNavBackStack`, `NavDisplay`, `NavController`, and the `entry<T> { ... }`
DSL.

`@Serializable` is required for every route key in a remembered saveable stack.
If a route cannot be serialized, use the in-memory stack and do not claim
process-death restoration. The v1 runtime remains a flat stack: it has no
built-in dialog/bottom-sheet scene model, shared-element scene strategy, KSP
route registry, or `navigateForResult`/`setResult` channel.

## 2. New public content

### Breadcrumbs, blur, and window back handling

- `BreadcrumbBar`, `BreadcrumbItem`, `BreadcrumbBarDefaults`,
  `BreadcrumbBarColors`, and `List<BreadcrumbItem>.joinToPath()` provide
  horizontally scrollable path navigation.
- `miuix-blur` adds `ProgressiveBlur`, directional `Top`/`Bottom`/`Left`/`Right`
  presets, `Modifier.progressiveTextureBlur(...)`, and progressive APIs for
  custom `drawBackdrop` pipelines. Use the same gradient in the effect and
  `drawBackdrop(progressiveGradient = ...)` paths.
- `WindowNavigationEventScope` lets separate-window hosts bind predictive/system
  back handling to the focused platform window. Miuix window dialogs, sheets,
  and list popups apply it automatically.

### Navigation and transition state

- `NavigationBarItem` and `FloatingNavigationBarItem` accept a shared
  `NavigationBarItemColors` value from
  `NavigationBarDefaults.navigationBarItemColors(...)`. State opacity is
  applied to the supplied alpha; `Color.Transparent` remains transparent.
- `LocalNavTransitionScope` is provided inside each `NavDisplay` entry.
  `NavTransitionScope.isRunning` is the composition-safe transition lifecycle
  signal; read `relativeDepth`, `gesture`, and `settle` in deferred graphics
  layers for frame-level effects.
- Entry ViewModels inherit the parent creation extras and factory and use the
  surrounding `SavedStateRegistryOwner` when present, enabling
  `SavedStateHandle` and integrations such as Hilt without custom owner wiring.

### Pager gesture utilities

`miuix-ui` adds `PagerGestureUtils.kt`:

- `PagerInterceptionMode` (`Native`, `CrossAxisInterceptor`, `TapToHalt`)
- `PagerNavigationSpringSpec` and `PagerState.springAnimateToPage(...)`
- `PagerGestureNestedScrollConnection`
- `Modifier.horizontalPagerSwipeOverride(...)`
- `PagerFlingTrackerConnection` and `Modifier.iosStyleMomentumHalt(...)`
- composable and non-composable `Modifier.pagerGestureOverride(...)` overloads

For `CrossAxisInterceptor`, set
`userScrollEnabled = false` and
`pageNestedScrollConnection = PagerGestureNestedScrollConnection`. Restore the
pager defaults when using `Native` or `TapToHalt`.

## 3. Breaking and removed content

### NavigationRail final overloads

The `v0.9.3` nullable-state usage must be migrated to the final stable shapes:

```kotlin
// Fixed layout: no built-in toggle or expansion animation.
NavigationRail(expanded = false /* or true */) { /* items */ }

// Expandable layout: state is required and non-null.
NavigationRail(state = rememberNavigationRailState()) { /* items */ }
```

Do not use `mode = NavigationRailDisplayMode...` or `state = null` in the stable
API. If a state is nullable, branch before calling the component. The state-based
overload takes `state` first, so positional calls should become named calls.
Toggle content descriptions belong only to the state-based overload. Fixed
compact rails now show a selected-icon background.

### Existing API migrations

- `RadioButtonPreference` removes the top-level `titleColor` and `summaryColor` parameters.
  Use `colors = RadioButtonPreferenceDefaults.radioButtonPreferenceColors(...)`;
  configure `selectedTitleColor` and `selectedSummaryColor` through that factory.
  The row owns selection, click, and haptic behavior; do not add a second callback
  to the embedded radio glyph.
- `OverlayDialog` and `WindowDialog` add `maxWidth`, `largeScreen`, and
  `cornerRadius`. Centered dialogs default to `DialogDefaults.CornerRadius`
  (32dp); bottom-attached dialogs derive and clamp the screen radius.
- `InputField` adds `color`; `SearchBar` does not gain `SearchBar.color`.
  `Modifier.drawBackdrop` also gains `progressiveGradient`, so use named
  arguments for later positional parameters.
- `NavigationBarItem` and `FloatingNavigationBarItem` insert `colors` before
  `badge`; change positional badge calls to `badge = ...`.

### Removed APIs

The `miuix-navigation3-ui` module, its published artifacts, the old
`androidx.navigation3` implementation, and the English/Chinese
`navigation3.md` guides are removed. There is no compatibility alias for the
old scene/decorator APIs. The old navigation runtime's transition-spec helpers
and built-in dialog/bottom-sheet scene strategies are not part of `miuix-nav`.

## 4. Behavior changes

### Pull-to-refresh and overscroll

- `rememberPullToRefreshState(refreshThreshold = ...)` separates the trigger
  threshold from indicator scaling. Use `refreshState ==
  RefreshState.ThresholdReached` for the armed state; do not treat
  `pullProgress == 1f` or `visualProgress == 1f` as universal trigger tests.
- `PullToRefresh` exposes `fullDragProgress` and `visualProgress`, and its
  `onPullProgress` callback observes the full damped drag range.
- Pull-to-refresh and overscroll act on real press/pan sessions; wheel and
  keyboard input pass through. Stale overscroll offsets are reclaimed when the
  child becomes scrollable again. Custom mouse drags are supported.

### Components and navigation

- `TabRow` and `TabRowWithContour` contain horizontal nested scroll/overscroll
  instead of forwarding it to a parent pager.
- Animated overlay layers keep the library transform ahead of the caller's
  modifier. Predictive back can interrupt popup enter/expand animation.
- `SmallTopAppBar` pinned-state handling and `TopAppBarState.Saver` preserve
  scroll state correctly when bars share a `ScrollBehavior`.
- Predictive-back completion/cancellation is arbitrated through the platform
  navigation-event dispatcher, including separate Android windows. The stable
  default for `NavDisplayEffects.blockInputDuringTransition` remains `false`.
- Backdrop sampling is restored during scale animations and rebuilt when
  post-blur padding changes. Gate shader-backed blur with the public support
  check and validate on a capable runtime.
- `Scaffold` keeps a bottom `Snackbar` above a bottom-positioned floating
  toolbar; `floatingToolbar` itself was already present before this migration.

## 5. Tooling baseline

The stable release finalizes the repository toolchain at Compose Multiplatform
`1.12.0`, Kotlin `2.4.20`, AGP `9.4.1`, Gradle `9.7.1`, Baseline Profile
`1.5.0`, Benchmark macro JUnit4 `1.5.0`, Spotless `8.10.2`, Material Color
Utilities `5.0.1`, and AboutLibraries `15.2.0`. CI uses `actions/setup-java@v6`.
These are upstream build details, not dependencies every application must copy.

## Migration checklist

1. Raise Android `minSdk` to at least 24 and update dependencies to `0.9.4`.
2. Replace the entire Navigation3 module/import/route model with `miuix-nav`.
3. Migrate `NavigationRail` to its fixed or non-null state overload; remove
   `mode` and `state = null`.
4. Move RadioButtonPreference text colors into `colors`, and make positional
   navigation-item badges named after adding `colors`.
5. Recheck dialog, InputField, PullToRefresh, overscroll, blur, and pager calls
   against the stable source; use named arguments for changed signatures.
6. Recompile consumers and separately validate compilation, visual behavior,
   predictive back, blur support, and nested-pager gestures on target platforms.

## Evidence

- Current stable tag: `v0.9.4`
- Stable source commit: `39c40f99844227b853f0049a0933b1f3ae6c00ba`
- Comparison source: `v0.9.3` tag
- Public docs: `docs/guide/miuix-nav.md`, `docs/guide/utils.md`,
  `docs/components/navigationbar.md`, and `docs/components/navigationrail.md`
- Public source: `miuix-ui/.../basic/NavigationBar.kt`,
  `miuix-ui/.../basic/NavigationRail.kt`,
  `miuix-ui/.../utils/PagerGestureUtils.kt`,
  `miuix-ui/.../basic/TabRow.kt`, and the `miuix-nav` core/state/transition
  files
