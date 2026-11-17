# Glass Composition Guide

Use this guide only for an explicit Glass-family requirement. Ordinary blurred app bars follow [Top bar compositions](top-bar-compositions.md); they do not require Glass components. Mandatory recording, fallback, and host separation live in [Blur and Glass rules](rules/page-shell.md#blur-and-glass).

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

## Pinned small-title composition

When the requested band/material and visible-title layout are separate concerns, a composite top slot can be evaluated:

```text
Scaffold.topBar
└── Box
    ├── sampling/band layer: GlassTopAppBar with no visible title
    └── title/actions layer: SmallTopAppBar
```

This is an application composition option, not a public combined component. The [Glass rules](rules/page-shell.md#blur-and-glass) define its backdrop and connection boundaries. A normal `GlassTopAppBar` is the simpler fit when its title layout already meets the requirement.

## API and evidence handoff

Verify the exact Glass imports/modules, backdrop type, app-bar overload, band/material parameters, and popup host contract through `miuix-components`. An ordinary blur radius or margin is not automatically a Glass material parameter.

Use the [page-shell validation boundary](rules/page-shell.md#validation) to assess supported and opaque-fallback branches, scroll/material transitions, sampling exclusions, and actual rendered/device appearance.
