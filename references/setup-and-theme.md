# Project Setup and Theme

## Quick Start

**Compose Multiplatform project** (most common — Android, iOS, Desktop, Web):

```kotlin
// settings.gradle.kts — ensure mavenCentral() is in the repositories block
// build.gradle.kts (commonMain)
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.3")
implementation("top.yukonga.miuix.kmp:miuix-preference:0.9.3") // optional, for settings screens
```

**Pure Android project:**

```kotlin
// build.gradle.kts — ensure mavenCentral() is in the repositories block
implementation("top.yukonga.miuix.kmp:miuix-ui-android:0.9.3")
```

## MiuixTheme Setup

Use `ThemeController` to support system dark mode / dynamic colors. Do NOT hardcode `lightColorScheme()` — it locks the app to light theme.

```kotlin
@Composable
fun App() {
    val controller = remember { ThemeController(ColorSchemeMode.System) }
    MiuixTheme(controller = controller) {
        Scaffold(
            topBar = { /* TopAppBar, NavigationBar, etc. */ },
            bottomBar = { /* NavigationBar, TabRow, etc. */ },
            floatingActionButton = { /* FloatingActionButton */ },
            floatingToolbar = { /* FloatingToolbar */ }
        ) {
            // Screen content
        }
    }
}
```

`ColorSchemeMode` options: `System` / `Light` / `Dark` / `MonetSystem` / `MonetLight` / `MonetDark`.

> **Version pinning**: File paths and component mappings reflect source tree at tag `v0.9.3`. For full setup details (multiplatform, ProGuard, baseline profiles), read `docs/guide/getting-started.md`.
