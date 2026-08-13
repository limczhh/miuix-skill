# v0.9.3 → v0.9.4-rc01 Audit and Migration Notes

These notes cover the complete `v0.9.3..HEAD` range at upstream commit `4a6b750b` (full ref: `4a6b750b578880146876e4ab77097d9b01702413`). It contains 66 commits and is not just a component release: the navigation module is replaced, several existing components change behavior, and the Android/build baseline moves forward.

Contents: [baseline](#dependency-and-platform-baseline) · [paths and names](#1-paths-names-and-compatibility-sensitive-changes) · [new content](#2-new-public-content) · [removed content](#3-removed-or-no-longer-supported-content) · [behavior changes](#4-major-behavior-changes-in-existing-components) · [example/tooling](#example--and-tooling-only-changes) · [guardrails](#migration-guardrails)

`BuildConfig.LIBRARY_VERSION` is `0.9.4`; the publication script produces `0.9.4-rc01` with `-Prc=01`. The upstream `v0.9.4-rc01` tag now points to this commit, while the stable `v0.9.4` tag does not exist yet; treat this as a release-candidate contract, not a stable-release contract.

## Dependency and platform baseline

```kotlin
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.4-rc01")
implementation("top.yukonga.miuix.kmp:miuix-preference:0.9.4-rc01") // optional
implementation("top.yukonga.miuix.kmp:miuix-nav:0.9.4-rc01") // optional
```

- Android `minSdk` rises from 23 to 24.
- The base Android artifacts follow `minSdk = 24`; `miuix-blur-android` remains a separate API-33 target and must not be added to a lower-minSdk app without an explicit platform decision.
- Compose Multiplatform moves from `1.11.1` to `1.12.0-rc01`; Kotlin moves to `2.4.10`, AGP to `9.3.1`, lifecycle to `2.11.0`, and the baseline-profile tool reaches `1.5.0-rc01`.
- The navigation runtime now uses lifecycle/ViewModel, `kotlinx.serialization`, immutable collections, Compose Foundation, and `androidx.navigationevent`; the old AndroidX Navigation 3 runtime and its direct `androidx.collection` version-catalog dependency are removed. The example's generated `aboutlibraries.json` still lists collection artifacts as metadata, which is not a restored direct dependency or Navigation 3 API.

## 1. Paths, names, and compatibility-sensitive changes

### Navigation module replacement

| v0.9.3 | v0.9.4-rc01 |
|---|---|
| Gradle module/artifact `miuix-navigation3-ui` | `miuix-nav` |
| Namespace/package roots `androidx.navigation3.*` | `top.yukonga.miuix.kmp.nav.*` |
| `docs/guide/navigation3.md` | `docs/guide/miuix-nav.md` |
| `docs/zh_CN/guide/navigation3.md` | `docs/zh_CN/guide/miuix-nav.md` |
| Example `example/shared/.../navigation3/` | `example/shared/.../navigation/` |

This is a replacement, not a package alias. Do not mechanically change only the dependency string: the old `androidx.navigation3` scene model (`Scene`, `SceneStrategy`, `OverlayScene`, `DialogSceneStrategy`, scene decorators, `rememberSceneState`, and the old `NavDisplay` overloads) is gone. The new runtime is self-contained and uses `NavKey`, `rememberNavBackStack`, `NavDisplay`, `NavController`, and the `entry<T> { ... }` DSL. Read [the dedicated routing reference](miuix-nav.md).

### Existing API names that changed

- `RadioButtonPreference` removes the public `titleColor` and `summaryColor` parameters. Use `colors = RadioButtonPreferenceDefaults.radioButtonPreferenceColors(...)`, which provides selected and unselected title/summary colors. The selected defaults tint title and summary with `MiuixTheme.colorScheme.primary`; the row now owns the click and haptic behavior, while its embedded `RadioButton` is visual-only. Keep one row-level `onClick`, do not wire a second callback to the radio glyph, and let `enabled` gate the whole interaction.
- `OverlayDialog` and `WindowDialog` expose the existing `DialogDefaults.MaxWidth` through a new `maxWidth` parameter and add `largeScreen` and `cornerRadius`. `DialogDefaults.CornerRadius` is new; `DialogDefaults.MaxWidth` itself already existed in v0.9.3.
- `InputField` (not the `SearchBar` wrapper) adds `color` for the capsule background. Pass `Color.Transparent` only when another surface owns that background.
- `Scaffold`'s `floatingToolbar` and `ToolbarPosition` already existed in v0.9.3. The 0.9.4 change is the layout behavior that reserves space above a bottom-positioned toolbar for the `Snackbar`; it is not a newly introduced slot.
- `TopAppBar`'s `TitleWidthFraction` → `TITLE_WIDTH_FRACTION` change is private implementation cleanup, not a caller-facing API rename.

## 2. New public content

### `BreadcrumbBar`

`miuix-ui` adds `BreadcrumbItem(path, text?)`, `BreadcrumbBar(items, onItemClick: (Int) -> Unit, ...)`, `BreadcrumbBarDefaults`, `BreadcrumbBarColors`, and `List<BreadcrumbItem>.joinToPath()`. `highlightIndex` defaults to the last item and controls the active segment; a negative value disables highlighting and automatic centering. The component auto-scrolls a highlighted item into view and supports an optional hoisted `ScrollState`.

### Progressive blur

`miuix-blur` adds:

- `ProgressiveBlur(angle, startFraction, endFraction, curve)` and the `Top`, `Bottom`, `Left`, and `Right` presets.
- `Modifier.progressiveTextureBlur(...)` overloads for one radius or independent X/Y radii.
- `BackdropEffectScope.progressiveBlur(...)` and `progressiveTextureBlurEffect(...)` for custom `drawBackdrop` pipelines.
- `Modifier.drawBackdrop(..., progressiveGradient = ...)`, which must use the same gradient as the effect block for a genuinely sharp clear end.
- `BlurDefaults.ProgressiveNoiseCoefficient`, whose default is `0f`.

The blur ramps through real intermediate levels and ends with a full-resolution sharp pass. Prefer narrow edge bands/navigation bars over large fills; the clear pass costs more GPU bandwidth than a uniform `textureBlur`.

### Window back-event rebinding

`miuix-ui` adds the public multiplatform `WindowNavigationEventScope` utility at `miuix-ui/.../utils/WindowNavigationEventScope.kt`. The `WindowDialog`, `WindowBottomSheet`, `WindowListPopup`, and `WindowCascadingListPopup` wrappers use it automatically so predictive/system back handlers resolve against the focused platform window. Custom separate-window hosts that contain predictive-back handlers should use the same scope.

## 3. Removed or no-longer-supported content

- The `miuix-navigation3-ui` module, its Gradle include, its published artifact names, and its `androidx.navigation3.*` implementation sources are deleted.
- The English and Chinese `navigation3.md` guides are deleted; use the corresponding `miuix-nav.md` guides.
- The old `androidx.navigation3-runtime` dependency and the old Miuix scene/decorator API are not available through a compatibility layer. In particular, do not expect `NavDisplayTransitionEffects`, `defaultTransitionSpec`, `defaultPopTransitionSpec`, `defaultPredictivePopTransitionSpec`, `rememberDecoratedNavEntries`, or a built-in dialog/bottom-sheet scene strategy in `miuix-nav` v1.
- `miuix-nav` v1 also has no built-in `navigateForResult`/`setResult` channel, shared-element scene strategy, KSP route registry, or dialog/bottom-sheet scene model. Compose those in application code only after checking the target version.

The removal of old `navigation3` paths is the only broad public deletion in this range. For overscroll, the old documentation examples mentioning `scrollEasing`, `springStiff`, and `springDamp` were already not parameters of the v0.9.3 source signature; their disappearance from the docs is not an additional API removal.

## 4. Major behavior changes in existing components

### Pull-to-refresh

- `rememberPullToRefreshState(refreshThreshold = 0.25f)` separates the user trigger threshold from the built-in visual threshold.
- `pullProgress` is now relative to the effective trigger/visual threshold and saturates at 1.0 there. `fullDragProgress` represents the entire damped drag range, while `visualProgress` represents indicator scaling. Use the latter two deliberately instead of assuming the old `pullProgress` meaning.
- `PullToRefreshState.refreshThreshold` exposes the normalized configured threshold with an internal setter; it is updated by `rememberPullToRefreshState(...)`.
- `PullToRefresh` adds `onPullProgress`, observing `fullDragProgress` on composition and subsequent changes.
- Pull-to-refresh resolves only inside a real press/pan gesture session. Wheel, keyboard, and unrelated non-gesture nested-scroll events no longer latch the pull or trigger a release-based refresh.

### Overscroll

- `overScrollVertical`, `overScrollHorizontal`, and `overScrollOutOfBound` now default to enabled on all platforms, not only Android/iOS.
- Overscroll accumulation is limited to real press/pan sessions; mouse-wheel and keyboard scrolling pass through. Stale offsets are reclaimed when the child can scroll again, including pagination-style transitions.
- The theme-level `MiuixOverscrollFactory` keeps all-platform integration and gains stale-offset unwinding; the explicit press/pan session gate is implemented by the `Modifier.overScroll*` path, so verify the target Compose scroll source separately when relying on theme-level overscroll.

### Dialogs, sheets, and popups

- Animated overlay layers now put the library's `graphicsLayer` before the caller's `modifier`, so caller draw modifiers follow the dialog/sheet/popup transform instead of being left outside it. Do not add an application-side transform workaround.
- List-popup and cascading-popup predictive back can interrupt the enter/expand animation and reverse only the active track. Cascading menus retain selected-state updates by entry/item position; keep the entry and item order stable while a cascading popup is shown.
- `largeScreen = null` still uses the 840dp width / 480dp height detection. Centered dialogs use `DialogDefaults.CornerRadius` (32dp); bottom-attached dialogs derive the screen radius and clamp it to 32..48dp unless `cornerRadius` is supplied.

### Top app bars

When a `SmallTopAppBar` shares a `ScrollBehavior` with a collapsible bar, it now marks the state as pinned, clears a stale `heightOffset`, and bases small-title visibility on `contentOffset`. `TopAppBarState.Saver` preserves this internal pinned state, and the state is restored when a later large/collapsible bar takes ownership. Verify this behavior in source instead of adding an external offset reset.

### Navigation and predictive back

The new runtime arbitrates predictive-back completion/cancellation through the platform navigation-event dispatcher, bridges separate Android windows, and keeps pointer filters distinct between nested displays. Its transition input-blocking default is off (`NavDisplayEffects.blockInputDuringTransition = false`); enable it only when the product wants to swallow input on intermediate layers.

### Blur/backdrop behavior

Backdrop effects rebuild their sampling/effect chain when padding grows after blur, restore backdrop sampling during scale animations, and clean up progressive composite state when the gradient is cleared. These are behavior fixes rather than new component names; still gate blur with `isRuntimeShaderSupported()` and validate on a capable runtime.

### Example- and tooling-only changes

The range also updates the first-party Example's adaptive state preservation, search-bar inset timing, liquid-glass interaction, overscroll/load-more repro pages, iOS packaging, baseline profiles, and CI/tooling. These are useful composition or maintenance evidence, but they are not additional public Miuix APIs. Do not copy Example-only helpers or treat generated profile/dependency metadata as library contracts.

## Migration guardrails

- Pin current-candidate source verification to commit `4a6b750b` / tag `v0.9.4-rc01` until an upstream stable `v0.9.4` tag exists.
- For navigation, migrate the whole module/package/API model and the example path; do not perform a string-only artifact rename.
- Treat `RadioButtonPreference.titleColor`/`summaryColor` as deleted and migrate to `colors`.
- For `RadioButtonPreference`, keep selection and callback state at the row; use the new selected-color defaults or a verified `RadioButtonPreferenceColors` value instead of manually tinting title/summary text.
- Use `InputField.color`, not a fabricated `SearchBar.color`, and do not report the existing `Scaffold.floatingToolbar` slot as new in 0.9.4.
- Keep `isRefreshing` hoisted, preserve `Scaffold` padding and `MiuixTheme`, and avoid duplicating state to compensate for the new gesture/transition behavior.
- Separate source/API verification, compilation, and visual/device confirmation. Dialog window sizing, predictive back, blur support, and gesture feel still need runtime validation.
