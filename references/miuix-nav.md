# `miuix-nav` Routing and Transitions

Use this reference for route/back-stack architecture in the `0.9.4-rc01` candidate snapshot. `miuix-nav` is a self-contained Compose Multiplatform runtime; it replaces the old `miuix-navigation3-ui` module and does not use `androidx.navigation3` scene APIs. This is a whole-module migration: old `androidx.navigation3.*` imports, scene decorators, `NavDisplayTransitionEffects`, and the old transition-spec overloads have no compatibility alias.

Contents: [route model](#dependency-and-route-model) · [basic display](#minimal-navdisplay) · [state and lifecycle](#saveable-identity-and-lifecycle) · [transitions and gestures](#transitions-and-back-gestures) · [navigation chrome](#keep-routing-separate-from-navigation-chrome) · [v1 boundaries](#v1-boundaries) · [evidence](#evidence-paths)

## Dependency and route model

Add `top.yukonga.miuix.kmp:miuix-nav:<version>` to `commonMain` or the matching platform source set. A persisted stack uses `kotlinx.serialization`, so annotate the complete route hierarchy and every concrete route key with `@Serializable`.

The consumer project must also enable the Kotlin Serialization compiler plugin; the Miuix library's runtime dependency does not apply that compiler plugin to the application. Use the syntax that matches the target project's plugin management:

```kotlin
plugins {
    kotlin("multiplatform")
    kotlin("plugin.serialization") // use the same Kotlin version as the project
}
```

With a version catalog, use the project's `kotlin.serialization` plugin alias instead. Do not add a second Kotlin version just for `miuix-nav`. If the app uses an in-memory `navBackStackOf(...)` and no `@Serializable` route keys, the save/restore contract below does not apply; state that limitation explicitly.

```kotlin
import kotlinx.serialization.Serializable
import top.yukonga.miuix.kmp.nav.core.NavDisplay
import top.yukonga.miuix.kmp.nav.core.NavKey
import top.yukonga.miuix.kmp.nav.core.rememberNavBackStack

@Serializable
sealed interface Route : NavKey {
    @Serializable data object Home : Route
    @Serializable data class Detail(val id: String) : Route
}
```

`NavKey` is only a route marker. Keep business state and screen state in the application or screen owner; use route values for navigation identity.

## Minimal `NavDisplay`

`rememberNavBackStack<Route>(...)` returns a mutable `NavBackStack`. The `NavDisplay` content lambda is an entry-registration DSL, and matching is by the concrete runtime route type, so register every concrete route type that can appear in the stack.

```kotlin
@Composable
fun App() {
    val backStack = rememberNavBackStack<Route>(Route.Home)

    NavDisplay(
        backStack = backStack,
        onBack = { backStack.removeLastOrNull() },
    ) {
        entry<Route.Home> {
            HomeScreen(onOpen = { id -> backStack.add(Route.Detail(id)) })
        }
        entry<Route.Detail> { route ->
            DetailScreen(route.id)
        }
    }
}
```

For direct list operations use `add`, `removeLastOrNull`, `removeAt`, or replace the top entry. `NavController` is an optional thin wrapper with `push`, `pop`, `replace`, and `popUntil`; `pop()` keeps the root entry. `rememberNavController<Route>(Route.Home)` combines it with the remembered serializable stack.

## Saveable identity and lifecycle

- `@Serializable` is a hard requirement for every key in `rememberNavBackStack`. If keys cannot be serialized, use the in-memory `navBackStackOf(...)` factory and do not claim process-death restoration.
- Use `data object` / `data class` routes or a value-derived `contentKey`. Equal route/content keys cannot be pushed twice, and distinct keys whose `toString()` collides are rejected.
- `entry<T>(contentKey = { route -> ... })` controls the identity used for each entry's `rememberSaveable` state. Keep it stable across recompositions and process death.
- Each entry has its own lifecycle and ViewModel store. A settled top entry is `RESUMED`; covered entries remain `STARTED`; a popped entry is cleared after removal.

## Transitions and back gestures

The built-in presets are `NavTransitions.MiuixDefault`, `NavTransitions.Modal`, and `NavTransitions.None`. Set a global transition on `NavDisplay` or override one route in `entry(transition = ...)`.

Swipe-to-dismiss is opt-in. None of the built-in presets enables it automatically. Use the route override when the gesture matches the transition:

```kotlin
import top.yukonga.miuix.kmp.nav.transition.NavSwipeDirection
import top.yukonga.miuix.kmp.nav.transition.NavTransitions

NavDisplay(backStack, transition = NavTransitions.MiuixDefault) {
    entry<Route.Home>(swipeDismiss = NavSwipeDirection.LeftToRight) {
        HomeScreen()
    }
    entry<Route.Detail>(
        transition = NavTransitions.Modal,
        swipeDismiss = NavSwipeDirection.TopToBottom,
    ) { route ->
        DetailScreen(route.id)
    }
}
```

`NavSwipeDirection` describes physical finger motion and is not mirrored automatically for RTL. Use `LeftToRight` for the usual LTR back swipe and `RightToLeft` for the RTL counterpart. Pass `NavSwipeDirection.None` to disable a gesture on a route. For custom effects, use `navGraphicsTransition { scope -> ... }`; read `relativeDepth`, `gesture`, `settle`, `layoutSize`, and `layoutDirection` inside its graphics-layer block.

`NavDisplayEffects` owns cross-cutting effects rather than the transition itself: `enableCornerClip`, `cornerClipRadius`, `cornerClipMode`, `dimAmount`, `blockInputDuringTransition`, and `backdropColor`. Its default input blocking is off; `NavDisplayEffects.None` disables the clip/dim/input effects. In a multi-pane layout, clip the `NavDisplay` pane and normally use `cornerClipRadius = 0.dp`; the physical device corner belongs to the screen edge, not the middle pane boundary.

For asymmetric push/pop/predictive visuals, use `navDirectionalTransition(push, pop, predictivePop)`. For custom graphics-layer transitions, `navGraphicsTransition(...)` exposes `opaqueDepth`, `dismissDirection`, `motion`, an optional `scrim` curve, and a `NavTransitionScope` with depth, role, change, gesture/settle context, layout size, direction, and density. Read those values inside the deferred graphics-layer block.

The built-in `NavDisplay` installs the shared navigation-event bridge. If a custom host needs the lower-level stream, `PredictiveBackHandler` is in `nav/gesture`; if the host is a separate platform window, wrap its content in `miuix-ui`'s `WindowNavigationEventScope` so the focused window owns back dispatch.

## Keep routing separate from navigation chrome

Use `NavigationBar` or `NavigationRail` for persistent destination chrome, and use `NavDisplay` for the route stack. Hoist the selected destination/back stack before compact/wide branching so resizing does not create two sources of truth. A nested `NavDisplay` is supported, but disable the outer route's swipe with `entry(swipeDismiss = NavSwipeDirection.None)` when the inner stack must own the gesture.

## v1 boundaries

The current runtime is deliberately a single flat stack. It does not provide dialog/bottom-sheet scene strategies, shared-element transitions, KSP route registration, or a built-in `navigateForResult`/`setResult` channel. Layer those behaviors in application code only after confirming the target version.

## Evidence paths

- Guide: `docs/guide/miuix-nav.md`
- Core API: `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/core/`
- Transitions: `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/transition/`
- Gesture handling: `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/gesture/`
- Integrated example: `example/shared/src/commonMain/kotlin/navigation/` and `AppContent.kt`

Verify those paths at the exact snapshot in [Source verification](source-verification.md); do not substitute the deleted `docs/guide/navigation3.md` or infer APIs from another Navigation library.
