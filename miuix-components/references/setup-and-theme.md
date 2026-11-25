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

All Miuix coordinates below use `0.9.5-rc01`. The pinned upstream build uses Kotlin `2.4.21`, Compose Multiplatform `1.13.0-rc01`, serialization `1.13.0`, navigation-event `1.2.0`, AGP `9.5.2`, Gradle `9.9.2`, and JDK 21. Check the consumer's resolved compiler/dependency metadata; these upstream versions do not require copying Example-only tooling into an application. Example also adds `androidx.metrics:metrics-performance:1.0.0` for its Android `JankStats` monitor; add it only when the consumer actually uses that monitor.

**Compose Multiplatform project** (most common — Android, iOS, Desktop, Web):

```kotlin
// settings.gradle.kts — ensure mavenCentral() is in dependencyResolutionManagement.repositories
// module build.gradle.kts
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.5-rc01")
            implementation("top.yukonga.miuix.kmp:miuix-preference:0.9.5-rc01") // optional
            implementation("top.yukonga.miuix.kmp:miuix-icons:0.9.5-rc01") // optional
            implementation("top.yukonga.miuix.kmp:miuix-glass-icons:0.9.5-rc01") // optional Glass icons
            implementation("top.yukonga.miuix.kmp:miuix-material:0.9.5-rc01") // optional direct material types
            implementation("top.yukonga.miuix.kmp:miuix-blur:0.9.5-rc01") // optional; Android artifact requires API 33
            implementation("top.yukonga.miuix.kmp:miuix-squircle:0.9.5-rc01") // optional direct shape use
            implementation("top.yukonga.miuix.kmp:miuix-glass:0.9.5-rc01") // optional Glass family
            implementation("top.yukonga.miuix.kmp:miuix-nav:0.9.5-rc01") // optional
        }
        desktopMain.dependencies {
            implementation("top.yukonga.miuix.kmp:miuix-window:0.9.5-rc01") // optional; Desktop JVM only
        }
    }
}
```

**Pure Android project:**

```kotlin
// module build.gradle.kts — ensure mavenCentral() is configured
android {
    compileSdk {
        version = release(37) {
            minorApiLevel = 2
        }
    }
    defaultConfig {
        minSdk = 24
    }
}

dependencies {
    implementation("top.yukonga.miuix.kmp:miuix-ui-android:0.9.5-rc01")
    implementation("top.yukonga.miuix.kmp:miuix-preference-android:0.9.5-rc01") // optional
    implementation("top.yukonga.miuix.kmp:miuix-icons-android:0.9.5-rc01") // optional
    implementation("top.yukonga.miuix.kmp:miuix-glass-icons-android:0.9.5-rc01") // optional Glass icons
    implementation("top.yukonga.miuix.kmp:miuix-material-android:0.9.5-rc01") // optional direct material types
    implementation("top.yukonga.miuix.kmp:miuix-glass-android:0.9.5-rc01") // optional Glass family
    implementation("top.yukonga.miuix.kmp:miuix-nav-android:0.9.5-rc01") // optional
    // miuix-blur-android and the Android blur dependency of miuix-glass declare minSdk 33;
    // add them only when that target boundary is acceptable.
}
```

`miuix-ui`, `miuix-preference`, `miuix-icons`, `miuix-material`, and `miuix-nav` follow the repository's Android baseline of `minSdk = 24`; `miuix-blur-android` declares `minSdk = 33`. `miuix-glass` targets API 24 and has component-specific non-sampling fallbacks below API 33, but its transitive `miuix-blur` Android artifact still creates the dependency/manifest boundary described by the upstream Glass guide. Keep the dependency artifact aligned with the source set: use the common artifact in `commonMain`, `-android` for a pure Android module, and `miuix-window` only in `desktopMain`. Verify the target project's published version and Compose/Kotlin/AGP compatibility before changing its build versions.

