# Standard iPhone vs. iPhone Duo — What Carries Over, What Changes, How to Scope

Apple's own framing: "Standard iOS design patterns apply despite the novel form factor." Duo is not a new platform — it's standard iOS plus a specific set of adaptations. Use this file to decide, for a given feature or screen, whether it's Duo-agnostic (no extra work), Duo-aware (needs validation but no new design), or Duo-specific (needs an explicit Duo design pass).

## What carries over unchanged

These apply identically on standard iPhone and iPhone Duo — do not re-derive or re-litigate them per device:
- Minimum tap targets (44×44pt), Dynamic Type (up to 200%), color contrast (4.5:1 / 3:1), Reduce Motion — see [accessibility.md](accessibility.md).
- Notification structure (4 action buttons, badge rules), general widget families/timing constraints — see [notifications-widgets.md](notifications-widgets.md).
- Live Activities' general model (≤8hr scope, push-updatable) — see [live-activities-dynamic-island.md](live-activities-dynamic-island.md) — but **not** its dimensions/layout on Duo's outer display: there, the Dynamic Island sits in the vertical control rail and **expands vertically** as Live Activities arrive, sharing that rail with app toolbar/tab-bar items and collapsing into overflow under space pressure alongside them (see [iphone-duo.md](iphone-duo.md) and [vertical-toolbars.md](vertical-toolbars.md)) — the standard-iPhone Dynamic Island dimensions in live-activities-dynamic-island.md do not describe this outer-display presentation.
- Materials, Dark Mode, SF Symbols, App Icon composition rules — see [visual-system.md](visual-system.md).
- Privacy/permission UI rules, Sign in with Apple, In-App Purchase, SharePlay, Siri/App Intents constraints — see [privacy-permissions.md](privacy-permissions.md) and [commerce-collaboration-assistant.md](commerce-collaboration-assistant.md).
- The core size-class model (compact/regular) itself is unchanged — Duo doesn't introduce a third size class. What changes is *what drives* the size class (fold state, not just device/orientation) and *what else* is reserved around it.

## What's different on Duo — see [iphone-duo.md](iphone-duo.md) for full detail

| Standard iPhone | iPhone Duo |
|---|---|
| One display, one set of safe areas (Dynamic Island, home indicator) | Two displays (outer + inner), each with its own front camera; plus a conditional folding region |
| Orientation (portrait/landscape) and multitasking Split View drive size class | Fold state / pose (closed, book-fold, flat, stand) drives size class, *in addition to* orientation |
| Toolbars/tab bars anchored horizontally (top/bottom) | Toolbars/tab bars anchored **vertically** (side), except inner display in portrait |
| Split view use is opt-in (regular environments only) | Split view is a primary adaptation pattern: expands on inner display, collapses to one pane on outer display |
| No "arrangement view" concept | Split arrangement / overlay arrangement containers manage panes across fold transitions |
| One reserved region to design around (Dynamic Island + home indicator) | Four reserved regions: outer camera, inner camera, folding region, system components |
| A design is "done" once validated in portrait + landscape | A design must be validated across poses (at minimum: closed/outer, book-fold, fully open/inner) *and* the transition between them |

## Scoping questions to ask before designing/reviewing a feature

1. **Does this feature only ever appear on the outer display (closed-device use), or could it appear on the inner display too?** If both, it needs the split-view/adaptive-layout treatment, not two separate designs.
2. **Does this screen use a horizontal toolbar/tab bar assumption anywhere in the mockup?** If yes, it needs an explicit vertical-controls pass per [iphone-duo.md](iphone-duo.md) — this is the single most common place a standard-iPhone design silently breaks on Duo.
3. **Is this a master-detail or list+detail pattern?** If yes, default to scoping it as a system split view (expands on inner, collapses on outer) rather than inventing a custom two-pane layout.
4. **Does the design assume a fixed pixel/point width for "the screen"?** If yes, it's not Duo-feasible as specified — rescope around size classes and safe areas, the same discipline already required for standard iPhone/iPad, just with an extra (folding-region) constraint.
5. **Is this a game or highly custom full-bleed visual surface?** If yes, explicitly design for pose changes (aspect-ratio adaptation, not letterboxing) and check whether it qualifies for the named "disable the vertical bar" exception (like Calculator, a bottom-heavy single-page layout) rather than the default vertical-controls model.
6. **Does the feature depend on the front camera?** If yes, check both outer and inner camera behavior — see [camera.md](camera.md) for the virtual-vs-individual-camera tradeoff and the direction coordinator's per-view forward/backward model, rather than assuming a single fixed "the front camera" reference point.
7. **Does the design rely on a hand-built custom bar (`UIToolbar`/`UINavigationBar`/`UITabBar`), a second window on the outer display, or content shown on the outer display outside the Camera Capture Accessory pattern?** Each of these is an explicit feasibility gate, not a style choice — see [vertical-toolbars.md](vertical-toolbars.md) (custom bars don't get vertical-bar behavior) and [xcode-development.md](xcode-development.md) (new windows/scenes are inner-display-only).

## What this skill cannot yet confirm

The Designing for iPhone Duo HIG page is new (added September 9, 2026) and is, as of this research, the *only* HIG page with Duo-specific content — a direct check of Designing for iOS, Layout, Multitasking, and Live Activities found no Duo-specific updates to those pages yet, and a dedicated `arrangement-views` page does not exist (its content lives only inside the Duo page). Treat anything not stated **anywhere in this skill's Duo-specific files** (`iphone-duo.md`, `xcode-development.md`, `vertical-toolbars.md`, `camera.md`) as unconfirmed for Duo specifically, even if it seems like a natural extension of a standard-iPhone rule — check developer.apple.com directly before treating it as settled guidance, since Apple is likely to keep expanding Duo-specific coverage across other HIG pages and Tech Talks over time.
