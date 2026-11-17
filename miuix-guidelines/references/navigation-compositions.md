# Navigation and Action Composition Guide

Use this guide to choose the placement of persistent navigation or host-owned actions. Mandatory state, material, spacing, and adaptive behavior live in [Navigation rules](rules/page-shell.md#navigation-and-adaptive-layout); [page-host rules](rules/page-shell.md#page-hosts-and-shared-wrappers) own slot responsibilities.

## Destination or action?

| User intent | Composition shape | State/host to identify |
|---|---|---|
| Switch top-level destinations | `NavigationBar` in the shell bottom slot | Shell destination/pager owner |
| Same navigation, visually detached from edge | `FloatingNavigationBar` in the same slot | Same owner and shell backdrop policy |
| Side navigation for the chosen layout mode | `NavigationRail` in the shell side layout | Same selection, adaptive policy, content space handoff |
| One page action | Host `floatingActionButton` slot | Page action callback |
| Related page action group | Host `floatingToolbar` slot | Page action group and position policy |
| Transient operation message | Host `snackbarHost` path | Active page/window feedback owner |

The [standard bar material policy](rules/page-shell.md#blur-and-glass) applies to normal bottom/floating navigation. Existing component Defaults determine the actual bar shape and inset behavior after target verification.

## Shell placement

```text
shell: selected destination and navigation callbacks
└── layout policy
    ├── compact: Scaffold.bottomBar → blur wrapper/floating mask → navigation
    └── side: NavigationRail + available content region
        └── active page, using the shell padding handoff
```

A style or layout switch changes chrome placement. Use the [adaptive rules](rules/page-shell.md#navigation-and-adaptive-layout) to assess state continuity and the [inset rules](rules/page-shell.md#insets-and-content-padding) to assess the page handoff. Their exact dimensions are defined there, not repeated in this guide.

The bar and navigation share the same actual-window decision; landscape alone does not select a rail. For floating navigation, inspect built-in item spacing before adding content children: a Spacer becomes another arranged child and can duplicate the bar's own gap. A sibling rail's reserved start side is part of the content handoff, not a second page gutter.

## API handoff and delivery

After choosing the shape, verify only the selected bar/item scopes, floating slot parameters, corner Defaults, and inset flags through `miuix-components`. Public parameter spelling and default values belong in that API contract, not this guide.

For pager-backed navigation, use [Tabs and pagers](rules/page-shell.md#tabs-and-pagers). For competing gestures, use [Gesture dynamics](gesture-dynamics.md). Report route/selection, resized layout, inset, blur-fallback, and rendering evidence using the [page-shell validation boundary](rules/page-shell.md#validation).
