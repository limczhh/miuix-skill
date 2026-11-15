# Miuix Library Overview

Use this reference when the task is about the shape of Miuix as a library: what it provides, how its modules relate, which capability layer fits a requirement, or which dependency is likely needed. This is an orientation document, not an API contract. Do not derive a package, overload, default, or host requirement from this overview alone.

The overview is based on Miuix `v0.9.4`, pinned to commit `39c40f99844227b853f0049a0933b1f3ae6c00ba`. The pinned repository is a Compose Multiplatform library and marks its APIs as experimental. If the target project uses another version, use this map only to choose a route, then verify the target source.

## The library in layers

Think about Miuix in four layers before choosing a symbol:

1. **Application shell:** `MiuixTheme`, `Scaffold`, app bars, bottom/rail navigation, tabs, and page-hosted transient surfaces.
2. **Content and interaction:** surfaces, cards, text, buttons, fields, selection controls, progress, refresh, tooltips, badges, and floating actions.
3. **Specialized application families:** preference rows, dropdown/spinner menus, navigation runtime, and extended icons.
4. **Visual and platform support:** colors, blur, squircle, shader, animation, utility, and platform-sensitive infrastructure.

The layers are not a required page template. They are a way to locate the capability before asking for an exact call. A page may use only a subset of them, and ordinary Compose primitives remain valid layout tools around Miuix components.

## Modules and dependency decisions

| Module | Library role | Start here when | Important boundary |
|---|---|---|---|
| `miuix-ui` | Core public UI components, theme, colors, page host, overlays, windows, and basic controls | Building an ordinary Miuix page or looking for a general UI component | It exposes public UI families, but each symbol still needs its declaration and import verified |
| `miuix-preference` | Preference rows plus preference-oriented dropdown, spinner, menu, and popup families | The content is a complete setting row with title/summary and a trailing control or selector | It depends on `miuix-ui`; do not use a Preference merely because a page is called “settings” |
| `miuix-icons` | Extended Miuix icon declarations | `miuix-ui`'s basic icon set does not contain the needed icon | It is independently consumable; verify the requested icon name and weight in tagged source |
| `miuix-nav` | Navigation keys, back stack, destination rendering, transitions, and gesture integration | The application needs a Miuix navigation runtime rather than only navigation chrome | `NavigationBar`/`NavigationRail` present destinations; they do not replace route ownership |
| `miuix-blur` | Blur, progressive blur, highlight, and sensor/platform effect support | A product requirement needs a blur effect and has a fallback for unsupported targets | Android blur has platform constraints; verify the target source set and runtime support |
| `miuix-squircle` | Squircle shapes and modifiers | A shape is needed independently or a lower-level shape contract is being inspected | `miuix-ui` already exposes this dependency for ordinary UI use; do not add it by habit |
| `miuix-shader` | Low-level runtime shader/render-effect abstraction | A low-level effect implementation, not a page component, is required | Prefer a public higher-level effect API when it expresses the requirement |
| `miuix-core` | Shared icon and utility infrastructure used by other modules | A source path or transitive dependency points here during investigation | It is a support module, not the normal starting point for selecting a page component |

The module list follows the pinned repository's Gradle settings and module build files. The root project presents the seven application-facing modules in its getting-started material; `miuix-core` is included here because it is a real source/publication module that supports the others.

## Public capability families

This table is a semantic map. Use it to choose the next route, not to write an import.

| Capability | Representative families | Typical module/source route | Next reference |
|---|---|---|---|
| Page host and theme | `MiuixTheme`, `ThemeController`, `Scaffold` | `miuix-ui/theme`, `miuix-ui/basic` | [Project setup and theme](setup-and-theme.md) |
| Top-level navigation chrome | `TopAppBar`, `SmallTopAppBar`, `NavigationBar`, `FloatingNavigationBar`, `NavigationRail`, `TabRow`, `BreadcrumbBar` | `miuix-ui/basic` | [Component selection](component-selection.md), then [Verified API contracts](api-contracts.md) |
| Surfaces and grouping | `Surface`, `Card`, `SmallTitle`, `Divider`, `BasicComponent` | `miuix-ui/basic` | [Component selection](component-selection.md), then [Color semantics and visual lookup](color-lookup.md) |
| Actions and input | `Button`, `TextButton`, `IconButton`, `TextField`, `SearchBar`, `Switch`, `Checkbox`, `RadioButton`, `Slider`, `NumberPicker` | `miuix-ui/basic` | [Component selection](component-selection.md), then [Verified API contracts](api-contracts.md) |
| Status and interaction feedback | `ProgressIndicator`, `Snackbar`, `PullToRefresh`, `Tooltip`, `Badge`, scroll bars | `miuix-ui/basic` | [Component selection](component-selection.md), then source verification for uncovered contracts |
| Floating actions | `FloatingActionButton`, `FloatingToolbar` | `miuix-ui/basic` and `Scaffold` host slots | [Component selection](component-selection.md), then the guidelines composition route for placement |
| Preference rows | `ArrowPreference`, `SwitchPreference`, `CheckboxPreference`, `RadioButtonPreference`, `SliderPreference`, range/selector preferences | `miuix-preference/preference` | [Preference and menu guide](preferences-and-menus.md) |
| Menus and selectors | dropdown items, spinner, dropdown menu, cascading list popup | `miuix-ui/basic` plus `miuix-preference/menu` and `popup` | [Preference and menu guide](preferences-and-menus.md) |
| Page-scoped transient surfaces | `OverlayDialog`, `OverlayBottomSheet`, overlay list/menu/popup families | `miuix-ui/overlay` and related public menu/popup packages | [Overlay and Window catalog](overlays-and-windows.md) |
| Independent window surfaces | `WindowDialog`, `WindowBottomSheet`, window list/menu/popup families | `miuix-ui/window` and related public window packages | [Overlay and Window catalog](overlays-and-windows.md) |
| Colors and effects | `Colors`, color spaces, blur, progressive blur, squircle, shader | `miuix-ui/theme`, `miuix-ui/color`, `miuix-blur`, `miuix-squircle`, `miuix-shader` | [Color semantics and visual lookup](color-lookup.md) or [Styling, icons, and effects](styling-icons-and-effects.md) |
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
