# Page Structure and Ownership

Use this reference for a new page, page redesign, shell refactor, or a change involving navigation, scrolling, insets, or transient UI.

This is a structural contract, not a page-type or component template. A settings page, list page, form page, detail page, and custom-content page are all ordinary pages here. The structure is fixed; the components that fill the main content are chosen by semantics.

## Default page map

```text
Application root
└── one MiuixTheme
    └── application/route shell
        ├── optional adaptive navigation chrome
        └── active page boundary
            └── existing or page-owned host
                ├── optional top slot
                ├── one primary main-content owner
                ├── optional bottom/floating/snackbar slots
                └── optional page-scoped overlay layer

Independent window layer (only when required)
└── Window dialog, sheet, or menu
```

The route shell may render the active page directly or through `miuix-nav`. Navigation chrome presents the selected destination; it does not become a second route/back-stack owner.

## Required ownership decisions

Before writing page code, fill in this map:

```text
Existing root/theme:          file or caller
Route/back-stack owner:       file, type, or none
Navigation chrome owner:      shell, page, or none
Page boundary:                route/entry/composable
Page host:                    existing Scaffold or direct layout
Primary content owner:        LazyColumn / Column / Pager / other
Inset consumer:               the single content boundary
Host slots:                   top / bottom / floating / snackbar
Transient layer:              Overlay or Window, and its state owner
Persistent state owner:       screen owner, ViewModel, or application model
Adaptive policy owner:        one named shell/layout policy, if applicable
```

Do not select concrete content components until these ownership decisions are clear.

## Structural rules

- Reuse the existing application `MiuixTheme`, route host, page host, and inset contract. A page must not create a second root theme or host just to obtain styling or padding.
- Use one page boundary and one primary content/scroll owner. A pager may own horizontal paging while an individual page intentionally owns vertical content; unrelated competing scroll owners are a defect.
- Consume `Scaffold`/window/system padding at one known content boundary. Pass it to the primary scrollable's `contentPadding`, or apply it to the content root. Do not ignore it or apply the same inset to every child.
- Keep bars, floating controls, snackbars, and page-scoped overlays in the host that owns them. Do not put a page bar inside the scrolling content only because it is visually nearby.
- Use a page `Scaffold` when the page owns a real bar, floating/bottom slot, snackbar host, Overlay host, or content-window contract. Do not nest one for background, grouping, or padding alone.
- Keep persistent/business state in the screen owner or model. Keep transient presentation state close to the host that renders it.
- Put compact/wide branching in one shell or layout-policy function. Preserve the same selected destination, page, and business state when only navigation chrome changes.
- Choose Overlay components for page-scoped surfaces under the intended `Scaffold`; choose Window components when the surface must outlive or ignore that page host.

## Component choice after structure

After the map is complete:

1. Choose the page host only if the page owns host behavior.
2. Choose the primary content primitive by measurement and scrolling needs.
3. Fill the content with any semantically suitable Miuix component, Compose primitive, or application-owned component.
4. Prefer Miuix theme roles and component `*Defaults` for visual details.
5. Use `Card`, `SmallTitle`, Preference rows, custom layouts, or effects only when their containment or interaction semantics fit the content.

The page skeleton does not require a settings recipe, a Card, a top bar, or any other specific component.

## Choose a composition recipe

Once ownership is mapped, use [Page composition recipes](page-compositions.md) when the page combines a `Scaffold` with an app bar, bottom navigation, floating action, primary scrolling content, or an overlay. A recipe supplies a known combination and placement; it does not override the ownership map or make its components mandatory.

## Evidence boundary

Miuix source and public docs define API signatures, defaults, slots, insets, and host behavior. The Example demonstrates integrated composition and state flow. This file combines those facts into a default structure; it is not an additional hidden Miuix API requirement. Preserve an intentional target-project convention when it differs, then verify the affected behavior against the pinned source.

## Structural review

Before implementation is ready, confirm:

- the existing theme and application shell are identified;
- route, navigation chrome, page, and business state have distinct owners;
- one primary content/scroll owner and one inset consumer are identified;
- host slots and Overlay/Window choice match the lifecycle;
- compact/wide, IME, and resized-window policy has one owner when relevant;
- only after these answers, concrete components are selected.
