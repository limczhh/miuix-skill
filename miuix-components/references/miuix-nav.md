# `miuix-nav` Routing and Transitions

Self-contained Compose Multiplatform routing runtime for `0.9.5-rc02` (`docs/guide/miuix-nav.md`, `miuix-nav/.../`). Do not confuse with AndroidX Navigation3 APIs.

Contents: [dependency & keys](#dependency-and-route-keys) · [NavDisplay & stack](#navdisplay-and-lifecycle) · [transitions & Expansion](#transitions-expansion-and-back-gestures) · [navigation chrome](#routing-vs-navigation-chrome) · [v1 boundaries](#v1-boundaries)

## Dependency and Route Keys

Add `top.yukonga.miuix.kmp:miuix-nav:0.9.5-rc02` (`miuix-nav-android` for Android-only). Persistent stack saving (`rememberNavBackStack`) uses `kotlinx.serialization`, so the **consumer module must apply the Kotlin Serialization compiler plugin** matching its Kotlin version (`kotlin("plugin.serialization")` or catalog alias).

```kotlin
@Serializable
sealed interface Route : NavKey {
    @Serializable data object Home : Route
    @Serializable data class Detail(val id: String) : Route
}
```

- All keys in `rememberNavBackStack` must be `@Serializable` (`data object` / `data class`, unique `toString()` / `contentKey` — duplicate equal keys cannot be pushed twice).
- If non-serializable keys use `navBackStackOf(...)`, process-death restoration does not apply.

## `NavDisplay` and Lifecycle

```kotlin
val backStack = rememberNavBackStack<Route>(Route.Home)

NavDisplay(backStack = backStack, onBack = { backStack.removeLastOrNull() }) {
    entry<Route.Home> { HomeScreen(onOpen = { backStack.add(Route.Detail(it)) }) }
    entry<Route.Detail> { route -> DetailScreen(route.id) }
}
```

- `NavBackStack` supports `add`, `removeLastOrNull`, `removeAt`, or top replacement; `NavController` (`rememberNavController`) is a thin wrapper (`push`, `pop`, `replace`, `popUntil` retaining root).
- Entries get per-entry lifecycles (`RESUMED` on settled top, `STARTED` when covered, cleared when popped) and `SavedStateHandle`/ViewModel stores via `NavEntryViewModel.kt`.
- **Focus clearing**: `NavDisplay` clears focus whenever the top entry key changes (including while covered entries stay composed) — attach IME/search focus restoration to active entries rather than adding duplicate hacks.

## Transitions, Expansion, and Back Gestures

- **Packages**: `NavDisplay`/`NavBackStack`/`NavKey`/`NavController`/`NavDisplayEffects`/`LocalNavTransitionScope`/`rememberNavSystemCornerRadius` live in `top.yukonga.miuix.kmp.nav.core`; `NavTransitions`, `NavMotion`, `NavSwipeDirection`, and `Modifier.navTransitionSource` live in `.nav.transition`; `Modifier.navSwipeDismiss` lives in `.nav.gesture`.
- **Built-in presets (`NavTransitions`)**: `MiuixDefault` (full-width slide + 1/4 covered parallax), `Modal` (bottom-up sheet-like slide), `Expansion` / `NavTransitions.expansion(containerColor, cornerRadius = 28.dp, motion = NavMotion.Default)`, and `None`. Override per route via `entry(transition = ...)`.
- **Component Expansion (`NavTransitions.Expansion`)**:
  - Bind source card/item to its destination route: `Modifier.navTransitionSource(route, cornerRadius = 16.dp, containerColor = Color.Unspecified, corners = NavTransitionCorners(cornerRadius), shape = null, sourceKey = null)`.
  - Attach `navTransitionSource` **after outer padding and before surface/background/clip**; match its fill color and single-contour outline (no holes), and keep `Card` `pressFeedbackType = PressFeedbackType.None` (default; use `showIndication = true` instead of `SinkFeedback`) so press shrink does not distort captured bounds. When multiple sources bind the same route, the pressed one wins.

```kotlin
val containerColor = MiuixTheme.colorScheme.surface
val sourceColor = MiuixTheme.colorScheme.surfaceContainer
NavDisplay(backStack = stack, transition = NavTransitions.expansion(containerColor)) {
    entry<Route.Home> {
        Card(
            modifier = Modifier.navTransitionSource(Route.Detail(1), CardDefaults.CornerRadius, sourceColor),
            onClick = { stack.add(Route.Detail(1)) },
        ) { /* Card content */ }
    }
    entry<Route.Detail>(swipeDismiss = NavSwipeDirection.LeftToRight) { detail -> DetailScreen(detail.id) }
}
```

  - Destination page must paint an opaque background matching the resolved container color (resolved in order: `expansion(containerColor)`, `NavDisplayEffects.backdropColor`, source `containerColor`). Missing live same-window source or opaque color falls back to `MiuixDefault`.
  - On pop/return, the source needs visible area inside the container (otherwise centered shrink/fade). Provide a unique stable `sourceKey` per source inside the same origin->destination pair if recomposed while covered.
- **`NavDisplayEffects`**: `enableCornerClip`, `cornerClipRadius` (`rememberNavSystemCornerRadius()` for full-screen host; keep `0.dp` on interior multi-pane `NavDisplay`), `cornerClipMode`, `dimAmount`, `blockInputDuringTransition`, `backdropColor`. Transitions whose `NavTransition.handlesCornerClip(scope)` is `true` (like `Expansion`) own their animated clip and automatically skip `enableCornerClip` while preserving `dimAmount`.
- **Custom transitions & scopes**: `navDirectionalTransition(push, pop, predictivePop)` and `navGraphicsTransition(...)`. `LocalNavTransitionScope.current` (valid only inside a `NavDisplay` entry) provides coarse `isRunning` for composition plus `relativeDepth`, `role`, `change`, `gesture`, `settle`, `layoutSize`, `layoutDirection`, `density`, `entryKey`, `layoutPositionInWindow`, and `windowToLocal(...)` for deferred draw/graphicsLayer reads. `NavTransition` also offers `Modifier.transformContent(scope)`.
- **Swipe-to-dismiss**: Opt-in per entry via `entry(swipeDismiss = NavSwipeDirection.LeftToRight)` (`None` by default; physical finger motion, not RTL-mirrored). Consumed pointer release cancels dismissal. Disable outer swipe (`None`) on nested `NavDisplay` routes. In separate platform windows, wrap content in `WindowNavigationEventScope`.

## Routing vs Navigation Chrome

`NavigationBar` / `NavigationRail` display destinations and should **not** own a separate back stack. Keep destination/pager selection above compact/wide layout branching; use `NavDisplay` for route transitions (`docs/demo/NavExpandDemo.kt` shows Expansion usage).

## v1 Boundaries

Single flat stack only: no dialog/bottom-sheet scene strategies, no multi-element shared content matching (`Expansion` handles page-container expansion only), no KSP annotation routing, and no built-in `navigateForResult` channel.
