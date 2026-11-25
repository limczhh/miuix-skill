# Glass Composition Guide

Use this guide for a new Glass composition on the `0.9.5-rc01` baseline. For replacing existing components, a surface-only change, or keeping ordinary and Glass families, start with [Glass migration and coexistence](glass-migration.md). Ordinary blurred app bars follow [Top bar compositions](top-bar-compositions.md); they do not require Glass components. Mandatory recording, fallback, and host separation live in [Blur and Glass rules](rules/page-shell.md#blur-and-glass).

## Sampling relationship

```text
page owner: one backdrop + one scroll behavior
└── Scaffold
    ├── topBar: Glass surface sampling the backdrop
    ├── bottomBar: optional Glass navigation sampling the backdrop
    ├── content: opaque base + recorded content layer
    └── popup host/scrim outside the recorded content
```

The selected Glass family determines whether a popup belongs to the Scaffold host or beside an anchor in a root `Box`. Verify that family through [Glass Components](../../miuix-components/references/glass.md); the [popup rules](rules/settings-and-overlays.md#popup-family-and-lifetime) own its application lifetime.

## Fixed title and adaptive rail

Use `GlassTopAppBar(expandable = !isWideWindow)` with the shared window policy when the layout fits. Its compact title can stay visible without composing a second `SmallTopAppBar`. A custom hero header can retain its intentional structure; identify its visible title and scroll/material owner.

`GlassNavigationRail` belongs in a full-space overlay outside recording. Its state morphs a side panel into a top capsule; page clearance and title overlap follow that progress. This differs from a width-reserving ordinary rail Row. The [migration guide](glass-migration.md#keep-ordinary-and-glass-navigation-together) explains the state/selection handoff, including secondary destinations.

For full-screen search, use `GlassSearchOverlay` as an in-page layer above the shell rather than adding a search route or a second top bar. Keep query/results in one owner, pass the result list's `scrollOffset`, and keep the overlay outside the recorded Glass scene through its exit transition. Use `GlassTextField` only for a field inside an existing Glass surface; it does not replace the form or popup host. For grouping content inside `GlassBottomSheet` or `GlassDialog`, use `GlassCard` (translucent fill without separate backdrop sampling), and remember `GlassBottomSheet` passes header `PaddingValues` to its `content` lambda when `progressiveBlur = true`.

## API and evidence handoff

Verify the exact Glass imports/modules, backdrop type, app-bar overload, band/material parameters, and popup host contract through `miuix-components`. An ordinary blur radius or margin is not automatically a Glass material parameter.

Use the [page-shell validation boundary](rules/page-shell.md#validation) to assess supported and opaque-fallback branches, scroll/material transitions, sampling exclusions, and actual rendered/device appearance.
