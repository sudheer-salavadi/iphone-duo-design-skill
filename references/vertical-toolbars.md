# iPhone Duo — Vertical Toolbars, Tab Bars & Sheets (Full API Reference)
Source: Apple's Sept 2026 Tech Talk "Raise the Bar with iPhone Duo" (Maria, Anna) — transcript reviewed directly, including its on-screen code samples, which is what the code blocks below are taken from.

This file is the engineering-level companion to the "Vertical Controls" and "Sheets" sections of [iphone-duo.md](iphone-duo.md) — load this one when the task requires exact API names (estimating engineering scope, reviewing a PR, writing a precise spec), and the other for the design-pattern-level framing.

> Same pre-release caveat as the rest of this skill: this is Xcode 27.1 / iOS 27.1 SDK territory, still in beta as of this research.

## Why bars move (recap)

The wider outer-display aspect ratio gives more horizontal space; to use it and preserve vertical space for content, top/bottom controls move to the side, where they're also easier to reach. Position stays consistent when opening to the inner display **in landscape**. Opening **in portrait**, the inner display reverts to a familiar horizontal-bar iPhone layout. These are the same components, just adapted to a different layout — not a separate design.

---

## Opting In: System Containers Are Required

Vertical-bar behavior requires (1) rebuilding against the latest SDK, and (2) using bars **provided by navigation containers**, not custom-built ones:

```swift
// SwiftUI — bars from a navigation container adapt automatically
NavigationStack {
    ContentView()
        .toolbar {
            ToolbarItem(placement: .bottomBar) {
                ...
            }
        }
}
```

```swift
// UIKit — content from custom bars (UINavigationBar, UITabBar, UIToolbar)
// won't be considered. Prefer UINavigationController and UITabBarController,
// which manage their own bars, over building a custom UIToolbar like this:
let toolbar = UIToolbar()
toolbar.items = [...]
```

**Design implication**: if an app builds its own `UIToolbar` manually (a common pattern for fully custom chrome), it is explicitly excluded from vertical-bar adaptation — this is a concrete, checkable engineering fact to raise in a design review, not a matter of preference.

---

## Container Interaction Rules

- Navigation, toolbar, and tab bar controls coexist within a **shared vertical region** — conceptually, imagine them rotating 90° into a vertical stack. A layout may contain any combination (Notes: toolbar only; Clock: tab bar only; Fitness: both).
- In **split views**, only the **detail column** participates in the vertical bar — other columns (e.g. a sidebar/primary column) stay horizontal. Because the detail column already has a vertical bar, an **expanded inspector doesn't get its own** (avoids doubling up / confusion).
- **Sheets**: on the outer display, a sheet's existing toolbar displays vertically by default. On the inner display, sheets are centered with horizontal items by default. Using the preferred-placement API to move a sheet: placed on the **left**, it stays without a vertical bar; placed on the **right**, it gets one. The bar is aligned to the hardware side, so it stays on the same physical side of the device even in right-to-left languages — content adapts around it, the bar itself stays fixed.

---

## Item Ordering

Items retain a clear top-to-bottom hierarchy. Reserve the top for primary navigation (Back/Close), followed by prominent actions (Done):

```swift
// SwiftUI — back/close button
.toolbar {
    ToolbarItem(placement: .cancellationAction) {
        ...
    }
}

// UIKit — a navigation-controller-provided back button is automatic;
// for a custom leading item:
navigationItem.leftItemsSupplementBackButton = false   // this is the default
navigationItem.leadingItemGroups = [UIBarButtonItemGroup(...)]
```

```swift
// SwiftUI — prominent actions
.toolbar {
    ToolbarItem(placement: .topBarPinnedTrailing) {
        ...
    }
}

// UIKit
navigationItem.pinnedTrailingGroup = UIBarButtonItemGroup(...)
```

Remaining items preserve their original grouping, with a vertical spacer visually separating top and bottom placements even when unified into a single bar. **Not every pose lays out bars vertically** — keep control placement consistent across poses so people don't have to relearn where actions live.

---

## Tailoring Toolbar Content for the Vertical Axis

Horizontal bars have fixed height/flexible width; **vertical bars have flexible height/fixed width** — better suited to symbol-only items. The system already decides icon-vs-text representation based on what you provide (icon preferred in-bar if present, otherwise text; both title+icon shown once an item moves to overflow) — **none of this changes for vertical bars**; the system additionally considers whether content suits a vertical or horizontal axis. Always provide a title even for icon-only items — it's used whenever the item moves to overflow or an expanded/accessible representation.

**Axis-behavior override API** — for the cases where the default content-based inference isn't right:

```swift
// SwiftUI — force a custom view into the vertical bar (e.g. a compass view)
.toolbar {
    ToolbarItem {
        CompassView()
    }
    .axisBehavior(.verticalPreferred)
}

// UIKit
let item = UIBarButtonItem(customView: CompassView())
item.axisBehavior = .verticalPreferred
```

