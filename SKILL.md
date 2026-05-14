---
name: miuix
description: Miuix (HyperOS) Compose UI component library expert. Use this skill when the user asks to build UI with Miuix components, mentions Xiaomi/HyperOS design style, or references specific Miuix component names (NavigationBar, SwitchPreference, Scaffold, OverlayDialog, ArrowPreference, etc.). In the miuix-main project, Miuix is the default UI toolkit — use this skill for UI work here UNLESS the user explicitly asks for Material 3, MD3, or another design system. Never force Miuix when the user wants standard Compose Material components.
version: "0.9.1"
---

# Miuix Skill

## What Miuix Is

Miuix is a Compose Multiplatform UI component library that implements Xiaomi HyperOS design language. It targets Android, iOS, Desktop (JVM), macOS, and Web (Wasm/JS) from a single Kotlin codebase.

**Modules:**
| Module | Purpose |
|--------|---------|
| `miuix-ui` | Core UI components — Button, Switch, TextField, NavigationBar, Scaffold, dialogs, etc. |
| `miuix-preference` | Settings-screen components — SwitchPreference, CheckboxPreference, dropdown selectors, etc. |
| `miuix-icons` | 100+ extended icons in 5 weights (Light/Normal/Regular/Medium/Demibold) |
| `miuix-blur` | Backdrop blur effects (Android minSdk=31) |
| `miuix-navigation3-ui` | Jetpack Navigation 3 UI integration |

**Key concepts:**
- Every Miuix UI must be wrapped in `MiuixTheme { ... }` which provides `MiuixTheme.colorScheme.*` and `MiuixTheme.textStyles.*`
- Overlay components (OverlayDialog, OverlayBottomSheet, OverlayListPopup, etc.) must be hosted inside a `Scaffold`. Window components (WindowDialog, WindowBottomSheet, etc.) are standalone.
- Components follow a consistent API pattern: required params → `modifier` → boolean flags → visual params (cornerRadius, colors, etc.) → content lambda
- Each component has a `ComponentDefaults` object with default dimensions, corner radii, and a `@Composable` color factory function

A minimal Miuix screen looks like this:

```kotlin
@Composable
fun App() = MiuixTheme(colorScheme = lightColorScheme()) {
    Scaffold {
        Text("Hello Miuix", style = MiuixTheme.textStyles.body)
    }
}
```

### Public Release vs. Main Branch

This skill targets **Miuix v0.9.1** (the latest stable release). Install via:

```kotlin
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.1")
```

> **Version pinning**: File paths and component mappings reflect source tree at tag `v0.9.1`.

## Source Configuration

This skill relies on a local configuration file `config.json` located in the same directory as this `SKILL.md` file. It records the user's preference and local source path.

The structure of `config.json` is as follows:

```json
{
  "mode": "",
  "source_path": ""
}
```

- `mode`: Can be `"full"` (reads files directly from local clone) or `"lightweight"` (reads files online).
- `source_path`: The absolute path to a local `miuix` repository clone (used when `mode` is `"full"`).

| Source | Usage |
|--------|-------|
| Local clone | Read files directly from `{source_path}/<relative-path>` |
| GitHub — read a **single file** | `https://raw.githubusercontent.com/compose-miuix-ui/miuix/v0.9.1/<file-path>` e.g. `.../v0.9.1/docs/index.md` |
| GitHub — browse a **directory** | `https://github.com/compose-miuix-ui/miuix/tree/v0.9.1/<dir-path>` |
| Rendered docs site | `https://compose-miuix-ui.github.io/miuix/<path>` (strip `.md`, `index.md` → `/`)

## Initialization Flow

To provide a seamless experience, follow this initialization flow to load or set up user preferences before answering their request.

### Step 0: Read Configuration
Read the `config.json` file in the same directory as this skill.
- If `mode` is already set to a valid value (`full` or `lightweight`), and `source_path` is configured (if `full`), **load these settings, skip the setup questions, and proceed directly to Step 3**.
- If `mode` is empty or `source_path` is missing when it shouldn't be, proceed to Step 1.

### Step 1: Offer source setup
If the configuration is empty, tell the user:
> "I can work in two modes: **lightweight** (no source clone, quick answers from docs and my own knowledge) or **full** (local source clone for precise API verification). Which do you prefer?"

- **Lightweight**: Skip cloning. You will read docs from `https://compose-miuix-ui.github.io/miuix/` and source files via `raw.githubusercontent.com`.
- **Full**: Best for production work where API precision matters. Proceed to Step 2.

