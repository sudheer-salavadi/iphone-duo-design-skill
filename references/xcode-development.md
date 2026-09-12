# iPhone Duo — Xcode, Simulator & APIs
Sources: https://developer.apple.com/iphone-duo/ (developer hub), Apple Tech Talks 111461 ("Prepare your app for iPhone Duo"), 111463 ("Strike a pose with adaptive layouts on iPhone Duo"), 111464 ("Leverage multiple displays and scenes on iPhone Duo") — researched 2026-09-12.

> **This entire file describes pre-release tooling.** As of this research date, Xcode 27.1 — the version that adds iPhone Duo support — is a **beta**, described by Apple's own developer hub as "coming later this month." It is not yet generally available, no Xcode 27.1 release notes page exists yet, and the classes named below (`UIHingeInteraction`, `UIViewReservedRegion`, `UIArrangementViewController`) have no confirmed standalone API reference pages — they're known only from Tech Talk video transcripts. Treat every API name/signature here as **subject to change before GA**, not a stable contract to design or build against yet.

## Simulator & Tooling

**Capabilities**
- Xcode 27.1 (beta) adds an **iPhone Duo simulator device** in Device Hub, with on-screen controls to open, close, rotate, and fold the simulated device — so poses can be checked without physical hardware once the beta ships.

**Limitations**
- No GA release date, build number, or release-notes page found as of this research. The existing (non-Duo) "Xcode 27 RC Release Notes" page was checked directly and contains zero mention of iPhone Duo, hinge, foldable, or Duo simulator support — Duo-specific release notes are presumably forthcoming with the 27.1 beta.
- No minimum deployment target was found for any of the new fold/hinge/arrangement APIs below.
- No new Xcode project template specific to Duo was found.

**Design implications**
- Any design review that assumes "we can just check this in Simulator" should first confirm Xcode 27.1 has actually shipped and been installed — as of Sept 2026 that capability does not yet exist in a released tool.

---

## SDK-Version-Dependent Behavior (important for scoping legacy vs. adapted apps)

**Capabilities / behavior matrix** (confirmed via Tech Talk 111461) — the *same app binary's* behavior on Duo depends on which SDK it was built against:
- Built with **no iOS 27 SDK** (older): runs at familiar iPhone size when closed; on the inner display, uses the space to the left of the status bar/camera area — essentially unadapted legacy behavior. Still works, doesn't crash, isn't Duo-aware.
- Built with **iOS 27 SDK**: extends further onto the inner display but stops short of the status bar/camera area.
- Built with **iOS 27.1 SDK**: reaches the full inner-display edge and lays out standard nav/toolbar controls **vertically** rather than horizontally — this is the behavior the HIG "Designing for iPhone Duo" page describes (see [iphone-duo.md](iphone-duo.md)).

**Design implications**
- "Does this app look right on Duo" is not a single yes/no — it depends on which SDK the app targets. A design audit should record which SDK tier the current build falls into, since the answer to "will this toolbar be vertical or horizontal" literally depends on it, not on anything the designer controls visually.
- Recommend against pointing engineering at 27.1-only APIs before 27.1 is GA if the plan is to ship before it's released — the fallback behavior (27 SDK or earlier) is still functional, just not Duo-optimized.

---

## New APIs: Fold State / Hinge

**Capabilities**
- **SwiftUI**: `.onHingeChange { previous, context in ... }` view modifier; `context.hinge` is optional (`nil` on non-foldable devices — must be guarded). `hinge.status` is `.closed` / `.partiallyOpen` / `.fullyOpen`; `hinge.angle` is a continuous `Angle`.
- **UIKit**: `UIHingeInteraction`, added via `UIView.addInteraction(_:)` (transcript-only, no confirmed reference page — see caveat at top of file).
- Apple's explicit guidance: this API is for **interactions and effects** (e.g. a parallax effect, a shutter-style camera control tied to fold angle) — **not for layout decisions.** Layout should use reserved regions / arrangement APIs (below), not raw hinge angle.

**Design implications**
- A design concept that uses continuous fold angle to *drive layout* (e.g., "the panel width scales smoothly with fold angle") is against Apple's own stated intent for this API — reserve fold-angle-driven behavior for interaction/effect polish, and drive actual layout structure off size classes and reserved regions instead.

---

## New APIs: Reserved Regions

**Capabilities**
- **SwiftUI**: `GeometryProxy.reservedRegions(kind:options:)` — kinds `.division` and `.occlusion`; `.includeInactive` option surfaces regions not currently active.
- **UIKit**: `UIView.reservedRegions(kind:)`, returning objects with a `.frame`.
- Types: SwiftUI `ReservedRegion`; UIKit `UIViewReservedRegion` (transcript-only, tied to iOS 27.1 SDK — see caveat).

**Design implications**
- This is the programmatic counterpart to the "four reserved regions" concept in [iphone-duo.md](iphone-duo.md) (outer camera, inner camera, folding region, system components) — a design spec that names those regions qualitatively can be implemented against this API rather than requiring engineering to hand-derive geometry per pose.

