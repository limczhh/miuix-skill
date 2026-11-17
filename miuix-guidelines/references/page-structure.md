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

## Translate ordinary page requests

The following are source- and Example-backed routing cues for requests that do not name an architecture. Use them to start the investigation, then return to the ownership map below.

| User wording | Start by resolving | Do not assume |
|---|---|---|
| “做个普通页面/设置页” | Existing theme, route/page boundary, primary content owner, and state owner | A page type does not require `Scaffold`, `Card`, `SmallTitle`, or Preference rows |
| “像系统设置一样” | Meaningful groups, section hierarchy, and complete setting-row semantics | The Example's `SmallTitle` + `Card` arrangement is a universal template |
| “顶部大标题随滚动收起” | Page `topBar`, one primary scroll owner, and the shared scroll-behavior connection | A second scroll state or a manually offset app bar |
| “底下放几个页面入口” | Shell navigation ownership, selected destination/back-stack state, and compact/wide policy | That a bottom action group is navigation, or that a page should create its own route state |
| “做个弹窗/下拉选择” | Current page `Scaffold` lifetime versus independent window lifetime | That “dialog” always means `Window*`, or that a nested page overlay should use the root host by default |
| “加个搜索框” | Search input, expansion, result content, and which scroll owner displays the results | That `SearchBar` alone owns the whole page or that search results need a new scroll container |

The pinned Example demonstrates the shell/page distinction: `AppContent.kt` owns persistent navigation and global layers in shell scaffolds, while `SettingsPage.kt` owns its page app bar and forwards the shell padding into page content. The pinned `Scaffold.kt`, `TopAppBar.kt`, and `NavigationBar.kt` declarations define the corresponding slots and scroll/navigation contracts. Use those facts to choose the next reference, not to turn one Example page into a mandatory template.

## Shell and page host layering

An application may have an outer shell `Scaffold` and a page `Scaffold`. This is valid when the two hosts have different responsibilities, as in the Miuix Example: the shell owns persistent navigation or global transient layers, while the page host owns a page app bar or page-scoped behavior.

| Layer | May own | Must hand off |
|---|---|---|
| Application/shell host | persistent `NavigationBar`/`FloatingNavigationBar`/`NavigationRail`, global snackbar, shell-wide floating action or toolbar | its effective outer `PaddingValues` and adaptive state to the active page |
| Page host | the current page's `TopAppBar`, page-specific floating/transient layers, and page content contract | its local `PaddingValues` together with the shell handoff to the page's primary content |

Use nested hosts only when both rows have a real slot, inset, popup, or lifecycle responsibility. Do not add a nested `Scaffold` for background, grouping, or a convenient `padding` call. Each host may calculate local padding, but the primary page content must receive the intentionally combined result once; do not independently apply the same shell or system inset at multiple levels.

When an Overlay is declared below a nested page host, verify the root-host flag. In the pinned source, `renderInRootScaffold` defaults to `true`; a dialog that must remain inside the page host must explicitly use `renderInRootScaffold = false` after confirming the target version's declaration.

## Required ownership decisions

Before writing page code, fill in this map:

```text
Existing root/theme:          file or caller
Route/back-stack owner:       file, type, or none
Navigation chrome owner:      shell, page, or none
Page boundary:                route/entry/composable
Page host:                    existing Scaffold or direct layout
Primary content owner:        LazyColumn / Column / Pager / other
Inset handoff/consumer:       shell handoff, then final page content boundary
Host slots:                   top / bottom / floating / snackbar
Transient layer:              Overlay or Window, and its state owner
Persistent state owner:       screen owner, ViewModel, or application model
Adaptive policy owner:        one named shell/layout policy, if applicable
```

Do not select concrete content components until these ownership decisions are clear.

## Structural rules

- Reuse the existing application `MiuixTheme`, route host, page host, and inset contract. A page must not create a second root theme or host just to obtain styling or padding.
- Use one page boundary and one primary content/scroll owner. A pager may own horizontal paging while an individual page intentionally owns vertical content; unrelated competing scroll owners are a defect.
- Consume each `Scaffold`/window/system padding contract at a named handoff and one final page content boundary. In a nested shell/page arrangement, combine the outer handoff with the page host's local padding intentionally, then pass the result to the primary scrollable's `contentPadding` or content root. Do not ignore it or apply the same inset independently to every child.
- Keep bars, floating controls, snackbars, and page-scoped overlays in the host that owns them. Do not put a page bar inside the scrolling content only because it is visually nearby.
- Use a page `Scaffold` when the page owns a real bar, floating/bottom slot, snackbar host, Overlay host, or content-window contract. A shell `Scaffold` and page `Scaffold` may both exist when their responsibilities are distinct; do not nest one for background, grouping, or padding alone.
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

Once ownership is mapped, use [Page composition routes](page-compositions.md) only to select the narrow guide for a `Scaffold` + app bar, bottom navigation, floating action, primary scrolling content, or overlay combination. A guide supplies a known placement shape; it does not override the ownership map or make its components mandatory.

## Evidence boundary

Miuix source and public docs define API signatures, defaults, slots, insets, and host behavior. The Example demonstrates integrated composition and state flow. This file combines those facts into a default structure; it is not an additional hidden Miuix API requirement. Preserve an intentional target-project convention when it differs, then verify the affected behavior against the pinned source.

## Structural review

Before implementation is ready, confirm:

- the existing theme and application shell are identified;
- route, navigation chrome, page, and business state have distinct owners;
- one primary content/scroll owner and one final inset consumer are identified, with any shell-to-page handoff named;
- host slots and Overlay/Window choice match the lifecycle;
- compact/wide, IME, and resized-window policy has one owner when relevant;
- only after these answers, concrete components are selected.
