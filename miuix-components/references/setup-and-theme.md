# Project Setup and Theme

Use this reference only for dependency, theme, root-host, and preview setup. It is not a page-composition guide. If the task is about page structure rather than setup, use the `miuix-guidelines` Skill instead of loading this file.

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
            implementation("top.yukonga.miuix.kmp:miuix-ui:<version>")
            implementation("top.yukonga.miuix.kmp:miuix-preference:<version>") // optional
            implementation("top.yukonga.miuix.kmp:miuix-icons:<version>") // optional
            implementation("top.yukonga.miuix.kmp:miuix-glass-icons:<version>") // optional Glass icons
            implementation("top.yukonga.miuix.kmp:miuix-material:<version>") // optional direct material types
            implementation("top.yukonga.miuix.kmp:miuix-blur:<version>") // optional; Android artifact requires API 33
            implementation("top.yukonga.miuix.kmp:miuix-squircle:<version>") // optional direct shape use
            implementation("top.yukonga.miuix.kmp:miuix-glass:<version>") // optional Glass family
            implementation("top.yukonga.miuix.kmp:miuix-nav:<version>") // optional
        }
        desktopMain.dependencies {
            implementation("top.yukonga.miuix.kmp:miuix-window:<version>") // optional; Desktop JVM only
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
    implementation("top.yukonga.miuix.kmp:miuix-ui-android:<version>")
    implementation("top.yukonga.miuix.kmp:miuix-preference-android:<version>") // optional
    implementation("top.yukonga.miuix.kmp:miuix-icons-android:<version>") // optional
    implementation("top.yukonga.miuix.kmp:miuix-glass-icons-android:<version>") // optional Glass icons
    implementation("top.yukonga.miuix.kmp:miuix-material-android:<version>") // optional direct material types
    implementation("top.yukonga.miuix.kmp:miuix-glass-android:<version>") // optional Glass family
    implementation("top.yukonga.miuix.kmp:miuix-nav-android:<version>") // optional
    // miuix-blur-android and the Android blur dependency of miuix-glass declare minSdk 33;
    // add them only when that target boundary is acceptable.
}
```

`miuix-ui`, `miuix-preference`, `miuix-icons`, `miuix-material`, and `miuix-nav` follow the repository's Android baseline of `minSdk = 24`; `miuix-blur-android` declares `minSdk = 33`. `miuix-glass` targets API 24 and has an opaque fallback below API 33, but its transitive `miuix-blur` Android artifact still creates the dependency/manifest boundary described by the upstream Glass guide. Keep the dependency artifact aligned with the source set: use the common artifact in `commonMain`, `-android` for a pure Android module, and `miuix-window` only in `desktopMain`. Verify the target project's published version and Compose/Kotlin/AGP compatibility before changing its build versions.

For an Android app with `minSdk < 33` that uses `miuix-glass`, follow the upstream manifest boundary for its transitive blur artifact: add `tools:overrideLibrary="top.yukonga.miuix.kmp.blur"` only after accepting that shader-backed blur is skipped below API 33, and keep an opaque fallback. This is a build/runtime constraint, not a promise that Glass blur works on older Android versions.

The manifest entry is placed on the `<uses-sdk>` element, not on a Glass composable:

```xml
<manifest xmlns:tools="http://schemas.android.com/tools">
    <uses-sdk tools:overrideLibrary="top.yukonga.miuix.kmp.blur" />
</manifest>
```

Use this only for the `minSdk < 33` compatibility decision above; it does not make the Android blur implementation available on older releases.

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

> **Version pinning**: Current file paths and component mappings reflect the source-audit snapshot `main` at commit `d8efe1872c08c863a8b33e7044dd2a8b43038032`, not a new published version. For a project pinned to `v0.9.4` or another release, inspect that target declaration before using a main-branch-only API. For the historical `v0.9.3` → `v0.9.4` migration, use the single [migration reference](release-v0.9.4.md).

## Android Studio Preview

The repository does not provide a first-party `@Preview` sample, so treat Preview as target-tooling integration rather than a guaranteed Miuix runtime environment.

- Use the target source set's normal Preview annotation/dependency and render a page/component with fake deterministic state and no real services.
- Wrap preview content in one `MiuixTheme` with a deterministic Light or Dark `ThemeController`; avoid System/Monet inputs when the preview host cannot supply real platform theme data.
- Add `Scaffold` when previewing an Overlay component because it needs the popup host. Window popups, system back, focus/IME, animation timing, dynamic color, and platform windows still require runtime validation.
- Preview cannot prove shader or blur behavior. Keep the unsupported fallback renderable and validate those effects on an API/platform that supports them.

## Boundary

This file establishes setup facts and library-host assumptions. It does not require the agent to read the Example, visual references, or source-verification reference afterward. Open one of those only when the task contains that separate question. Before returning code, satisfy the [Code Delivery Contract](../SKILL.md#delivery-contract).