### Step 2: Clone the source & Update Configuration (full mode only)
1. **"Have you already cloned the Miuix source code to your local machine?"**
   - **If YES**: Ask the user for the absolute path. Validate that `docs/index.md` exists at that path.
   - **If NO**: Ask "May I clone it from `https://github.com/compose-miuix-ui/miuix.git`?" If they agree, run `git clone https://github.com/compose-miuix-ui/miuix.git <target_path>`, then change directory into it and run `git checkout v0.9.1` to guarantee API compatibility with this skill.

To ensure the user doesn't have to answer these setup questions again in future conversations, use a tool to save the `mode` and `source_path` to `config.json` once they are determined.

### Step 3: Bootstrap knowledge (full mode) / start helping (lightweight)
If full mode, briefly read:
- `{source_path}/docs/index.md` — what Miuix is
- `{source_path}/docs/guide/getting-started.md` — dependency setup

If lightweight mode, skip reading and proceed. 

Tell the user: "Miuix skill is ready. Tell me which component you want to use (e.g., 'NavigationBar', 'SwitchPreference', 'OverlayDialog') and I'll give you accurate code."

---

## Path Convention

All file paths in the tables below are relative. Resolve them against the appropriate base depending on mode:

- **Full mode**: prepend `{source_path}/`
- **Lightweight mode**: for each source file, fetch `https://raw.githubusercontent.com/compose-miuix-ui/miuix/v0.9.1/<file-path>` individually (raw URL serves single files only — it cannot list directories). For rendered docs, use `https://compose-miuix-ui.github.io/miuix/<path>` (strip `.md`; `index.md` → `/`). Need to explore a directory? Use `https://github.com/compose-miuix-ui/miuix/tree/v0.9.1/<dir-path>`.

| Abbreviation | Relative path |
|---|---|
| `miuix-ui/.../basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/` |
| `miuix-ui/.../overlay/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/` |
| `miuix-ui/.../window/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/window/` |
| `miuix-ui/.../layout/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/layout/` |
| `miuix-ui/.../theme/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/` |
| `miuix-ui/.../utils/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/utils/` |
| `miuix-ui/.../icon/basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/basic/` |
| `miuix-preference/.../preference/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/` |
| `miuix-preference/.../menu/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/menu/` |
| `miuix-preference/.../popup/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/popup/` |
| `miuix-core/.../icon/` | `miuix-core/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/` |
| `miuix-icons/.../extended/` | `miuix-icons/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/extended/` |
| `docs/components/` | `docs/components/` |
| `docs/demo/` | `docs/demo/src/commonMain/kotlin/` |

---

## Tiered Reading Strategy

The depth of reading depends on the user's intent — don't read more than needed. Before reading files, classify the request:

| Intent | Signs | Read |
|---|---|---|
| **Project setup** | "How do I add Miuix to my project", "What's the Gradle dependency", "How to set up the theme" | `docs/guide/getting-started.md` — then follow the setup steps it describes |
| **Vague need** | "I want a settings page", "I need a popup menu", "build a login form" — describes a *scenario*, not a component name | `docs/components/index.md` to match the need to components via the Description and Common Usage columns |
| **Quick info** | "What is X", "What parameters does it have", "What components exist" | Doc only |
| **Usage help** | "How do I use X", "Give me an example", "Write a page with X" | Doc + Demo |
| **Deep dive** | "How is X implemented internally", "Why does it behave this way", debugging | Doc + Demo + Source |

If unsure, start with the doc — it's cheap, and you can always read more if the user's need proves deeper.

---

## Component Reference Table

When the user asks about a component, first match it case-insensitively against this table, then read files at the depth appropriate to the user's intent.

### Scaffold

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| Scaffold | `docs/components/scaffold.md` | `docs/demo/ScaffoldDemo.kt` | `miuix-ui/.../basic/Scaffold.kt` |

