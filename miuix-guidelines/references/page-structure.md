# Page Structure and Ownership Guide

Use this guide to map an unfamiliar page, redesign, or shell change. It is a decision worksheet. Mandatory ownership and host behavior live in [Page shell rules](rules/page-shell.md#page-hosts-and-shared-wrappers); visual choices live in [Design system rules](rules/design-system.md).

## Start from the actual boundary

A typical map is:

```text
application theme and route owner
└── shell: persistent navigation and global layers
    └── active page boundary
        └── existing or page-owned host
            ├── top bar/header
            ├── primary content/scroll owner
            ├── optional actions and feedback slots
            └── page-scoped transient surface

separate window, when the selected surface needs it
```

An outer shell plus page host can represent two real responsibilities. A direct content root can represent a page with no host-owned slots. Use the [host rules](rules/page-shell.md#page-hosts-and-shared-wrappers) to decide which applies rather than selecting from the page's name.

## Translate ordinary requests

| Request | Question to investigate | Next guide |
|---|---|---|
| Ordinary/settings page | Which existing shell owns routing, and what content belongs to the page? | Greenfield or Brownfield after inspection |
| System-settings appearance | Which content forms a semantic group, and which rows are actual preferences? | [Design language](design-language.md), then [content](content-and-overlays.md) |
| Large title reacting to scroll | Where is the page's bar/material/scroll integration boundary? | [Top bar](top-bar-compositions.md) |
| Bottom destinations or a rail | Which shell owns selection and width/orientation policy? | [Navigation](navigation-compositions.md) |
| Dialog or selection popup | Which page/window lifetime and anchor does this surface need? | [Content and overlays](content-and-overlays.md) |
| Search input and results | Does search filter this content or own an expanded result surface? | [Content and overlays](content-and-overlays.md) |
| Multiple gesture participants | Which path actually installs each connection? | [Gesture dynamics](gesture-dynamics.md) |

## Ownership worksheet

For a non-trivial change, record what is visible in the source:

```text
Existing theme/root:
Route/back-stack owner:
Navigation chrome owner:
Page entry and host:
Primary content/scroll owner:
Shell-to-page inset handoff and final consumer:
Host slots and transient surface family:
Persistent state and draft/presentation owners:
Adaptive policy owner:
Shared bar/backdrop/scroll wrapper and callers:
```

Unknown fields stay unknown until inspected. For an existing page, label changed boundaries preserve/replace/add using [Brownfield evolution](brownfield-evolution.md).

## Choose the composition

After the map identifies the changed boundary, select its narrow guide from the table above. A normal scrollable page follows the [standard top-app-bar rule](rules/page-shell.md#standard-top-app-bar); a fixed/absent bar is an explicit variant with its own reason. Content semantics still decide whether the body uses preferences, cards, a form, or custom drawing.

For exact symbols, Defaults, host flags, and version facts, hand the chosen calls to `miuix-components`. This guide supplies no separate structural-rule checklist or API-default catalog.
