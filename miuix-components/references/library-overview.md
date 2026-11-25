# Miuix Library Overview

Use this reference when the task is about the shape of Miuix as a library: what it provides, how its modules relate, which capability layer fits a requirement, or which dependency is likely needed. This is an orientation document, not an API contract. Do not derive a package, overload, default, or host requirement from this overview alone.

The overview is based on the current Miuix source-audit snapshot, release tag `v0.9.5-rc01` at commit `542463a0863b393a3d6052237daae6c3da217e70` (a published prerelease). The repository is a Compose Multiplatform library and marks its APIs as experimental. Only `0.9.5-rc01` is supported. Confirm its actual resolution and inspect local overrides; do not adapt this map into a different-release catalog.

## The library in layers

Think about Miuix in four layers before choosing a symbol:

1. **Application shell:** `MiuixTheme`, `Scaffold`, app bars, bottom/rail navigation, tabs, and page-hosted transient surfaces.
2. **Content and interaction:** surfaces, cards, text, buttons, fields, selection controls, progress, refresh, tooltips, badges, and floating actions.
3. **Specialized application families:** preference rows, dropdown/spinner menus, navigation runtime, and extended icons.
4. **Visual and platform support:** colors, blur, squircle, material/render primitives, Glass surfaces, animation, utility, and platform-sensitive infrastructure.

The layers are not a required page template. They are a way to locate the capability before asking for an exact call. A page may use only a subset of them, and ordinary Compose primitives remain valid layout tools around Miuix components.

## Modules and dependency decisions

| Module | Library role | Start here when | Important boundary |
|---|---|---|---|
| `miuix-ui` | Core public UI components, theme, colors, page host, common overlays/windows, and basic controls | Building an ordinary Miuix page or looking for a general UI component | It exposes public UI families, but each symbol still needs its declaration and import verified |
| `miuix-preference` | Preference rows plus preference-oriented dropdown, spinner, menu, and popup families | The content is a complete setting row with title/summary and a trailing control or selector | It depends on `miuix-ui`; do not use a Preference merely because a page is called “settings” |
| `miuix-material` | Runtime-shader handles, AGSL fragments, Glass material/style/shape/shadow definitions, and edge strokes | A lower-level render/material type is required directly, or a Glass surface needs a material preset | It is a render-definition layer, not a page component; `miuix-blur` and `miuix-glass` use it transitively |
| `miuix-blur` | Backdrop sampling, blur, progressive blur and color blending; runtime capability checks come from `miuix-material` | A product requirement needs a blur/backdrop effect and a fallback for unsupported targets | Android's blur artifact declares `minSdk 33`; verify target setup and runtime support |
| `miuix-glass` | Glass surfaces, app bars, search overlay/text field, navigation bars/rail, tabs, buttons, floating actions/toolbars, popups, dialogs, sheets, and Glass preferences | The user wants the optional Glass visual family, migration, or coexistence | It depends on `miuix-ui`, `miuix-material`, and `miuix-blur`; only the Scaffold-hosted dialog/sheet/preference variants require the Miuix popup host |
| `miuix-glass-icons` | Generated Glass icon declarations exposed through `MiuixIcons.Glass` | The selected Glass visual family needs a matching icon | It is independent from `miuix-icons`; verify the requested name and weight in source |
| `miuix-squircle` | Squircle shapes and modifiers | A shape is needed independently or a lower-level shape contract is being inspected | `miuix-ui` already exposes this dependency for ordinary UI use; do not add it by habit |
| `miuix-icons` | Extended Miuix icon declarations | `miuix-ui`'s basic icon set does not contain the needed icon | It is independently consumable; verify the requested icon name and weight in source |
| `miuix-nav` | Navigation keys, back stack, destination rendering, transitions, and gesture integration | The application needs a Miuix navigation runtime rather than only navigation chrome | `NavigationBar`/`NavigationRail` present destinations; they do not replace route ownership |
| `miuix-window` | Desktop JVM native window frame, caption bar, dragging, resize, and backdrop integration | A Desktop JVM app needs Miuix window/caption-bar styling | It is `desktopMain` only and is separate from `miuix-ui`'s common `WindowDialog`/popup family |
| `miuix-core` | Shared icon and utility infrastructure used by other modules | A source path or transitive dependency points here during investigation | It is a support module, not the normal starting point for selecting a page component |

The module list follows the current repository's Gradle settings and getting-started guide. The settings include eleven library modules; `miuix-core` is included because it is a real support/publication module, while `miuix-window` is a Desktop JVM source-set module. `miuix-material` owns the render/material layer used by blur and Glass.

## Public capability families

This table is a semantic map. Use it to choose the next route, not to write an import.