```swift
// SwiftUI — keep a symbol/text-transitioning custom item horizontal
// (the system's own Edit button already handles this automatically —
// only needed for a custom item that behaves similarly, e.g. a custom
// select-or-done toggle)
.toolbar {
    ToolbarItem {
        SelectOrDoneButton()
    }
    .axisBehavior(.horizontalOnly)
}

// UIKit
item.axisBehavior = .horizontalOnly
```

**Badges** — minimize title-only items or dual text+image custom views (e.g. an inline unread count) in favor of a symbol-only item with a badge, which is vertical-bar-eligible:

```swift
// SwiftUI
ToolbarItem(...) {
    InboxButton()
        .badge(7)
}

// UIKit
let item = UIBarButtonItem(...)
item.badge = .count(7)
```

Adopt the iOS 26 Badge API if not already, for a standard badge appearance across all devices. Heuristic for whether text can be dropped in favor of a symbol: is the text merely reinforcing the symbol (drop it, symbol alone communicates the action), or does it carry standalone information (e.g. a cart total dollar amount — keep it in the horizontal bar)?

**Custom views in a vertical bar** must either fit the bar's fixed width or have a vertically-adapted layout (e.g., hiding titles and becoming slightly shorter when vertical, to leave room for other content). Detect vertical-bar context from within a custom view:

```swift
// SwiftUI
@Environment(\.toolbarVerticalEdge) var edge

// UIKit
traitCollection.verticalBarEdge
```

Other notes: a vertical bar has no scroll-edge effect by default but does have a background — ensure custom content stays legible when "Reduce Transparency" is on. Flexible spacers are zero-size on the vertical axis by default (fixed spacers keep their minimum size) — don't add manual extra spacing regardless of bar orientation. **Accessory bars (e.g. attached to the keyboard) should stay attached to the keyboard**, not move to the vertical axis.

---

## Managing Overflow

Overflow pressure is higher on the outer display in landscape (less vertical space), and increases further when competing UI appears (keyboard, Picture-in-Picture in open portrait).

**Toolbar vs. tab bar — which compresses first**:

```swift
// SwiftUI — default behavior for navigation-focused experiences
// (e.g. a podcast view): the toolbar compresses first so primary
// destinations (tab bar) remain accessible
TabView {
    Tab("Recents", systemImage: "clock") {
        ContentView()
            .toolbarVerticalCompressionBehavior(.prefersToolbarItems)
    }
}

// UIKit
navigationItem.verticalBarCompressionBehavior = .prefersBarItems
```

For task-oriented experiences (e.g. Games), the default flips: the tab bar compresses first to preserve toolbar actions. Configure this per-view rather than assuming one rule applies everywhere.

**Consolidating a custom overflow menu into the system-managed one**:

```swift
// SwiftUI
.toolbar {
    ToolbarOverflowMenu {
        Button("Scan") { ... }
        Button("Connect") { ... }
    }
}

// UIKit
navigationItem.additionalOverflowItems = UIDeferredMenuElement({ provider in
    provider(self.persistentOverflowItems())
})
```

Reserve the ellipsis symbol specifically for the standard overflow menu — give any other (non-overflow) menu a distinct symbol.

**Visibility priority** — items overflow bottom-to-top by default; assign priority to control collapse order (prioritize by group first, then individual items within a group if needed):

```swift
// SwiftUI
.toolbar {
    ToolbarItem {
        Button(...) { ... }
    }
    .visibilityPriority(.high)
}

// UIKit
let item = UIBarButtonItem(...)
item.visibilityPriority = .high
```

Give high priority to frequently-used actions (Compose in Mail, New Note in Notes) and to items conveying important status (e.g. badged items) — these should be among the last to move into overflow.

---

## Disabling the Vertical Bar

Named cases where a vertical bar should be turned off: a single-page, bottom-heavy layout (Apple's own example: Calculator — let content fully expand instead), or a control-sparse sheet with only one item (e.g. just a Close button — a vertical bar would waste space here).

```swift
// SwiftUI
NavigationStack {
    ContentView()
        .toolbarVerticalBehavior(.disabled)
}

// UIKit
class MyViewController: UIViewController {
    override var preferredVerticalBarBehavior: UIVerticalBarBehavior {
        .disabled
    }
}
```

---

## Design Implications Summary

- Before estimating engineering effort for "make our toolbars Duo-ready," check whether the app already uses `UINavigationController`/`UITabBarController` (or SwiftUI `NavigationStack`/`TabView`) versus hand-rolled bars — the latter needs a rebuild, not just a config change, to participate at all.
- A design spec for a custom toolbar item should say explicitly whether it's icon-only, text-only, or needs an axis-behavior override — don't leave "will this go vertical" to be discovered during implementation.
- When auditing overflow behavior, identify whether a screen is "navigation-focused" or "task-oriented" up front — that classification determines the default compression direction, and getting it backwards means the wrong content becomes hard to reach under space pressure.