### Basic Components (miuix-ui)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| Surface | `docs/components/surface.md` | `docs/demo/SurfaceDemo.kt` | `miuix-ui/.../basic/Surface.kt` |
| TopAppBar | `docs/components/topappbar.md` | `docs/demo/TopAppBarDemo.kt` | `miuix-ui/.../basic/TopAppBar.kt` |
| NavigationBar | `docs/components/navigationbar.md` | `docs/demo/NavigationBarDemo.kt` | `miuix-ui/.../basic/NavigationBar.kt` |
| NavigationRail | `docs/components/navigationrail.md` | `docs/demo/NavigationRailDemo.kt` | `miuix-ui/.../basic/NavigationRail.kt` |
| TabRow | `docs/components/tabrow.md` | `docs/demo/TabRowDemo.kt` | `miuix-ui/.../basic/TabRow.kt` |
| Card | `docs/components/card.md` | `docs/demo/CardDemo.kt` | `miuix-ui/.../basic/Card.kt` |
| BasicComponent | `docs/components/basiccomponent.md` | `docs/demo/BasicComponentDemo.kt` | `miuix-ui/.../basic/Component.kt` |
| Button | `docs/components/button.md` | `docs/demo/ButtonDemo.kt` | `miuix-ui/.../basic/Button.kt` |
| IconButton | `docs/components/iconbutton.md` | `docs/demo/IconButtonDemo.kt` | `miuix-ui/.../basic/IconButton.kt` |
| Text | `docs/components/text.md` | `docs/demo/TextDemo.kt` | `miuix-ui/.../basic/Text.kt` |
| SmallTitle | `docs/components/smalltitle.md` | `docs/demo/SmallTitleDemo.kt` | `miuix-ui/.../basic/SmallTitle.kt` |
| TextField | `docs/components/textfield.md` | `docs/demo/TextFieldDemo.kt` | `miuix-ui/.../basic/TextField.kt` |
| Switch | `docs/components/switch.md` | `docs/demo/SwitchDemo.kt` | `miuix-ui/.../basic/Switch.kt` |
| Checkbox | `docs/components/checkbox.md` | `docs/demo/CheckboxDemo.kt` | `miuix-ui/.../basic/Checkbox.kt` |
| RadioButton | `docs/components/radiobutton.md` | `docs/demo/RadioButtonDemo.kt` | `miuix-ui/.../basic/RadioButton.kt` |
| Slider | `docs/components/slider.md` | `docs/demo/SliderDemo.kt` | `miuix-ui/.../basic/Slider.kt` |
| NumberPicker | `docs/components/numberpicker.md` | `docs/demo/NumberPickerDemo.kt` | `miuix-ui/.../basic/NumberPicker.kt` |
| ProgressIndicator | `docs/components/progressindicator.md` | `docs/demo/ProgressIndicatorDemo.kt` | `miuix-ui/.../basic/ProgressIndicator.kt` |
| Snackbar | `docs/components/snackbar.md` | `docs/demo/SnackbarDemo.kt` | `miuix-ui/.../basic/Snackbar.kt` |
| Icon | `docs/components/icon.md` | `docs/demo/IconDemo.kt` | `miuix-ui/.../basic/Icon.kt` |
| FloatingActionButton | `docs/components/floatingactionbutton.md` | `docs/demo/FloatingActionButtonDemo.kt` | `miuix-ui/.../basic/FloatingActionButton.kt` |
| FloatingToolbar | `docs/components/floatingtoolbar.md` | `docs/demo/FloatingToolbarDemo.kt` | `miuix-ui/.../basic/FloatingToolbar.kt` |
| Divider | `docs/components/divider.md` | `docs/demo/DividerDemo.kt` | `miuix-ui/.../basic/Divider.kt` |
| PullToRefresh | `docs/components/pulltorefresh.md` | `docs/demo/PullToRefreshDemo.kt` | `miuix-ui/.../basic/PullToRefresh.kt` |
| SearchBar | `docs/components/searchbar.md` | `docs/demo/SearchBarDemo.kt` | `miuix-ui/.../basic/SearchBar.kt` |
| ColorPalette | `docs/components/colorpalette.md` | `docs/demo/ColorPaletteDemo.kt` | `miuix-ui/.../basic/ColorPalette.kt` |
| ColorPicker | `docs/components/colorpicker.md` | `docs/demo/ColorPickerDemo.kt` | `miuix-ui/.../basic/ColorPicker.kt` |
| ScrollBar | — (no dedicated doc) | — (no dedicated demo) | `miuix-ui/.../basic/ScrollBar.kt` |

> 
### Overlay Components (require Scaffold wrapper)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDialog | `docs/components/overlaydialog.md` | `docs/demo/OverlayDialogDemo.kt` | `miuix-ui/.../overlay/OverlayDialog.kt` |
| OverlayBottomSheet | `docs/components/overlaybottomsheet.md` | `docs/demo/OverlayBottomSheetDemo.kt` | `miuix-ui/.../overlay/OverlayBottomSheet.kt` |
| OverlayListPopup | `docs/components/overlaylistpopup.md` | `docs/demo/OverlayListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayListPopup.kt` |
| OverlayCascadingListPopup | `docs/components/overlaycascadinglistpopup.md` | `docs/demo/OverlayCascadingListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayCascadingListPopup.kt` |