| Capability | Representative families | Typical module/source route | Next reference |
|---|---|---|---|
| Page host and theme | `MiuixTheme`, `ThemeController`, `Scaffold` | `miuix-ui/theme`, `miuix-ui/basic` | [Project setup and theme](setup-and-theme.md) |
| Top-level navigation chrome | `TopAppBar`, `SmallTopAppBar`, `NavigationBar`, `FloatingNavigationBar`, `NavigationRail`, `TabRow`, `BreadcrumbBar` | `miuix-ui/basic` | [Component selection](component-selection.md), then [Documentation and integration index](api-contracts.md) |
| Surfaces and grouping | `Surface`, `Card`, `SmallTitle`, `Divider`, `BasicComponent` | `miuix-ui/basic` | [Component selection](component-selection.md), then [Color semantics and visual lookup](color-lookup.md) |
| Actions and input | `Button`, `TextButton`, `IconButton`, `TextField`, `SearchBar`, `Switch`, `Checkbox`, `RadioButton`, `Slider`, `NumberPicker` | `miuix-ui/basic` | [Component selection](component-selection.md), then [Documentation and integration index](api-contracts.md) |
| Status and interaction feedback | `ProgressIndicator`, `Snackbar`, `PullToRefresh`, `Tooltip`, `Badge`, scroll bars | `miuix-ui/basic` | [Component selection](component-selection.md), then source verification for uncovered contracts |
| Floating actions | `FloatingActionButton`, `FloatingToolbar` | `miuix-ui/basic` and `Scaffold` host slots | [Component selection](component-selection.md), then the guidelines composition route for placement |
| Preference rows | `ArrowPreference`, `SwitchPreference`, `CheckboxPreference`, `RadioButtonPreference`, `SliderPreference`, range/selector preferences | `miuix-preference/preference` | [Preference and menu guide](preferences-and-menus.md) |
| Menus and selectors | dropdown items, spinner, dropdown menu, cascading list popup | `miuix-ui/basic` plus `miuix-preference/menu` and `popup` | [Preference and menu guide](preferences-and-menus.md) |
| Page-scoped transient surfaces | `OverlayDialog`, `OverlayBottomSheet`, overlay list/menu/popup families | `miuix-ui/overlay` and related public menu/popup packages | [Overlay and Window catalog](overlays-and-windows.md) |
| Independent window surfaces | `WindowDialog`, `WindowBottomSheet`, window list/menu/popup families | `miuix-ui/window` and related public window packages | [Overlay and Window catalog](overlays-and-windows.md) |
| Glass visual family | `GlassTopAppBar`, `GlassSearchOverlay`, `GlassTextField`, `GlassNavigationBar`, `GlassNavigationRail`, `GlassTabRow`, `GlassIconButton`, `GlassFloatingActionButton`, `GlassFloatingToolbar`, Glass popups/dialogs/sheets | `miuix-glass/glass`, with `Backdrop` from `miuix-blur` and definitions from `miuix-material` | [Glass components](glass.md) |
| Colors and effects | `Colors`, color spaces, blur, progressive blur, squircle, runtime shader/material definitions | `miuix-ui/theme`, `miuix-ui/color`, `miuix-blur`, `miuix-material`, `miuix-squircle` | [Color semantics and visual lookup](color-lookup.md) or [Styling, icons, and effects](styling-icons-and-effects.md) |
| Desktop window frame | `MiuixWindow`, `MiuixDialogWindow`, `MiuixCaptionBar`, `WindowDraggableArea` | `miuix-window/window` (`desktopMain`) | [Desktop window guide](desktop-window.md) |
| Navigation runtime | `NavDisplay`, `NavController`, `NavKey`, transitions, gesture scope | `miuix-nav/nav/*` | [miuix-nav guide](miuix-nav.md) |

The representative names are deliberately not exhaustive. The pinned component index and source map contain the broader family; the declaration controls whether a name is public, which package exports it, and which module supplies it.

## Orientation workflow

For a library-tour or dependency question:

1. Identify the requested capability layer and whether the user needs an overview or code.
2. Start with this overview and name the smallest likely module set.
3. If the semantic role is still unclear, read the matching section of [Component selection](component-selection.md).
4. If the task asks for a call, import, default, or host rule, leave the overview and enter the exact API/source-verification route.
5. If the task asks how several selected components form a page, hand the composition question to `miuix-guidelines`.

When a family has meaningful variants—such as Overlay versus Window, Dropdown versus Spinner, or a Preference row versus a standalone control—this overview is allowed to stop with the family boundary. Do not invent the variant without the required lifetime, content, or interaction semantics; record the missing decision and inspect the focused reference or ask for it.

Do not answer a library-tour question by dumping every source directory. Do not answer an exact API question from this overview.

## Evidence boundary

- The Miuix root README and getting-started guide establish the library purpose, supported platforms, application-facing modules, and dependency examples.
- The pinned Gradle settings and module build files establish module inclusion and inter-module dependency boundaries.
- The pinned `docs/components/index.md` establishes the documented public component families.
- Kotlin declarations establish public visibility, package, overloads, defaults, opt-in, and host behavior.
- The Example demonstrates integration patterns and state flow; it is not a substitute for any declaration.

When these sources disagree, use the pinned declaration for API facts, the build files for module facts, and the Example only for composition evidence.
