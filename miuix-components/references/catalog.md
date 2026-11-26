# Miuix Library Catalog

Use when selecting a module or component, mapping a visual need to a public family, or locating the pinned official doc (`docs/...`) and Kotlin source path. See [SKILL.md](../SKILL.md#documentation-first-resolution) for resolving paths at commit `542463a0863b393a3d6052237daae6c3da217e70`.

Contents: [modules](#modules) · [semantic cues](#semantic-cues-for-vague-requests) · [package imports](#package-import-map-topyukongamiuixkmp) · [component index](#component-reference-table) · [API boundaries](#public-api-boundaries-and-low-level-exports)

## Modules

| Module | Role | Important boundary |
|---|---|---|
| `miuix-ui` | Core controls, theme, colors, `Scaffold`, `Overlay*`/`Window*` popups, scroll/pager utils, 7 basic icons | Transitive `miuix-squircle`; does not include `miuix-blur` |
| `miuix-preference` | Preference rows, dropdown/spinner selectors, anchored/cascading menus | Depends on `miuix-ui`; use for complete setting rows or menus, not merely "settings" screens |
| `miuix-glass` | Optional translucent Glass bars, search overlay, text field, nav, tabs, actions, popups, dialogs, sheets, `GlassCard` | Depends on `miuix-ui`, `miuix-material`, `miuix-blur`; only dialog/sheet/preference use `Scaffold` popup host |
| `miuix-blur` | `Backdrop`, `rememberLayerBackdrop`, `textureBlur`, `progressiveTextureBlur`, `drawBackdrop` | Android artifact declares `minSdk = 33`; `miuix-glass` pulls it transitively |
| `miuix-material` | `RuntimeShader`, `GlassMaterial`, `GlassStyle`, `GlassShape`, `GlassShadow`, `EdgeStroke`, `rememberTiltLightState`, `ShaderBrushCache` | Render/shader definitions layer, not page components |
| `miuix-squircle` | Continuous-corner `squircleBackground`/`Surface`/`Clip`/`Border`, `absoluteSquircle*`, `Path.addSquircleRect` | Already brought in by `miuix-ui`; falls back to rounded corners below Android API 33 |
| `miuix-nav` | `NavKey`, serializable `NavBackStack`, `NavController`, `NavDisplay`, transitions (`Expansion`), swipe-dismiss | Single flat route stack; requires Kotlin Serialization compiler plugin for `rememberNavBackStack` |
| `miuix-window` | Desktop JVM native-styled `MiuixWindow`, `MiuixDialogWindow`, `MiuixCaptionBar`, `WindowDraggableArea` | `desktopMain` only (Windows/macOS/Linux JNA); separate from common `miuix-ui` `Window*` popups |
| `miuix-icons` | Extended Regular/Light/Normal/Medium/Demibold vector icon sets (`MiuixIcons`) | Optional; separate from `miuix-ui`'s 7 Basic icons |
| `miuix-glass-icons` | Generated Glass vector icon sets (`MiuixIcons.Glass`) | Optional and independent from `miuix-icons` |
| `miuix-core` | Shared icon & utility base (`internal`/support layer for other modules) | Not normally imported directly by applications |

## Semantic Cues for Vague Requests

| User describes | Inspect first | Boundary |
|---|---|---|
| Expanding search input with result panel | `SearchBar` + `InputField` | `InputField` exposes `color`; `SearchBar` owns expansion wrapper |
| Full-screen Glass search | `GlassSearchOverlay` | Owns query, focus, Back/Escape, and progressive result header; not a route |
| Input field inside Glass dialog/sheet | `GlassTextField` | `TextField` colors adapted for Glass; relies on `TextFieldState` |
| Persistent bottom navigation (2–5 items) | `NavigationBar` / `FloatingNavigationBar` | Chrome only; does not own routes or back stack |
| Side navigation rail | `NavigationRail` / `GlassNavigationRail` | Ordinary rail has fixed (`expanded`) and expandable (`state != null`) overloads; Glass rail is an overlay morphing into a top capsule |
| Setting row vs selection selector vs popup menu | `SwitchPreference` / `ArrowPreference` / `*DropdownPreference` / `*SpinnerPreference` / `*DropdownMenu` | Plain strings -> `*DropdownPreference`; rich `DropdownItem` -> `*SpinnerPreference`; anchored triggers -> `*DropdownMenu` |
| Popup, dialog, or bottom sheet | `Overlay*` vs `Window*` | Page-scoped under `Scaffold` -> `Overlay*`; cross-page/standalone -> `Window*` |
| Collapsing large title | `TopAppBar` + `MiuixScrollBehavior` | Scroll owner must attach `scrollBehavior.nestedScrollConnection` (or coordinate via `PullToRefresh`) |
| Continuous rounded corners / desktop chrome | `miuix-squircle` / `miuix-window` | `miuix-window` is `desktopMain` only; `squircle*` has no `SquircleShape` class |

## Package Import Map (`top.yukonga.miuix.kmp.*`)

Verify exact subpackages before guessing imports (e.g., `OverlayDialog` is in `.overlay`, not `.basic`; `DialogDefaults` is in `.layout`):

| Subpackage (`top.yukonga.miuix.kmp.*`) | Key public exports |
|---|---|
| `.theme` | `MiuixTheme`, `ThemeController`, `ColorSchemeMode`, `ThemeColorSpec`, `ThemePaletteStyle`, `Colors`, `TextStyles`, `defaultTextStyles`, `LocalDismissState` |
| `.basic` | `Scaffold`, `Surface`, `TopAppBar`, `SmallTopAppBar`, `MiuixScrollBehavior`, `NavigationBar`, `NavigationBarItem`, `FloatingNavigationBar`, `FloatingNavigationBarItem`, `NavigationItem`, `NavigationBarDisplayMode`, `NavigationRail`, `Card`, `CardDefaults`, `BasicComponent`, `Button`, `TextButton`, `ButtonDefaults`, `IconButton`, `FloatingActionButton`, `FloatingToolbar`, `FabPosition`, `ToolbarPosition`, `Text`, `TextField`, `TextFieldDefaults`, `Switch`, `Checkbox`, `RadioButton`, `Slider`, `VerticalSlider`, `RangeSlider`, `NumberPicker`, `TabRow`, `TabRowWithContour`, `SearchBar`, `InputField`, `SearchClearButton`, `PullToRefresh`, `Snackbar`, `SnackbarHost`, `SnackbarHostState`, `DropdownEntry`, `DropdownItem`, `DropdownDefaults`, `DropdownImpl`, `ListPopupColumn`, `ListPopupDefaults`, `PopupPositionProvider`, `SmallTitle`, `HorizontalDivider`, `VerticalDivider`, `DividerDefaults`, `VerticalScrollBar`, `HorizontalScrollBar`, `rememberScrollBarAdapter`, `ScrollBarDefaults` |
| `.overlay` | `OverlayDialog`, `OverlayBottomSheet`, `OverlayListPopup`, `OverlayCascadingListPopup` |
| `.window` (`miuix-ui` commonMain) | `WindowDialog`, `WindowBottomSheet`, `WindowListPopup`, `WindowCascadingListPopup`, `WindowComponentFrame`, `LocalWindowComponentFrame` |
| `.window` (`miuix-window` desktopMain) | `MiuixWindow`, `MiuixDialogWindow`, `MiuixCaptionBar`, `WindowDraggableArea`, `MiuixWindowProperties`, `MiuixWindowBackgroundType`, `LocalMiuixWindowInfo` (button components in `.window.basic`) |
| `.layout` / `.utils` / `.interfaces` | `.layout`: `DialogDefaults`, `DialogMotion`, `BottomSheetDefaults`, `SheetMotion`, `BottomSheetOverlayLayout`; `.utils`: `MiuixPopupHost`, `PagerInterceptionMode`, `pagerGestureOverride`, `PagerNavigationSpringSpec`, `PagerGestureNestedScrollConnection`, `overScrollVertical`, `scrollEndHaptic`, `PressFeedbackType`, `WindowNavigationEventScope`; `.interfaces`: `ExperimentalScrollBarApi` |
| `.preference` / `.menu` (`miuix-preference`) | `.preference`: `*Preference`, `RadioButtonLocation`, `CheckboxLocation`, `RadioButtonPreferenceDefaults`; `.menu`: `OverlayDropdownMenu`, `WindowDropdownMenu`, `OverlayIconDropdownMenu`, `WindowIconDropdownMenu`, `OverlayIconCascadingDropdownMenu`, `WindowIconCascadingDropdownMenu` |
| `.blur` / `.material` / `.squircle` | `.blur`: `Backdrop`, `rememberLayerBackdrop`, `Modifier.layerBackdrop`, `Modifier.drawBackdrop`, `Modifier.textureBlur`, `Modifier.progressiveTextureBlur`, `ProgressiveBlur`; `.material`: `GlassMaterial`, `GlassMaterials`, `EdgeStroke`, `EdgeStrokes`, `GlassEdgeStrokes`, `rememberTiltLightState`, `ShaderBrushCache`, `isRuntimeShaderSupported`, `isRenderEffectSupported` (`rememberDeviceTilt` in `.material.sensor`); `.squircle`: `squircleBackground`, `squircleSurface`, `squircleClip`, `squircleBorder`, `absoluteSquircle*`, `addSquircleRect`, `isSquircleEnabled` |
| `.glass` (`miuix-glass`) | `GlassTopAppBar`, `GlassNavigationBar`, `GlassNavigationItem`, `GlassNavigationRail`, `GlassTabRow`, `GlassSegmentedTabRow`, `GlassIconButton`, `GlassFloatingActionButton`, `GlassFloatingToolbar`, `GlassSearchOverlay`, `GlassTextField`, `GlassDialog`, `GlassDialogButton`, `GlassDialogButtonRole`, `GlassBottomSheet`, `GlassCard`, `GlassCardDefaults`, `GlassDropdownPreference`, `glassDialogSurface`, `glassBottomSheetSurface`, `glassSurface`, `Modifier.glassPanel`, `Modifier.glass`, `GlassMotion` |
| `.nav.*` (`miuix-nav`) | `.nav.core`: `NavKey`, `NavBackStack`, `rememberNavBackStack`, `navBackStackOf`, `NavController`, `rememberNavController`, `NavDisplay`, `NavDisplayEffects`, `LocalNavTransitionScope`, `rememberNavSystemCornerRadius`; `.nav.transition`: `NavTransitions`, `NavMotion`, `NavSwipeDirection`, `Modifier.navTransitionSource`, `navDirectionalTransition`, `navGraphicsTransition`; `.nav.gesture`: `Modifier.navSwipeDismiss` |
| `.icon.*` | `top.yukonga.miuix.kmp.icon.MiuixIcons`, Basic in `.icon.basic.*`, Extended (`miuix-icons`) in `.icon.extended.*`, Glass (`miuix-glass-icons`) in `.icon.glass.*` |

## Component Reference Table

Path abbreviations: `docs/demo/<Name>` -> `docs/demo/src/commonMain/kotlin/<Name>`; `miuix-ui/.../` -> `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/`; `miuix-preference/.../` -> `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/`; `miuix-glass/.../` -> `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/`.

### `miuix-ui` Core & Host

| Component | Doc | Demo / Example | Source | Local reference |
|---|---|---|---|---|
| Scaffold | `docs/components/basic/scaffold.md` | `docs/demo/ScaffoldDemo.kt` | `miuix-ui/.../basic/Scaffold.kt` | [Setup & theme](setup-and-theme.md#scaffold-host) |
| Surface | `docs/components/basic/surface.md` | `docs/demo/SurfaceDemo.kt` | `miuix-ui/.../basic/Surface.kt` | — |
| TopAppBar / SmallTopAppBar | `docs/components/basic/topappbar.md` | `docs/demo/TopAppBarDemo.kt` | `miuix-ui/.../basic/TopAppBar.kt` | [Bars & scroll](bars-scroll-and-navigation.md#top-app-bar-and-scrollbehavior) |
| NavigationBar / FloatingNavigationBar | `docs/components/basic/navigationbar.md` | `docs/demo/NavigationBarDemo.kt` | `miuix-ui/.../basic/NavigationBar.kt` | [Bars & scroll](bars-scroll-and-navigation.md#navigationbar-and-floatingnavigationbar) |
| NavigationRail | `docs/components/basic/navigationrail.md` | `docs/demo/NavigationRailDemo.kt` | `miuix-ui/.../basic/NavigationRail.kt` | [Catalog cues](#semantic-cues-for-vague-requests) |
| TabRow / TabRowWithContour | `docs/components/basic/tabrow.md` | `docs/demo/TabRowDemo.kt` | `miuix-ui/.../basic/TabRow.kt` | [Bars & scroll](bars-scroll-and-navigation.md#tabrow-and-pager) |
| Card | `docs/components/basic/card.md` | `docs/demo/CardDemo.kt` | `miuix-ui/.../basic/Card.kt` | [Bars & scroll](bars-scroll-and-navigation.md#cards-and-smalltitle) |
| BasicComponent | `docs/components/basic/basiccomponent.md` | `docs/demo/BasicComponentDemo.kt` | `miuix-ui/.../basic/Component.kt` | — |
| Button / TextButton | `docs/components/basic/button.md` | `docs/demo/ButtonDemo.kt` | `miuix-ui/.../basic/Button.kt` | [Controls & menus](controls-and-menus.md#buttons-and-floating-actions) |
| IconButton | `docs/components/basic/iconbutton.md` | `docs/demo/IconButtonDemo.kt` | `miuix-ui/.../basic/IconButton.kt` | [Controls & menus](controls-and-menus.md#buttons-and-floating-actions) |
| Text | `docs/components/basic/text.md` | `docs/demo/TextDemo.kt` | `miuix-ui/.../basic/Text.kt` | — |
| SmallTitle | `docs/components/basic/smalltitle.md` | `docs/demo/SmallTitleDemo.kt` | `miuix-ui/.../basic/SmallTitle.kt` | [Bars & scroll](bars-scroll-and-navigation.md#cards-and-smalltitle) |
| TextField | `docs/components/basic/textfield.md` | `docs/demo/TextFieldDemo.kt` | `miuix-ui/.../basic/TextField.kt` | [Controls & menus](controls-and-menus.md#textfield) |
| Switch | `docs/components/basic/switch.md` | `docs/demo/SwitchDemo.kt` | `miuix-ui/.../basic/Switch.kt` | [Controls & menus](controls-and-menus.md#selection-controls-and-pickers) |
| Checkbox | `docs/components/basic/checkbox.md` | `docs/demo/CheckboxDemo.kt` | `miuix-ui/.../basic/Checkbox.kt` | [Controls & menus](controls-and-menus.md#selection-controls-and-pickers) |
| RadioButton | `docs/components/basic/radiobutton.md` | `docs/demo/RadioButtonDemo.kt` | `miuix-ui/.../basic/RadioButton.kt` | [Controls & menus](controls-and-menus.md#selection-controls-and-pickers) |
| Slider / VerticalSlider / RangeSlider | `docs/components/basic/slider.md` | `docs/demo/SliderDemo.kt` | `miuix-ui/.../basic/Slider.kt` | [Controls & menus](controls-and-menus.md#selection-controls-and-pickers) |
| NumberPicker | `docs/components/basic/numberpicker.md` | `docs/demo/NumberPickerDemo.kt` | `miuix-ui/.../basic/NumberPicker.kt` | [Controls & menus](controls-and-menus.md#selection-controls-and-pickers) |
| ProgressIndicator | `docs/components/basic/progressindicator.md` | `docs/demo/ProgressIndicatorDemo.kt` | `miuix-ui/.../basic/ProgressIndicator.kt` | [Controls & menus](controls-and-menus.md#progress-indicators) |
| Snackbar / SnackbarHost | `docs/components/basic/snackbar.md` | `docs/demo/SnackbarDemo.kt` | `miuix-ui/.../basic/Snackbar.kt` | [Overlays & windows](overlays-and-windows.md#list-popups-and-snackbars) |
| Icon | `docs/components/basic/icon.md` | `docs/demo/IconDemo.kt` | `miuix-ui/.../basic/Icon.kt` | [Effects & icons](effects-and-icons.md#icons) |
| FloatingActionButton | `docs/components/basic/floatingactionbutton.md` | `docs/demo/FloatingActionButtonDemo.kt` | `miuix-ui/.../basic/FloatingActionButton.kt` | [Controls & menus](controls-and-menus.md#buttons-and-floating-actions) |
| FloatingToolbar | `docs/components/basic/floatingtoolbar.md` | `docs/demo/FloatingToolbarDemo.kt` | `miuix-ui/.../basic/FloatingToolbar.kt` | [Controls & menus](controls-and-menus.md#buttons-and-floating-actions) |
| BreadcrumbBar | `docs/components/basic/breadcrumbbar.md` | `docs/demo/BreadcrumbBarDemo.kt` | `miuix-ui/.../basic/BreadcrumbBar.kt` | [Bars & scroll](bars-scroll-and-navigation.md#breadcrumbbar) |
| HorizontalDivider / VerticalDivider | `docs/components/basic/divider.md` | `docs/demo/DividerDemo.kt` | `miuix-ui/.../basic/Divider.kt` | — |
| PullToRefresh | `docs/components/basic/pulltorefresh.md` | `docs/demo/PullToRefreshDemo.kt` | `miuix-ui/.../basic/PullToRefresh.kt` | [Bars & scroll](bars-scroll-and-navigation.md#pulltorefresh-and-overscroll) |
| SearchBar / InputField | `docs/components/basic/searchbar.md` | `docs/demo/SearchBarDemo.kt` | `miuix-ui/.../basic/SearchBar.kt`, `SearchClearButton.kt` | [Controls & menus](controls-and-menus.md#searchbar-inputfield-and-searchclearbutton) |
| ColorPalette | `docs/components/basic/colorpalette.md` | `docs/demo/ColorPaletteDemo.kt` | `miuix-ui/.../basic/ColorPalette.kt` | — |
| ColorPicker | `docs/components/basic/colorpicker.md` | `docs/demo/ColorPickerDemo.kt` | `miuix-ui/.../basic/ColorPicker.kt` | — |
| Tooltip | `docs/components/basic/tooltip.md` | `docs/demo/TooltipDemo.kt` | `miuix-ui/.../basic/Tooltip.kt` | [Overlays & windows](overlays-and-windows.md#back-lifecycle-and-frame-bounds) |
| Badge | `docs/components/basic/badge.md` | `docs/demo/BadgeDemo.kt` | `miuix-ui/.../basic/Badge.kt` | — |
| VerticalScrollBar / HorizontalScrollBar | — (source/Example only) | `example/shared/.../SettingsPage.kt`, `MainPage.kt` | `miuix-ui/.../basic/ScrollBar.kt` | [Bars & scroll](bars-scroll-and-navigation.md#scrollbars) |
| OverlayDialog / OverlayBottomSheet | `docs/components/basic/overlaydialog.md`, `overlaybottomsheet.md` | `docs/demo/OverlayDialogDemo.kt`, `OverlayBottomSheetDemo.kt` | `miuix-ui/.../overlay/OverlayDialog.kt`, `OverlayBottomSheet.kt` | [Overlays & windows](overlays-and-windows.md) |
| OverlayListPopup / OverlayCascadingListPopup | `docs/components/basic/overlaylistpopup.md`, `overlaycascadinglistpopup.md` | `docs/demo/OverlayListPopupDemo.kt`, `OverlayCascadingListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayListPopup.kt`, `OverlayCascadingListPopup.kt` | [Overlays & windows](overlays-and-windows.md) |
| WindowDialog / WindowBottomSheet | `docs/components/basic/windowdialog.md`, `windowbottomsheet.md` | `docs/demo/WindowDialogDemo.kt`, `WindowBottomSheetDemo.kt` | `miuix-ui/.../window/WindowDialog.kt`, `WindowBottomSheet.kt` | [Overlays & windows](overlays-and-windows.md) |
| WindowListPopup / WindowCascadingListPopup | `docs/components/basic/windowlistpopup.md`, `windowcascadinglistpopup.md` | `docs/demo/WindowListPopupDemo.kt`, `WindowCascadingListPopupDemo.kt` | `miuix-ui/.../window/WindowListPopup.kt`, `WindowCascadingListPopup.kt` | [Overlays & windows](overlays-and-windows.md) |

### `miuix-preference` Preferences & Menus

| Component | Doc | Demo | Source | Local reference |
|---|---|---|---|---|
| SwitchPreference | `docs/components/extended/switchpreference.md` | `docs/demo/SwitchPreferenceDemo.kt` | `miuix-preference/.../preference/SwitchPreference.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| ArrowPreference | `docs/components/extended/arrowpreference.md` | `docs/demo/ArrowPreferenceDemo.kt` | `miuix-preference/.../preference/ArrowPreference.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| CheckboxPreference | `docs/components/extended/checkboxpreference.md` | `docs/demo/CheckboxPreferenceDemo.kt` | `miuix-preference/.../preference/CheckboxPreference.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| RadioButtonPreference | `docs/components/extended/radiobuttonpreference.md` | `docs/demo/RadioButtonPreferenceDemo.kt` | `miuix-preference/.../preference/RadioButtonPreference.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| SliderPreference / RangeSliderPreference | `docs/components/extended/sliderpreference.md`, `rangesliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| OverlayDropdownPreference / WindowDropdownPreference | `docs/components/extended/overlaydropdownpreference.md`, `windowdropdownpreference.md` | `docs/demo/OverlayDropdownPreferenceDemo.kt`, `WindowDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/OverlayDropdownPreference.kt`, `WindowDropdownPreference.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| OverlaySpinnerPreference / WindowSpinnerPreference | `docs/components/extended/overlayspinnerpreference.md`, `windowspinnerpreference.md` | `docs/demo/OverlaySpinnerPreferenceDemo.kt`, `WindowSpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/OverlaySpinnerPreference.kt`, `WindowSpinnerPreference.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| OverlayDropdownMenu / WindowDropdownMenu | `docs/components/extended/overlaydropdownmenu.md`, `windowdropdownmenu.md` | `docs/demo/OverlayDropdownMenuDemo.kt`, `WindowDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayDropdownMenu.kt`, `WindowDropdownMenu.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| OverlayIconDropdownMenu / WindowIconDropdownMenu | `docs/components/extended/overlayicondropdownmenu.md`, `windowicondropdownmenu.md` | `docs/demo/OverlayIconDropdownMenuDemo.kt`, `WindowIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconDropdownMenu.kt`, `WindowIconDropdownMenu.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |
| OverlayIconCascadingDropdownMenu / WindowIconCascadingDropdownMenu | `docs/components/extended/overlayiconcascadingdropdownmenu.md`, `windowiconcascadingdropdownmenu.md` | `docs/demo/OverlayIconCascadingDropdownMenuDemo.kt`, `WindowIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconCascadingDropdownMenu.kt`, `WindowIconCascadingDropdownMenu.kt` | [Controls & menus](controls-and-menus.md#preference-and-menu-contracts) |

### `miuix-glass` Optional Glass Family (21 Public UI Components + Modifiers/Adapters)

| Component | Doc | Demo / Example | Source | Local reference |
|---|---|---|---|---|
| GlassTopAppBar | `docs/components/glass/glasstopappbar.md` | `docs/demo/GlassTopAppBarDemo.kt` | `miuix-glass/.../glass/GlassTopAppBar.kt` | [Glass & blur](glass-and-blur.md#glass-bars-navigation-and-tabs) |
| GlassSearchOverlay | `docs/components/glass/glasssearchoverlay.md` | `docs/demo/GlassSearchOverlayDemo.kt` | `miuix-glass/.../glass/GlassSearchOverlay.kt` | [Glass & blur](glass-and-blur.md#glass-search-and-text-input) |
| GlassTextField | `docs/components/glass/glasstextfield.md` | `docs/demo/GlassTextFieldDemo.kt` | `miuix-glass/.../glass/GlassTextField.kt` | [Glass & blur](glass-and-blur.md#glass-search-and-text-input) |
| GlassNavigationBar | `docs/components/glass/glassnavigationbar.md` | `docs/demo/GlassNavigationBarDemo.kt` | `miuix-glass/.../glass/GlassNavigationBar.kt` | [Glass & blur](glass-and-blur.md#glass-bars-navigation-and-tabs) |
| GlassNavigationRail | `docs/components/glass/glassnavigationrail.md` | `docs/demo/GlassNavigationRailDemo.kt` | `miuix-glass/.../glass/GlassNavigationRail.kt` | [Glass & blur](glass-and-blur.md#glass-bars-navigation-and-tabs) |
| GlassTabRow / GlassSegmentedTabRow | `docs/components/glass/glasstabrow.md` | `docs/demo/GlassTabRowDemo.kt` | `miuix-glass/.../glass/GlassTabRow.kt` | [Glass & blur](glass-and-blur.md#glass-bars-navigation-and-tabs) |
| GlassIconButton | `docs/components/glass/glassiconbutton.md` | `docs/demo/GlassIconButtonDemo.kt` | `miuix-glass/.../glass/GlassIconButton.kt` | [Glass & blur](glass-and-blur.md#glass-actions-and-cards) |
| GlassFloatingActionButton / GlassFloatingToolbar | `docs/components/glass/glassfloatingactionbutton.md`, `glassfloatingtoolbar.md` | `docs/demo/GlassFloatingActionButtonDemo.kt`, `GlassFloatingToolbarDemo.kt` | `miuix-glass/.../glass/GlassFloatingActionButton.kt`, `GlassFloatingToolbar.kt` | [Glass & blur](glass-and-blur.md#glass-actions-and-cards) |
| GlassPopup / GlassPopupItem | `docs/components/glass/glasspopup.md` | `docs/demo/GlassPopupDemo.kt` | `miuix-glass/.../glass/GlassPopup.kt` | [Glass & blur](glass-and-blur.md#boxscope-glass-popups) |
| GlassDropdownPopup | `docs/components/glass/glassdropdownpopup.md` | `docs/demo/GlassDropdownPopupDemo.kt` | `miuix-glass/.../glass/GlassDropdownPopup.kt` | [Glass & blur](glass-and-blur.md#boxscope-glass-popups) |
| GlassIconDropdownMenu | — | `example/shared/.../MainPage.kt` | `miuix-glass/.../glass/GlassIconDropdownMenu.kt` | [Glass & blur](glass-and-blur.md#boxscope-glass-popups) |
| GlassTransformPopup / GlassSecondaryPopup | `docs/components/glass/glasstransformpopup.md`, `glasssecondarypopup.md` | `docs/demo/GlassTransformPopupDemo.kt`, `GlassSecondaryPopupDemo.kt` | `miuix-glass/.../glass/GlassTransformPopup.kt`, `GlassSecondaryPopup.kt` | [Glass & blur](glass-and-blur.md#boxscope-glass-popups) |
| GlassDialog / GlassDialogButton | `docs/components/glass/glassdialog.md` | `docs/demo/GlassDialogDemo.kt` | `miuix-glass/.../glass/GlassDialog.kt`, `GlassDialogButton.kt` | [Glass & blur](glass-and-blur.md#glass-overlays-and-glasscard) |
| GlassBottomSheet | `docs/components/glass/glassbottomsheet.md` | `docs/demo/GlassBottomSheetDemo.kt` | `miuix-glass/.../glass/GlassBottomSheet.kt` | [Glass & blur](glass-and-blur.md#glass-overlays-and-glasscard) |
| GlassCard | `docs/components/glass/glasscard.md` | `docs/demo/GlassCardDemo.kt` | `miuix-glass/.../glass/GlassCard.kt` | [Glass & blur](glass-and-blur.md#glass-overlays-and-glasscard) |
| GlassDropdownPreference | `docs/components/glass/glassdropdownpreference.md` | `docs/demo/GlassDropdownPreferenceDemo.kt` | `miuix-glass/.../glass/GlassDropdownPreference.kt` | [Glass & blur](glass-and-blur.md#glass-overlays-and-glasscard) |
| `Modifier.glassPanel`, `glass`, `glassShadow`, `glassPress`, `glassSurface`, `glassDialogSurface`, `glassBottomSheetSurface` | `docs/guide/glass.md` | — | `miuix-glass/.../glass/` | [Glass & blur](glass-and-blur.md#low-level-surfaces-and-adapters) |

### Guides, Navigation, Desktop Window & Utilities

| Area / Topic | Upstream Guide | Demo | Source / Reference |
|---|---|---|---|
| Theme & ThemeController | `docs/guide/theme.md` | — | [Setup, theme & colors](setup-and-theme.md) |
| Color tokens & dynamic color | `docs/guide/colors.md` | — | [Setup, theme & colors](setup-and-theme.md#color-semantics-and-visual-lookup) |
| Text styles | `docs/guide/textstyles.md` | — | [Setup, theme & colors](setup-and-theme.md#typography-miuixthemetextstyles) |
| Icons (Basic / Extended / Glass) | `docs/guide/icons.md` | — | [Effects & icons](effects-and-icons.md#icons) |
| Blur & ProgressiveBlur | `docs/guide/blur.md` | — | [Effects & icons](effects-and-icons.md#custom-blur-pipelines) |
| Material, EdgeStroke & TiltLight | `docs/guide/material.md` | — | [Effects & icons](effects-and-icons.md#material-and-edge-strokes) |
| Squircle | `docs/guide/squircle.md` | — | [Effects & icons](effects-and-icons.md#squircle) |
| Pager gesture & overscroll utilities | `docs/guide/utils.md` | — | [Bars & scroll](bars-scroll-and-navigation.md) |
| `miuix-nav` routing & `Expansion` | `docs/guide/miuix-nav.md` | `docs/demo/NavExpandDemo.kt` | [miuix-nav](miuix-nav.md) |
| `miuix-window` Desktop JVM frame | `docs/guide/miuix-window.md` | — | [Overlays & windows](overlays-and-windows.md#desktop-window-frames-miuix-window) |
| Multiplatform behavior | `docs/guide/multiplatform.md` | — | Verify platform source set directly |

## Public API Boundaries and Low-Level Exports

- **Compose primitives stay Compose**: `LazyColumn`, `LazyRow`, `Row`, `Column`, `Box`, and `AnimatedVisibility` are standard Compose APIs (`MiuixTheme` installs `MiuixOverscrollFactory` onto standard scrollables automatically).
- **`SmallTitle`, not `SectionHeader`**: `rc01` exports no `SectionHeader`; use `SmallTitle`.
- **`TextButton` & `InputField`**: `TextButton` shares `docs/components/basic/button.md`; `InputField` lives in `docs/components/basic/searchbar.md` and exposes `color`, which `SearchBar` does not forward.
- **`ProgressiveBlur` & lowercase Glass helpers**: `ProgressiveBlur` is a `miuix-blur` descriptor, not a `miuix-ui` component. `GlassPanel` / `GlassSurface` are lowercase modifiers/factories (`Modifier.glassPanel`, `glassSurface(...)`), not uppercase composables.
- **Do not treat `*Impl` as internal**: `rc01` publicly exports `DropdownImpl`, `SpinnerItemImpl`, `ListPopupColumn`, `ListPopupContent`, `rememberListPopupLayoutInfo`, `TopAppBarLayout`, `BottomSheetOverlayLayout`, `SearchClearButton`, `DialogMotion`, `SheetMotion`, and `WindowComponentFrame`/`LocalWindowComponentFrame`. Still prefer high-level wrappers when their host/lifecycle is needed.