### Window Components (standalone popups, no Scaffold needed)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| WindowDialog | `docs/components/windowdialog.md` | `docs/demo/WindowDialogDemo.kt` | `miuix-ui/.../window/WindowDialog.kt` |
| WindowBottomSheet | `docs/components/windowbottomsheet.md` | `docs/demo/WindowBottomSheetDemo.kt` | `miuix-ui/.../window/WindowBottomSheet.kt` |
| WindowListPopup | `docs/components/windowlistpopup.md` | `docs/demo/WindowListPopupDemo.kt` | `miuix-ui/.../window/WindowListPopup.kt` |
| WindowCascadingListPopup | `docs/components/windowcascadinglistpopup.md` | `docs/demo/WindowCascadingListPopupDemo.kt` | `miuix-ui/.../window/WindowCascadingListPopup.kt` |

### Preference Components (miuix-preference)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| ArrowPreference | `docs/components/arrowpreference.md` | `docs/demo/ArrowPreferenceDemo.kt` | `miuix-preference/.../preference/ArrowPreference.kt` |
| SwitchPreference | `docs/components/switchpreference.md` | `docs/demo/SwitchPreferenceDemo.kt` | `miuix-preference/.../preference/SwitchPreference.kt` |
| CheckboxPreference | `docs/components/checkboxpreference.md` | `docs/demo/CheckboxPreferenceDemo.kt` | `miuix-preference/.../preference/CheckboxPreference.kt` |
| RadioButtonPreference | `docs/components/radiobuttonpreference.md` | `docs/demo/RadioButtonPreferenceDemo.kt` | `miuix-preference/.../preference/RadioButtonPreference.kt` |
| OverlayDropdownPreference | `docs/components/overlaydropdownpreference.md` | `docs/demo/OverlayDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/OverlayDropdownPreference.kt` |
| OverlaySpinnerPreference | `docs/components/overlayspinnerpreference.md` | `docs/demo/OverlaySpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/OverlaySpinnerPreference.kt` |
| WindowDropdownPreference | `docs/components/windowdropdownpreference.md` | `docs/demo/WindowDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/WindowDropdownPreference.kt` |
| WindowSpinnerPreference | `docs/components/windowspinnerpreference.md` | `docs/demo/WindowSpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/WindowSpinnerPreference.kt` |

### Dropdown Menu Components (miuix-preference/menu/)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDropdownMenu | `docs/components/overlaydropdownmenu.md` | `docs/demo/OverlayDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayDropdownMenu.kt` |
| OverlayIconDropdownMenu | `docs/components/overlayicondropdownmenu.md` | `docs/demo/OverlayIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconDropdownMenu.kt` |
| OverlayIconCascadingDropdownMenu | `docs/components/overlayiconcascadingdropdownmenu.md` | `docs/demo/OverlayIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconCascadingDropdownMenu.kt` |
| WindowDropdownMenu | `docs/components/windowdropdownmenu.md` | `docs/demo/WindowDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowDropdownMenu.kt` |
| WindowIconDropdownMenu | `docs/components/windowicondropdownmenu.md` | `docs/demo/WindowIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconDropdownMenu.kt` |
| WindowIconCascadingDropdownMenu | `docs/components/windowiconcascadingdropdownmenu.md` | `docs/demo/WindowIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconCascadingDropdownMenu.kt` |

---

## Guide & Theme Resources

| Topic | Guide | Key Source Files |
|-------|-------|-----------------|
| Getting Started | `docs/guide/getting-started.md` | — |
| Icons | `docs/guide/icons.md` | `miuix-core/.../icon/MiuixIcons.kt`, `miuix-ui/.../icon/basic/`, `miuix-icons/.../extended/` |
| Colors | `docs/guide/colors.md` | `miuix-ui/.../theme/Colors.kt` |
| Theme + ThemeController | `docs/guide/theme.md` | `miuix-ui/.../theme/MiuixTheme.kt`, `miuix-ui/.../theme/ThemeController.kt` |
| TextStyles | `docs/guide/textstyles.md` | `miuix-ui/.../theme/TextStyles.kt` |
| Utils | `docs/guide/utils.md` | `miuix-ui/.../utils/` |
| Blur | `docs/guide/blur.md` | `miuix-blur/src/commonMain/kotlin/` |
| Multiplatform | `docs/guide/multiplatform.md` | — |
| Navigation3 | `docs/guide/navigation3.md` | — |
| Best Practices | `docs/guide/best-practices.md` | — |

## Supporting Internals

