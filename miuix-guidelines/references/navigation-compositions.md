# Navigation and Action Compositions

Use when the task changes persistent bottom navigation, compact/wide navigation chrome, or a host-owned floating action/action group.

Do not use this guide for a page-local action row that belongs inside the content, or for a gesture conflict involving navigation and scrolling. Keep route state in the shell/route owner and use [Gesture dynamics](gesture-dynamics.md) for competing gesture participants.

Navigation chrome changes presentation, not route ownership. The host slot supplies the page's effective inset boundary; do not manually overlay navigation over a scrolling list unless overlap is an intentional product requirement.

Before implementation, verify the selected navigation/action declarations and slot contract through miuix-components. After implementation, check route restoration, selected-state continuity, compact/wide layout, and the final content inset separately from source or compilation evidence.

## Fixed bottom navigation

Use `NavigationBar` when compact navigation is part of the persistent shell. Keep the selected destination and destination click callback in the route/screen owner, then present them in the bar. If the shell already owns a bottom bar, a page `Scaffold` may still own its top bar; pass the shell's effective padding into the page content instead of moving navigation into the page's scroll content.

```kotlin
Scaffold(
    bottomBar = {
        NavigationBar {
            destinations.forEachIndexed { index, destination ->
                NavigationBarItem(
                    selected = selectedIndex == index,
                    onClick = { onDestinationClick(index) },
                    icon = destination.icon,
                    label = destination.label,
                )
            }
        }
    },
) { innerPadding ->
    // The primary page content consumes innerPadding once.
}
```

`NavigationBarItem` is called inside the `NavigationBar` content scope. Use it for two to five destinations as documented by the component. The bar displays route state; it must not maintain a separate page stack.

## Floating bottom navigation

Use `FloatingNavigationBar` when the same persistent bottom-navigation role should be visually separated from the window edge. It is still navigation chrome, so place it in the `Scaffold(bottomBar = ...)` belonging to the shell that owns navigation, not as an arbitrary `Box` overlay above the list.

```kotlin
Scaffold(
    bottomBar = {
        FloatingNavigationBar {
            destinations.forEachIndexed { index, destination ->
                FloatingNavigationBarItem(
                    selected = selectedIndex == index,
                    onClick = { onDestinationClick(index) },
                    icon = destination.icon,
                    label = destination.label,
                )
            }
        }
    },
) { innerPadding ->
    // Apply innerPadding to the primary content boundary.
}
```

The component supplies its own floating bottom spacing. Keep `defaultWindowInsetsPadding = true` unless the surrounding shell has a deliberate caption-bar inset policy; disabling it transfers only that caption-bar inset responsibility, not the component's own floating spacing. Do not compensate with unrelated hard-coded bottom padding. The public customization parameter is spelled `horizontalOutSidePadding`.

## Adaptive bottom navigation and rail

Use one navigation state and one layout policy when switching between compact and wide navigation chrome:

```text
route/screen owner
├── selected destination and navigation callbacks
└── one compact/wide policy
    ├── compact: NavigationBar or FloatingNavigationBar in Scaffold.bottomBar
    └── wide: NavigationRail in the shell's side slot
```

The active page, selected destination, and business state must survive the branch. The branch changes chrome placement, not navigation ownership. Keep the page's primary content and its inset boundary consistent across both forms; only the shell layout should change unless the available width requires an intentional content reflow.

## Floating action and toolbar

Use the `Scaffold` slots for actions that float relative to page content:

| Intent | Slot/component direction | Placement rule |
|---|---|---|
| One primary action | `floatingActionButton` with `FloatingActionButton` | Let the host position it; do not pin it inside the last list item |
| A related group of actions | `floatingToolbar` with `FloatingToolbar` | Keep the group in the host and select the appropriate toolbar position |
| A destination switcher | `bottomBar` with `NavigationBar` or `FloatingNavigationBar` | Do not model navigation as a floating action |
| A page-scoped message | `snackbarHost`/Snackbar path | Keep transient message state with the page host |

Do not use a floating toolbar as a substitute for bottom navigation, and do not make a single primary action into a toolbar just because both can float.
