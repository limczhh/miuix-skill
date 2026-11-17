# Verified API Contract Index

This file routes exact Miuix API questions to a narrow contract guide. It is not the contract itself and should not be loaded together with every contract guide.

The maintained snapshot is upstream main at commit d8efe1872c08c863a8b33e7044dd2a8b43038032 (v0.9.4-80-gd8efe187, not a release tag). The pinned declaration remains subordinate to the target project's resolved dependency and compilation.

## Select one contract guide

| Need | Read |
|---|---|
| Application theme or Scaffold host/insets | [Theme and page host](contracts/theme-and-host.md) |
| TopAppBar, SmallTopAppBar, BlurTopAppBar, or collapsing behavior | [Top bars and scroll](contracts/top-bars-and-scroll.md) |
| Card, SmallTitle, NavigationBar, or FloatingNavigationBar | [Surfaces and navigation](contracts/surfaces-and-navigation.md) |
| OverlayDialog or WindowDialog | [Overlays](contracts/overlays.md) |
| SwitchPreference, ArrowPreference, TextField, Button, or TextButton | [Inputs and actions](contracts/inputs-and-actions.md) |

## Contract rules

- Read only the matching guide and heading.
- The declaration block records public parameter names, types, order, and defaults for the pinned snapshot.
- The import block identifies the Miuix package; target dependency wiring and standard Compose imports still require checking.
- Docs and Demo explain intent or isolated usage; Example and consumer code explain integration. None replaces the declaration.
- On a Miuix version change, treat the contract as stale until the source path and declaration are rechecked.

## When the symbol is not listed

Do not enlarge one contract guide for every component. Read [Source verification](source-verification.md), locate the declaration through [Library map](library-map.md), and add a new contract guide only when the API is high-frequency, structurally important, or has repeatedly caused integration errors.

For navigation stack APIs, use the dedicated [miuix-nav guide](miuix-nav.md) and the same source-verification gate.
