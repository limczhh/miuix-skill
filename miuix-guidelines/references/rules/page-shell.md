# Page Shell and Navigation Rules

Mandatory for page hosts, navigation, bars, pagers, insets, scroll/refresh, blur/Glass, and adaptive layouts (see [Scope & Exceptions](design-system.md#scope-and-rule-strength)). Verify concrete Miuix symbols/defaults via `miuix-components`.

Contents: [hosts & wrappers](#page-hosts-and-shared-wrappers) · [greenfield shell](#greenfield-app-shell-invariants) · [top bar](#standard-top-app-bar) · [routes & back](#routes-and-back) · [nav & adaptive](#navigation-and-adaptive-layout) · [tabs & pagers](#tabs-and-pagers) · [insets & status bar](#insets-and-content-padding) · [scroll & refresh](#scroll-refresh-and-haptics) · [blur & Glass](#blur-and-glass) · [validation](#validation)

## Page Hosts and Shared Wrappers

- Keep **1 route owner** and **1 primary vertical scroll owner** per page boundary. Nest shell + page `Scaffold`s only when shell owns navigation/global overlays and page owns its bar/popup lifetime (never nest just for background color).
- Secondary settings/list/detail pages use a page `Scaffold` + adaptive `TopAppBar` + back action + scroll container (`Column.verticalScroll` for small forms; lazy container for repeated content).
- Put bars, FAB/toolbar, and visible `Snackbar` hosts in their intended `Scaffold` slots (separate popup windows need their own visible feedback if page Snackbar is covered).
- Reuse shared page wrappers (custom-content slots consume the passed `PaddingValues` and shared `ScrollBehavior` instead of creating parallel owners). Split complex features by sections rather than duplicating entire page skeletons.

## Greenfield App Shell Invariants

For multi-destination shells with bottom/floating navigation (follow [App shell & navigation](../app-shell-and-navigation.md) before page details):
- Use **1 shell-owned selection coordinator** + **1 `HorizontalPager`** for peer pages and all destination-bar presentations, and **1 root `NavDisplay`** stack for secondary detail/subflow routes (`bottomBar` switches destinations, never pushes a route).
- Each pager page owns its internal vertical scroll container; never put all destinations inside one shared vertical list.
- Keep the shell `Scaffold(containerColor = Color.Transparent)` when the content layer paints the full-window surface/backdrop. Wrapper around `FloatingNavigationBar` handles alignment/width only and **MUST NOT** paint a rectangular background or clip around the bar; apply shell `innerPadding` once at the pager/content boundary so the background extends continuously behind the floating bar.
- Keep selection and route state above ordinary/Glass/rail visual branches, rendering only one active interactive surface per role.

## Standard Top App Bar

- Scrollable settings, list, and detail pages **MUST** use a scroll-aware adaptive bar in `Scaffold.topBar`: compact windows collapse a `largeTitle`; wide windows fix a compact title (`expandable = false` or matching wide strategy). Share **1 `MiuixScrollBehavior`** between bar and content/refresh owner (`LazyListState` only tracks list position).
- Standard bars **MUST** participate in backdrop blur: ordinary blurred bars sample a sibling recorded content subtree behind a transparent inner bar (or paint solid `surface` when off/unsupported); `GlassTopAppBar` owns its own material/band (remove any ordinary blur wrapper to prevent double blur). Bar background **MUST** cover the top system-bar inset too.
- Secondary pages wire back action to the navigator (root pages omit it); keep `SmallTopAppBar` / custom hero headers only where intentional and preserve blur/scroll linkage. Bare unblurred/static bars require a justified basis (e.g., non-scrolling setup step or explicit Brownfield host).

## Routes and Back

- Use typed serializable routes; pass arguments cleanly (no temporary globals). Mutate back stack through one navigator preventing duplicate consecutive pushes and protecting root validity.
- Handle local popups, search, and IME dismissal before popping routes; keep optional "back to first tab" policy in the shell coordinator rather than competing tab `BackHandler`s. Complete onboarding replaces/pops setup routes cleanly. Keep transitions/predictive back in the existing host (do not spawn new Activities merely for heavy Compose screens).

## Navigation and Adaptive Layout

- Persistent bottom bars, floating bars, and rails consume the **same** selection state, surviving compact/wide resize intact. Use `horizontal` spacing **`12.dp`** on floating navigation recipes (avoid inserting extra `Spacer`s when the component provides item spacing; **`12.dp`** item spacing applies only if absent).
- Compute compact vs wide in **1 shared window-size helper** (`width >= 840.dp`, or `width >= 600.dp` when `height/width < 1.2`; guard zero/invalid bounds; orientation alone is insufficient). Re-evaluate on resize. Centered max-width bodies must keep scrolling/inputs usable.
- When adopting `GlassNavigationRail`, note it is a full-space overlay morphing into a top capsule rather than a width-reserving Row: coordinate page clearance and title obstruction from its single rail state, keep it outside recording, and keep secondary items reachable in collapsed/compact layouts.

## Tabs and Pagers

- Put page tabs in the top bar bottom slot or fixed header boundary; **`12.dp`** horizontal gutter + **`12.dp`** bottom gap; use transparent unselected fill inside blurred bars. Keep per-tab scroll/list/filter state independent while top bar state reflects the active page (note `TabRow` uses `selectedTabIndex`/`onTabSelected`, whereas `GlassTabRow`/`GlassSegmentedTabRow` use `selectedIndex`/`onSelect`).
- **Peer-page switch motion**: **2 pages -> smooth `EaseInOut` (`~300 ms` baseline)**; **3+ pages -> direct switch** via one shared coordinator across tabs & bottom nav (user swipe physics unaffected).
- **Intent lock**: On selection click, synchronously set `selectedIndex` + hold `isNavigating = true` during the guarded pager job; when unlocked, sync `selectedIndex` from **`currentPage`** (never predictive `targetPage`). Release lock on completion, cancellation, or user swipe takeover (stale cancelled jobs must never unlock newer requests).
- Set `HorizontalPager(overscrollEffect = null)` for fixed peer-page edges while preserving child vertical list overscroll/refresh. Only retain offscreen adjacent pages when cost-justified (never run offscreen scans/animations).

## Insets and Content Padding

- Combine host, page, and system-safe padding in **1 shared boundary helper**: take the **max** of overlapping edge clearances, add only non-overlapping occupied slots + named extras, and **consume once** at the primary scroll/content boundary (`16.dp` bottom breathing room for standalone scroll pages, without doubling shell-provided room). Subtract start clearance already consumed by a sibling side rail (LTR/RTL aware).
- Do not hardcode status/navigation bar heights; test landscape cutouts, caption bars, and IME avoidance. Verify component `defaultWindowInsetsPadding` flags via `miuix-components` before setting `false` or adding manual padding.
- **Status-bar background ownership**: When a page top bar owns the top edge, its background **MUST** extend behind the status bar while title/controls stay inside safe top insets (do not push the entire background below status bar padding or size it to controls only; check status-bar icon contrast across expanded/collapsed/rebound/effects-off states).

## Scroll, Refresh, and Haptics

- **Default list-owned chain**: conditional scroll-end haptic -> `overScrollVertical(onOffsetChanged = ...)` (updating bar `overscrollOffset` through spring/reset frames) -> **1** app-bar `nestedScrollConnection` on the scrollable list (leave `PullToRefresh` app-bar behavior `null`). Wrapper-owned coordination (`PullToRefresh` owns connection, list disables it) is only a Brownfield compatibility choice — **never attach the connection in both places**.
- Keep `isRefreshing`, indicator state, list scroll position, collapse fraction, and visual rebound separate; prevent duplicate active refresh triggers; localize refresh labels. Do not stack explicit overscroll modifiers with a conflicting theme scrollable overscroll effect.

## Blur and Glass

- Resolve runtime shader support and user blur preference in one owner; keep background sampling vs foreground transition blur (titles, visibility fade, popup morphs) distinct.
- **Standard blurred bar recipe**: dedicated background sibling layer (e.g., `matchParentSize`, never wrapping/blurring foreground controls) using **`25f` radius + theme `surface` at `0.8f` alpha** for top/bottom bars, or **`surfaceContainer` at `0.6f` alpha** matching component corners for floating bars.
- Never record a sampling surface, popup host, or scrim inside its own `Backdrop`; keep an opaque fill inside recorded scenes. Never use `alpha = 0f` or `0f` radius as a universal off switch — apply each component's real off/fallback contract (check `miuix-components` for `GlassTabRow` vs `GlassSegmentedTabRow`, `GlassBottomSheet`, `glassPanel` fallback, etc.). Keep theme dark/light/effects transitions synchronized on live bars.

## Validation

Inspect affected routes/back stack, rapid pager clicks/interruptions, compact/wide resize & resize-retained state, cutouts/IME/RTL, light/dark/effects-off/unsupported-runtime states, and scroll drag/refresh/cancellation/rebound. Distinguish source inspection & compilation from rendered/runtime proof.
