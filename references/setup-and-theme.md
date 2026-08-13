# Project Setup and Theme

Use this reference to decide who owns the application theme and page/popup host. After ownership is resolved, continue with [integrated usage patterns](usage-patterns.md) for page composition and [design language](design-language.md) for visual decisions.

## Theme and Host Ownership

| Code context | Theme rule | Scaffold rule |
|---|---|---|
| Existing Miuix application screen/component | Inspect and reuse the application's existing `MiuixTheme`; do not nest another theme merely to make a snippet self-contained | Reuse the intended page/root Scaffold; add a nested Scaffold only for a real page boundary or deliberately scoped popup host |
| Standalone runnable `App`/complete example | Create a remembered `ThemeController` from the example's theme inputs and provide one root `MiuixTheme` | Add Scaffold when the example needs bars, insets, snackbar, floating controls, or Overlay components |
| Isolated component snippet | State that the caller must place it below `MiuixTheme`; do not invent application theme ownership | State any host requirement; wrap locally only when the snippet must be independently runnable |

When theme inputs such as color mode, key color, palette style, or color specification can change, include them as `remember` keys as demonstrated by `example/shared/.../ui/Theme.kt`.

## Quick Start

**Compose Multiplatform project** (most common — Android, iOS, Desktop, Web):

```kotlin
// settings.gradle.kts — ensure mavenCentral() is in dependencyResolutionManagement.repositories
// module build.gradle.kts
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.4-rc01")
            implementation("top.yukonga.miuix.kmp:miuix-preference:0.9.4-rc01") // optional
            implementation("top.yukonga.miuix.kmp:miuix-icons:0.9.4-rc01") // optional
            implementation("top.yukonga.miuix.kmp:miuix-blur:0.9.4-rc01") // optional; Android target requires API 33
            implementation("top.yukonga.miuix.kmp:miuix-nav:0.9.4-rc01") // optional
        }
    }
}
```

**Pure Android project:**

```kotlin
// module build.gradle.kts — ensure mavenCentral() is configured
android {
    defaultConfig {
        minSdk = 24
    }
}

dependencies {
    implementation("top.yukonga.miuix.kmp:miuix-ui-android:0.9.4-rc01")
    implementation("top.yukonga.miuix.kmp:miuix-preference-android:0.9.4-rc01") // optional
    implementation("top.yukonga.miuix.kmp:miuix-icons-android:0.9.4-rc01") // optional
    implementation("top.yukonga.miuix.kmp:miuix-nav-android:0.9.4-rc01") // optional
    // miuix-blur-android itself requires minSdk 33; add it only when that target is acceptable.
}
```

`miuix-ui`, `miuix-preference`, `miuix-icons`, and `miuix-nav` follow the candidate's Android baseline of `minSdk = 24`; `miuix-blur-android` is a separate API-33 target. Keep the dependency artifact aligned with the source set: use the common artifact in `commonMain` and the `-android` artifact for a pure Android module. Verify the target project's Compose/Kotlin/AGP compatibility before changing its build versions.

## MiuixTheme Setup

Use `ThemeController` for runtime system dark mode and dynamic-color behavior. Do not choose `lightColorScheme()` merely for convenience, because that fixes the app to a light scheme; use a fixed `lightColorScheme()`/`darkColorScheme()` pair only for an intentional fixed palette, a light-only product, or deterministic preview/test content.

```kotlin
@Composable
fun App() {
    val controller = remember { ThemeController(ColorSchemeMode.System) }
    MiuixTheme(controller = controller) {
        Scaffold(
            topBar = { /* TopAppBar, NavigationBar, etc. */ },
            bottomBar = { /* NavigationBar, TabRow, etc. */ },
            floatingActionButton = { /* FloatingActionButton */ },
            floatingToolbar = { /* FloatingToolbar */ },
            floatingToolbarPosition = ToolbarPosition.BottomCenter,
        ) { innerPadding ->
            // Apply innerPadding to the content root or scrolling content.
        }
    }
}
```

`ColorSchemeMode` options: `System` / `Light` / `Dark` / `MonetSystem` / `MonetLight` / `MonetDark`.

> **Version pinning**: Current file paths and component mappings reflect tag `v0.9.4-rc01` at commit `4a6b750b`. There is no stable `v0.9.4` tag yet; for stable `v0.9.3` work, use the historical migration reference and substitute that tag in [Source verification](source-verification.md).

## Android Studio Preview

The candidate repository does not provide a first-party `@Preview` sample, so treat Preview as target-tooling integration rather than a guaranteed Miuix runtime environment.

- Use the target source set's normal Preview annotation/dependency and render a page/component with fake deterministic state and no real services.
- Wrap preview content in one `MiuixTheme` with a deterministic Light or Dark `ThemeController`; avoid System/Monet inputs when the preview host cannot supply real platform theme data.
- Add `Scaffold` when previewing an Overlay component because it needs the popup host. Window popups, system back, focus/IME, animation timing, dynamic color, and platform windows still require runtime validation.
- Preview cannot prove shader or blur behavior. Keep the unsupported fallback renderable and validate those effects on an API/platform that supports them.

## Continue With

- For application root, page shell, Scaffold padding, and state patterns, read [Example-derived usage patterns](usage-patterns.md).
- For semantic colors, typography, grouping, and component Defaults, read [Design language and Defaults](design-language.md).
- For exact `MiuixTheme`, `ThemeController`, and Scaffold source paths, use [Source verification](source-verification.md).
- Before returning code, satisfy the [Code Delivery Contract](../SKILL.md#code-delivery-contract).