---

## New APIs: Split/Overlay Layout Containers (Arrangement Views)

**Capabilities**
- **SwiftUI `ArrangementView`**: takes `primary`/`secondary` view builders, must be inside a `NavigationStack`. Styled via `.arrangementViewStyle(.split)`, `.arrangementViewStyle(.split.axes(.horizontal))`, or `.arrangementViewStyle(.overlay)`. Environment key `\.overlayArrangementZIndex` exposes z-order info for the overlay style.
- **UIKit `UIArrangementViewController`**: used as the root view controller of a `UINavigationController`; methods `setViewController(_:for:)` (`.primary`/`.secondary`), `updateArrangement(_:)`, `state(for:)`.

**Design implications**
- This is the concrete implementation of the "split arrangement / overlay arrangement" concepts described in [iphone-duo.md](iphone-duo.md) — confirms those are real, buildable container types, not just a HIG design pattern name. A design spec calling for "split arrangement" or "overlay arrangement" maps directly to `.arrangementViewStyle(.split)` / `.arrangementViewStyle(.overlay)`, which is useful to know when estimating engineering scope.
- Per the HIG guidance already captured: keep navigation *outside* the arrangement view (wrap it in the required `NavigationStack`/`UINavigationController`, don't nest navigation inside `primary`/`secondary`).

---

## New APIs: Multiple Displays / Scenes

**Capabilities**
- New windows/scenes can only be created on the **inner** display — the outer display is reserved for the existing single scene and cannot host a second new window.
- `.sceneAccessory` (SwiftUI) modifier and a `CameraCaptureAccessory` type let an app show supplementary camera UI on the outer display while primary camera UI stays on the inner display; availability is observed via `.onAvailabilityChange`, and is available specifically when the app is full-screen on the inner display with an active camera session.
- Preferred pose/size-class detection: SwiftUI `@Environment(\.horizontalSizeClass)` / `\.verticalSizeClass`; UIKit `traitCollection.horizontalSizeClass` / `.verticalSizeClass` — **not** interface orientation.
- `UIScreen.main` is being deprecated for this purpose; guidance is to get displays dynamically from the window scene instead.

**Design implications**
- An outer-display "second window" or picture-in-picture-style multi-window concept on the *outer* display specifically is not feasible as described — new scenes are inner-display-only. A camera-accessory-on-outer-display pattern is the one confirmed exception, and it's narrowly scoped (full-screen inner-display camera session only).
- Any design spec that still frames Duo adaptation in terms of "portrait vs. landscape orientation" is using the wrong mental model per Apple's own migration guidance — reframe in terms of size class, matching the same discipline already required for standard iPhone/iPad (see [layout-navigation.md](layout-navigation.md)).

---

## Migration Notes (existing apps adapting for Duo)

**Capabilities / required changes** (from Tech Talk 111461):
- Remove `UIRequiresFullScreen` from Info.plist if present — this pre-existing key opts an app out of adaptive resizing entirely, which blocks Duo adaptation.
- Adopt continuous resizing behavior instead of assuming discrete size jumps.
- Replace interface-orientation checks with size-class logic.
- Handle **asymmetric safe-area insets** per side — an app can no longer assume left/right safe areas are equal (this follows directly from vertical toolbar placement pushing controls to one side — see [iphone-duo.md](iphone-duo.md)).
- Related (not Duo-exclusive but relevant to the same edge-to-edge adaptation work): `ConcentricRectangle` (SwiftUI) / `UICornerConfiguration` (UIKit) for corner-matching.

**Design implications**
- If a design review turns up `UIRequiresFullScreen` still present in an app's Info.plist, that is a concrete, named blocker to flag to engineering before any Duo-specific design work is scoped — no layout adaptation will occur at all until it's removed.

---

## App Icons & Launch Screens — no Duo-specific requirement found

**Capabilities / Limitations**
- Neither "Configuring your app icon" (Xcode docs) nor the HIG App Icons page nor "Specifying your app's launch screen" mention iPhone Duo, dual displays, or an outer/inner icon or launch-image distinction, as of this research pass (both fetched and checked directly).
- Best read: a single standard 1024×1024 source icon (via Icon Composer or asset catalog) and the standard `UILaunchScreen` Info.plist configuration are still the whole story for Duo — but this is an **absence-of-evidence inference**, not a positive confirmation from Apple. Treat as "no news found," not "confirmed unchanged."
- The consumer-facing unfold transition (content scaling up, controls staying in place when opening the device) appears to be **system-level** UI behavior, not something requiring an app-supplied asset — no developer documentation surfaced describing it as an app-level launch-screen requirement.

**Design implications**
- Don't budget separate icon or launch-screen design work for Duo unless/until Apple publishes a stated requirement — re-check this specific gap when Xcode 27.1 ships, since new tooling releases are when such requirements typically surface.