For an Android app with `minSdk < 33` that uses `miuix-glass`, follow the upstream manifest boundary for its transitive blur artifact: add `tools:overrideLibrary="top.yukonga.miuix.kmp.blur"` only after accepting that shader-backed blur is skipped below API 33, and preserve a [readable component-specific fallback](blur-behavior.md#component-behavior). This is a build/runtime constraint, not a promise that Glass blur works on older Android versions.

The manifest entry is placed on the `<uses-sdk>` element, not on a Glass composable:

```xml
<manifest xmlns:tools="http://schemas.android.com/tools">
    <uses-sdk tools:overrideLibrary="top.yukonga.miuix.kmp.blur" />
</manifest>
```

Use this only for the `minSdk < 33` compatibility decision above; it does not make the Android blur implementation available on older releases.

### Android compile SDK requirements

The checked `miuix-ui-android`, `miuix-blur-android`, and `miuix-glass-android` AAR metadata (unchanged in toolchain between beta03 and rc01) declares `minCompileSdk=37` and **`minCompileMinorSdk=2`**, not a decimal-valued `minCompileSdk=37.2`. Use SDK 37 minor API 2 (or a compatible newer compile SDK) and an AGP/SDK installation that supports it. The Android-only snippet above uses the same expanded `compileSdk { version = release(37) { minorApiLevel = 2 } }` shape as the pinned upstream build; place it in the matching Android target DSL for a multiplatform project.

See [Android SDK configuration](https://developer.android.com/build#module-level) for the minor-API DSL. Do not substitute an invented `compileSdkMinor` property or warning suppression for satisfying dependency metadata. If the existing toolchain cannot express or provide that SDK, report the exact build constraint before proposing a scoped toolchain change. Compile SDK, runtime `minSdk`, and the blur manifest override are separate boundaries; the override above does not resolve a compile-SDK mismatch.

## MiuixTheme Setup

Use `ThemeController` for runtime system dark mode and dynamic-color behavior. Do not choose `lightColorScheme()` merely for convenience, because that fixes the app to a light scheme; use a fixed `lightColorScheme()`/`darkColorScheme()` pair only for an intentional fixed palette, a light-only product, or deterministic preview/test content.

```kotlin
@Composable
fun App() {
    val controller = remember { ThemeController(ColorSchemeMode.System) }
    MiuixTheme(controller = controller) {
        Scaffold(
            topBar = { /* Page TopAppBar; tabs can use its bottomContent. */ },
            bottomBar = { /* Shell NavigationBar or FloatingNavigationBar. */ },
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

In the rc01 snapshot's explicit-seed Monet path, `ThemePaletteStyle.Cmf` internally uses spec 2026; palette/spec do not override the ordinary or seedless platform-dynamic paths. See the [theme contract](contracts/theme-and-host.md#theme). `ThemeColorSpec` still has only `Spec2021` and `Spec2025`; there is no public `ThemeColorSpec.Spec2026`. Keep effective-spec labels and exhaustive palette handling consistent with the selected style. Both theme overloads install `ProvideDisplayCutoutFix`; inspect any older application cutout workaround before retaining it. `LocalDisplayCutoutFixEnabled` controls that behavior at the actual theme/window boundary.

> **Supported version**: Only `0.9.5-rc01`, tag `v0.9.5-rc01` at `542463a0863b393a3d6052237daae6c3da217e70`. Verify target resolution, including local substitutions, even when the declared coordinate matches. This Skill does not supply other-release compatibility paths.

## Android Studio Preview

The repository does not provide a first-party `@Preview` sample, so treat Preview as target-tooling integration rather than a guaranteed Miuix runtime environment.

- Use the target source set's normal Preview annotation/dependency and render a page/component with fake deterministic state and no real services.
- Wrap preview content in one `MiuixTheme` with a deterministic Light or Dark `ThemeController`; avoid System/Monet inputs when the preview host cannot supply real platform theme data.
- Add `Scaffold` when previewing an Overlay component because it needs the popup host. Window popups, system back, focus/IME, animation timing, dynamic color, and platform windows still require runtime validation.
- Preview cannot prove shader or blur behavior. Keep the unsupported fallback renderable and validate those effects on an API/platform that supports them.

## Boundary

This file establishes setup facts and library-host assumptions. It does not require the agent to read the Example, visual references, or source-verification reference afterward. Open one of those only when the task contains that separate question. Before returning code, satisfy the [Code Delivery Contract](../SKILL.md#delivery-contract).