These files underpin multiple components. They're read-on-demand, not listed per component above:

| File | Role |
|------|------|
| `miuix-ui/.../basic/Component.kt` | `BasicComponent` — universal base for all preference and overlay components |
| `miuix-ui/.../utils/MiuixPopupUtils.kt` | Popup position/anchor utilities for all overlay and window popups |
| `miuix-ui/.../layout/DialogContentLayout.kt` | Shared dialog content layout |
| `miuix-ui/.../layout/BottomSheetContentLayout.kt` | Shared bottom sheet content layout |
| `miuix-ui/.../layout/ListPopupLayout.kt` | Shared list popup content layout |
| `miuix-ui/.../layout/CascadingListPopupLayout.kt` | Shared cascading list popup layout |
| `miuix-ui/.../layout/CascadingMorphContent.kt` | Shared cascading morph content |
| `miuix-ui/.../layout/MorphHeaderRow.kt` | Shared morph header row |
| `miuix-preference/.../popup/DropdownEntriesContent.kt` | Internal dropdown entries content |
| `miuix-ui/.../basic/Dropdown.kt` | `DropdownImpl` and `SpinnerItemImpl` — internal dropdown/spinner building blocks used by preference selectors |
| `miuix-ui/.../basic/ListPopup.kt` | `ListPopupContent`, `ListPopupColumn`, `rememberListPopupLayoutInfo` — internal list popup building blocks |

---

## Example App

`{source_path}/example/` is a full Compose Multiplatform app built with Miuix. It's the best place to see how components, icons, colors, and themes work together in a real application context.

| Directory | What's inside |
|-----------|--------------|
| `example/shared/src/commonMain/kotlin/component/` | One demo screen per component — real usage with MiuixTheme, Scaffold, and navigation |
| `example/shared/src/commonMain/kotlin/` | App entry point, theme setup, main navigation graph |

When the user asks "what does this look like in a real app" or you want to verify how components compose together, browse the relevant screen under `example/shared/.../component/`.

---

## Guardrails

These rules prevent the most common failure mode: fabricating APIs that don't exist.

### Why this matters

Miuix is an independent library — its API surface is different from Material Design, Material 3, or any other Compose library you may know. Guessing parameter names, icon names, or color tokens from memory of those other libraries will produce wrong code that doesn't compile. Always verify against the actual source files.

### 为什么不能从其他库的惯性猜测

Miuix 的 API 命名与 Material Design、Material 3 等库完全不同。图标名、参数名、颜色 token 如果凭其他库的使用习惯猜测，编译必然失败。始终从源文件或文档验证。具体而言：

- **Icon names**: 基础图标 5 个（ArrowRight, ArrowUpDown, Check, Search, SearchCleanup，仅 Regular 粗细）+ 扩展图标 100+（含 5 种粗细：Light, Normal, Regular, Medium, Demibold）。`MiuixIcons.SomeName` 默认访问 Regular；其他粗细用 `MiuixIcons.Light.SomeName` 等。权威来源：`docs/guide/icons.md`（列表）、`miuix-icons/.../extended/<Name>.kt`（验证）。
- **Parameter names & API signatures**: 文档给概览，demo 给可运行的用法，源文件是权威。简单用法看文档+demo 即可；需要 demo 未覆盖的参数或不确定签名时，读源文件。
- **Color token names**: 只用 `MiuixTheme.colorScheme.*` 中定义的属性，在 `miuix-ui/.../theme/Colors.kt`。
- **TextStyle names**: 只用 `MiuixTheme.textStyles.*` 中定义的属性，在 `miuix-ui/.../theme/TextStyles.kt`。

### Always do

- **Wrap in MiuixTheme**: Every Miuix screen must be wrapped in `MiuixTheme { ... }`.
- **Scaffold for overlays**: Overlay* components need a `Scaffold` ancestor. Window* components don't.
- **Read before writing**: When the user asks to use a component, read at least the doc. When they ask for a concrete code example, read the doc + demo. When they ask about internals or edge-case behavior, read doc + demo + source.
- **Follow Miuix API conventions when writing code**: All Miuix composables follow a consistent signature pattern — required parameters first, then `modifier: Modifier = Modifier`, then boolean flags, then visual parameters (cornerRadius, colors, etc.), then content lambda last. Each component has a `ComponentDefaults` companion object with standard dimensions and a `@Composable` color factory. Use `MiuixTheme.colorScheme.*` for colors and `MiuixTheme.textStyles.*` for typography. Use `RoundedCornerShape(cornerRadius)` for rounded corners.
