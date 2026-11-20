# Gesture Dynamics Investigation Guide

Use when scrolling, refresh, overscroll, collapsing bars, tabs/pagers, sheets, or drag behavior interact. The [scroll/refresh rules](rules/page-shell.md#scroll-refresh-and-haptics) define required ownership, modifier order, haptics, and visual channels. This guide explains how to locate the actual participants and diagnose a conflict.

Exact declarations and version behavior live in the companion [scroll/refresh API contract](../../miuix-components/references/contracts/scroll-refresh-and-overscroll.md). A guide sketch does not prove a target's nested-scroll implementation.

## Map participants from the actual source

Trace pointer/wheel/keyboard input through:

```text
primary content movement
→ installed top-bar/refresh connections
→ overscroll effect and spring
→ external visual consumers
→ business refresh action/state
```

For each participant, identify its installed modifier, state object, changing inputs, owner, and callbacks. Useful source entrypoints are the target's `PullToRefresh`, overscroll utility, app-bar state/behavior, shared page helper, and the helper's callers/branch arguments.

| Question | Inspect |
|---|---|
| Who installs the app-bar connection? | Wrapper arguments and actual list/helper modifier construction |
| Does the refresh wrapper coordinate the bar internally? | Target PullToRefresh declaration and implementation |
| Which mechanism produces rebound? | Explicit overscroll modifier and theme/scrollable effect configuration |
| Which value drives a band/header? | Damped offset callback, spring frames, and reset path |
| Which tab is visually active? | Active pager page versus shared bar state |
| Does a drag compete with sheet dismissal? | Drag/list/sheet gesture ownership and nested-scroll settings |

## Compare the two integration shapes

These are diagnostic projections of the [one-connection rule](rules/page-shell.md#scroll-refresh-and-haptics), not complete page templates. List ownership is the standard profile; the wrapper-owned shape remains useful when inspecting a justified existing compatibility path.

```text
Wrapper-owned integration:
  TopAppBar(behavior)
  PullToRefresh(topAppBarScrollBehavior = behavior)
    list helper(bar connection disabled)

List-owned integration:
  TopAppBar(behavior)
  PullToRefresh(no app-bar behavior)
    list helper(overscroll → behavior connection)
```

A mature shared wrapper can implement either branch. The ordinary no-refresh branch may have a different connection owner from its refresh branch while still obeying the same rule. Compare actual argument values before proposing a migration.

## Diagnose an external visual bridge

Follow the producer callback to its state object and layout/draw consumer. Compare raw drag, damped rebound, real list position, and business loading state using the distinct channels required by the rules. A visual that follows finger movement but snaps on release often lacks spring/reset updates.

Use beta02's `overScrollVertical(onOffsetChanged = ...)` and `TopAppBarState.overscrollOffset` for the selected rebound-driven presentation. The callback includes spring frames and the zero reset. Verify one producer and the actual layout/draw consumer; do not introduce a private-field workaround or a second controller.

For pager configuration, use the [Tab, pager and breadcrumb contract](../../miuix-components/references/contracts/pager-and-breadcrumb.md). Check the active mode and `userScrollEnabled` together: the CrossAxis override supplies touch recognition, while Native/disabled mode must restore native scrolling. Normalize saved mode values separately from input configuration. Top tabs and pager-backed bottom navigation share the [intent-lock coordinator](navigation-compositions.md#tab-selection-and-page-switching): hold a requested target during navigation and follow `currentPage` when unlocked. Investigate edge translation separately through [pager effect ownership](../../miuix-components/references/contracts/pager-and-breadcrumb.md#pager-edge-effect-ownership).

## Symptom-oriented investigation

| Symptom | Candidate cause | First evidence |
|---|---|---|
| Bar collapses twice or skips | Duplicate behavior connection | Outer wrapper plus inner list modifiers |
| Refresh and bar unexpectedly consume each other | Changed connection ownership/order | Target wrapper implementation and branch args |
| Header snaps during rebound | Missing damped/spring bridge | Offset producer through final reset |
| Indicator finishes but content stays displaced | Business and visual states conflated | Refresh/overscroll state owners |
| Pager, list, and tab gestures fight | Direction/active-page ownership unclear | Pager and vertical-content chains |
| Navigation indicator returns to the old tab, or oscillates during drag | Click intent mixed with pager position or predictive target | Intent lock, selected-index observer, currentPage versus targetPage |
| First/last page moves sideways and reveals background | Pager or ancestor horizontal overscroll | Pager overscrollEffect, theme factory, explicit modifiers and transforms |
| Drag list closes its sheet | Competing drag/dismiss paths | Sheet and reorder controller configuration |
| Touch works but wheel/keyboard does not | Input-path-specific effect | Target platform event/scroll implementation |

Use one candidate change at the proven owner, then validate the affected states from [Page shell](rules/page-shell.md#validation) or [sheet rules](rules/settings-and-overlays.md#validation). Report compilation and runtime/device evidence separately; a modifier-only source inspection does not prove gesture behavior.
