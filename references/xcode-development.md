# iPhone Duo — Xcode, Simulator & Core APIs
Sources: developer.apple.com/iphone-duo/ (developer hub) plus Apple's Sept 2026 Tech Talks — "Prepare Your App for iPhone Duo," "Strike a Pose with Adaptive Layouts on iPhone Duo," and "Leverage Multiple Displays and Scenes on iPhone Duo." Talk transcripts were reviewed directly, including their on-screen code samples, which is the primary source for the exact API names/signatures below (higher confidence than the earlier secondary-source web research this file started from). See [vertical-toolbars.md](vertical-toolbars.md) for the full toolbar/tab-bar/sheet API surface and [camera.md](camera.md) for the dual front-camera API surface — both are big enough to warrant their own files.

> **This entire file describes pre-release tooling.** Xcode 27.1 — the version that adds iPhone Duo support — is a **beta**, described by Apple's own developer hub as "coming later this month" (not yet GA as of this research). No Xcode 27.1 release notes page exists yet. Treat every API name/signature here as **subject to change before GA**, not a stable contract to build against yet — though the exact code snippets below come directly from Apple's own talks, not a secondhand paraphrase.

## Simulator & Tooling

**Capabilities**
- Xcode 27.1 (beta) adds an **iPhone Duo simulator device** in Device Hub, with on-screen controls to open, close, rotate, and fold the simulated device — so poses can be checked without physical hardware once the beta ships.
- Apple's own talk refers to a renamed/expanded **"appability"** app-modernization *skill* (its word) shipping with Xcode 27.1, now supporting SwiftUI in addition to UIKit, and iPhone Duo specifically. The exact mechanism (an Xcode-integrated tool? an automated audit pass? something else) wasn't spelled out in the talk — treat "run appability to check your app" as the right instinct, but confirm what it actually is once Xcode 27.1 ships rather than assuming it's a simple lint pass. It's the same capability referenced (under an earlier name) in Apple's "Modernize Your UIKit App" talk from WWDC26 ("Dubdub26").

**Limitations**
- No GA release date or release-notes page found as of this research; the (non-Duo) "Xcode 27 RC Release Notes" page was checked directly and contains zero mention of iPhone Duo, hinge, foldable, or Duo simulator support.
- No minimum deployment target was found for any of the new fold/hinge/arrangement APIs below.
- No new Xcode project template specific to Duo was found.

**Design implications**
- Any design review that assumes "we can just check this in Simulator" should first confirm Xcode 27.1 has actually shipped and been installed.
- Run "appability" before treating a design as validated for Duo — it's a concrete, named capability for exactly this audit, not something to reinvent as a manual checklist.

---

## SDK-Version-Dependent Behavior (important for scoping legacy vs. adapted apps)

The *same app binary's* behavior on Duo depends on which SDK it was built against:
- **Built against a pre-iOS-27 SDK**: closed → familiar iPhone size; open (inner display) → uses the space to the left of the status bar/camera area. Unadapted legacy behavior — still works, isn't Duo-aware.
- **iOS 27 SDK**: extends further onto the inner display but stops short of the status bar/camera area.
- **iOS 27.1 SDK**: reaches the full inner-display edge, and standard nav/toolbar controls lay out **vertically** under the status bar — this is the behavior [iphone-duo.md](iphone-duo.md) and [vertical-toolbars.md](vertical-toolbars.md) describe.

**Related**: with iOS 27, **iPhone Mirroring on Mac** lets people resize an app larger than ever via the Mac; opening/closing iPhone Duo is treated the same way as any other resize event under mirroring — it's still fundamentally "an iPhone app," just one that can be resized more than before.

**Design implications**
- "Does this app look right on Duo" depends on which SDK tier the build falls into — a design audit should record that tier, since it directly determines whether toolbars render vertically or horizontally, independent of anything the designer controls visually.
- Don't scope 27.1-only APIs into a release that ships before 27.1 is GA — the older-SDK fallback behavior is still functional, just not Duo-optimized.

---

## Size Classes (confirmed exact matrix)

**Capabilities** — the full per-display, per-orientation size-class matrix:
- **Outer display, portrait**: regular vertical / compact horizontal (same as any other iPhone).
- **Outer display, landscape**: compact vertical / compact horizontal (same as any other iPhone).
- **Inner display, any orientation, full-screen**: **regular horizontal AND regular vertical** — the additional space is meant to support richer content like sidebars. This matrix describes a **full-screen** app; the talk doesn't state what size class applies in split-view multitasking (see below) — don't assume regular width still holds when the app only occupies half the inner display.

```swift
// SwiftUI
@Environment(\.horizontalSizeClass) private var horizontalSizeClass
@Environment(\.verticalSizeClass) private var verticalSizeClass

// UIKit
traitCollection.horizontalSizeClass
traitCollection.verticalSizeClass
```

**Limitations**
- The same talk ("Prepare Your App for iPhone Duo") states both that **"the inner display doesn't honor your supported interface orientations"** (orientation locks configured for the app simply don't apply there) *and*, later, that **"iPhone Duo respects your supported interface orientations, but your app will scale on the inner display"** (see the `UIRequiresFullScreen` section below for that exact quote). These two statements sit in tension and the skill doesn't attempt to resolve them — the practical takeaway both agree on is: **don't drive layout decisions off interface orientation on the inner display; use size classes instead**, regardless of exactly what "respects" means for orientation locking itself.
- The outer display behaves like any other iPhone (orientation locks apply normally there), and Duo is explicitly called out as "a great opportunity to support landscape" for the tent/stand pose.
- Avoid checking interface orientation for layout decisions anywhere in the app — use size classes instead (same discipline as iPad idiom guidance).

**Design implications**
- A feature spec that says "lock this screen to portrait" needs an explicit caveat for Duo: that lock is meaningless on the inner display.
- Any layout decision framed as "portrait vs. landscape" should be re-framed as "compact vs. regular," since that's the only reliable signal on the inner display.

---

## Screen & Display Access

```swift
// Avoid referencing the main screen on a two-display device.
// Access the screen dynamically from the window scene instead.
let screen = window?.windowScene?.screen
```

```swift
// Before
let screenScale = UIScreen.main.scale
// After
let screenScale = traitCollection.displayScale
```

**Limitations**
- `UIScreen.main` is ambiguous on a two-display device and is being **deprecated** for this reason — don't reference "the main screen" anywhere in code being reviewed for Duo-readiness. Prefer local concepts (environment, trait collection, scene bounds) over screen references at all where possible.

**Design implications**
- If a design review surfaces engineering code that still reads `UIScreen.main` for anything (not just scale), flag it as a concrete Duo-readiness gap, not a style nitpick — it's explicitly called out as the wrong pattern for this device.

---

## Concentric Corners

```swift
// SwiftUI
ConcentricRectangle()
    .fill(Color.green)
    .padding(8.0)
    .ignoresSafeArea()

// UIKit
// UICornerConfiguration
```

**Design implications**
- These (iOS 26-introduced, not Duo-exclusive) APIs are now updated to match Duo's differently-shaped screens — any custom-cornered container in a design should use these rather than hardcoded corner radii, so it automatically matches the actual device shape (outer vs. inner display) without device-specific tuning.

---

## Navigation Containers, Sidebar & Safe Areas

Standard navigation components adapt automatically when used as system containers (not custom-built bars — see [vertical-toolbars.md](vertical-toolbars.md) for that constraint):
- SwiftUI `NavigationSplitView` / UIKit `UISplitViewController` — closed: columns collapse to a single stack; open: columns appear tiled and as overlays.
- SwiftUI `TabView` / UIKit `UITabBarController` — tabs appear on inner and outer displays, laying out vertically when appropriate. Opt into a sidebar on the inner display:

```swift
// SwiftUI
TabView { … }
    .defaultTabBarPlacement(.sidebar)

// UIKit
tabBarController.sidebar.preferredPlacement = .sidebar
```

**Safe areas** — navigation bars, toolbars, and tab bars lay out **outside** the safe area and automatically avoid system UI (status bar) and hardware (camera): horizontal bars provide top/bottom insets, vertical bars provide leading/trailing insets. This is *why* the asymmetric-inset handling below matters — a vertical bar's leading/trailing inset is what a naive "assume both sides are equal" calculation gets wrong. Foreground/interactive content should be placed within the safe area; background/full-bleed content may extend past it:

```swift
// UIKit — align foreground content to the safe area
foreground.frame = view.bounds.inset(by: view.safeAreaInsets)
```

```swift
// SwiftUI — let background content extend past the safe area
.ignoresSafeArea()

// UIKit
backgroundView.frame = view.bounds
```

**Limitations — asymmetric safe areas**, especially on Duo (e.g. vertical buttons appearing on one side in landscape or split-view multitasking):

```swift
// Avoid assuming insets on opposite sides are equal
let width = view.bounds.width - view.safeAreaInsets.left * 2

// Handle each side independently
let width = view.bounds.inset(by: view.safeAreaInsets).width
```

Layout margins are asymmetric the same way — this lets foreground content sit closer to vertical buttons/the status bar on one side while preserving margin on the opposite side.

**Design implications**
- A layout audit that computes width/spacing by doubling one side's inset is a concrete, checkable bug on Duo — the correct pattern insets by each side's actual value independently.
- Testing must include split-view multitasking specifically (drag an app via the home indicator to one side in Device Hub) — vertically-laid-out content can appear on either side depending on which side the app is dropped onto.

---

## `UIRequiresFullScreen` — one primary-source statement, one unverified secondary claim (do not silently pick one)

- **"Prepare Your App for iPhone Duo" (David Jackson, UI Frameworks)** states directly: *"iPhone Duo will continue to honor the `UIRequiresFullScreen` key, but your app will still resize when someone opens or closes their iPhone Duo. iPhone Duo respects your supported interface orientations, but your app will scale on the inner display, including in split view multitasking."* — i.e., the key is still honored, but it does **not** block open/close resizing.
- A separate migration-guidance source (an earlier, secondary-source research pass citing a different Tech Talk) said to **remove** `UIRequiresFullScreen` because it "opts an app out of adaptive resizing entirely." This may describe a different, more complete tier of adaptation (e.g., full edge-to-edge vertical-bar behavior) that the key still blocks even though basic resizing isn't blocked — but this wasn't stated explicitly in either source.
- **Don't collapse this into a single confident claim.** If it matters for a real decision, treat "does this key need to be removed for full Duo adaptation" as an open question to verify against Apple's actual (not-yet-published) Xcode 27.1 release notes, rather than picking whichever source is convenient.

---

## Multitasking, Multiple Windows & Scene Accessories

**Capabilities**
- **Split-view multitasking is mandatory, not opt-in** — every app participates in the system's side-by-side layout. There's a second distinct system layout (Picture-in-Picture video pinned/stacked with an app — see [iphone-duo.md](iphone-duo.md)) that the app is expected to **handle identically** to plain split view, via size class + **scene geometry**, with no special-case logic between the two.
- iPhone Duo is the first iPhone to support **multiple instances of an app's UI** (multi-window), the same mechanism as iPad — if an app already supports this on iPad, it works on Duo too.
- **Scene accessories** (general iPhone/iPad concept, not Duo-exclusive): let an app pair supplementary content across multiple displays at once — e.g. using an iPhone as a game controller for an external display. Availability is dynamic (enabled by default, can be toggled at any time by the system) — observe changes via Swift's observation tracking so the app stays in sync.
- **Camera Capture Accessory** (Duo-specific use of scene accessories): pairs supplementary UI on the **outer** display (e.g. a teleprompter) while the main camera UI stays on the **inner** display — available specifically when the app is full-screen on the inner display with an active camera session. Full detail and code in [camera.md](camera.md); the accessory registration itself is general-purpose:

```swift
struct CameraRootView: View {
    @State private var model = TeleprompterModel()

    var body: some View {
        CameraView(model: model)
            .sceneAccessory {
                CameraCaptureAccessory(isEnabled: $model.isEnabled) {
                    TeleprompterView(model: model)
                }
                .onAvailabilityChange { newValue in
                    model.isAvailable = newValue
                }
            }
            .toolbar {
                TeleprompterToggle(isEnabled: $model.isEnabled)
                    .disabled(!model.isAvailable)
            }
    }
}
```

**Limitations**
- On iPad, new windows/scenes can be created **at any time**. On iPhone Duo, **new windows/scenes can only be created on the inner display — never the outer** — a dynamic, Duo-unique restriction. The action to request a new scene (`UIWindowSceneActivationAction`, referred to in the talk as "the UI window scene activation action") **automatically hides itself when unavailable**, so the app doesn't need to manually gate the affordance visibility. Even so, the talk separately says to **handle errors when requesting new scenes** — the auto-hiding affordance reduces how often a request would fail, it doesn't guarantee a request made through some other path can't still fail.

**Design implications**
- An outer-display "second window" concept is not feasible as described — new scenes are inner-display-only, full stop. The one confirmed way to show something on the outer display while the inner display runs the main UI is the Camera Capture Accessory pattern specifically (camera apps only, so far).
- A design that includes a "new window" button/menu item doesn't need a manual "disabled on outer display" state designed for it — the system handles that affordance's visibility automatically.

---

## New APIs: Fold State / Hinge

```swift
struct InstrumentView: View {
    @State private var pitchBend: Double = 0

    var body: some View {
        GuitarView(pitchBend: pitchBend)
            .onHingeChange { _, context in
                // A null hinge means the device doesn't have one
                if let hinge = context.hinge, hinge.status == .partiallyOpen {
                    pitchBend = calculatePitchBend(angle: hinge.angle)
                } else {
                    pitchBend = 0
                }
            }
    }

    private func calculatePitchBend(angle: Angle) -> Double { ... }
}
```
- **UIKit**: `UIHingeInteraction`, added via `UIView.addInteraction(_:)`.
- `hinge.status` is `.closed` / `.partiallyOpen` / `.fullyOpen`; `hinge.angle` is a continuous `Angle`.
- The two concrete examples actually given in the talks: an app-level **pitch-bend/whammy-bar effect** on a guitar app (code above), and a **system-level** example — the Lock Screen/opening wallpaper reacts to hinge angle with a zoom effect as the device opens (this second one is system UI behavior, not something a third-party app implements, but illustrates the intended category of use).
- "Leverage Multiple Displays and Scenes on iPhone Duo" states this API is for **interactions and effects, not layout decisions** — layout should use reserved regions / arrangement APIs (below) instead of raw hinge angle. "Strike a Pose with Adaptive Layouts on iPhone Duo" is consistent with this (it points to the multiple-displays talk for hinge-driven interactions and covers layout separately via reserved regions/arrangements), but only the first talk states the interactions-not-layout rule explicitly — treat it as one clear statement, not two independent confirmations.

**Design implications**
- A design concept that uses continuous fold angle to *drive layout* (e.g., "the panel width scales smoothly with fold angle") is against Apple's stated intent for this API — reserve fold-angle-driven behavior for interaction/effect polish (following the pitch-bend example's shape: a normalized value derived from angle, only while partially open, reset otherwise), and drive actual layout structure off size classes and reserved regions instead.

---

## New APIs: Reserved Regions

```swift
// SwiftUI — query division regions (e.g. the fold)
GeometryReader { proxy in
    let regions = proxy.reservedRegions(kind: .division)
}

// Include inactive regions too
GeometryReader { proxy in
    let regions = proxy.reservedRegions(kind: .division, options: .includeInactive)
    let frames = regions.map(\.frame)
}

// Query occlusion regions (e.g. the active FaceTime camera)
GeometryReader { proxy in
    let regions = proxy.reservedRegions(kind: .occlusion)
    let frames = regions.map(\.frame)
}
```

```swift
// UIKit
let regions = view.reservedRegions(kind: .division)
let frames = regions.map(\.frame)
```

**Design implications**
- This is the programmatic counterpart to the "reserved regions" concept in [iphone-duo.md](iphone-duo.md) — a design spec that names those regions qualitatively can be implemented against this API rather than requiring engineering to hand-derive geometry per pose.
- Querying inactive regions (e.g. the fold when flat) is a legitimate way to keep a layout decision (like grid column count) stable across a fold transition, rather than reacting only once the fold becomes active.
- "Prepare Your App for iPhone Duo" separately names this same reserved-region concept as the tool for **building custom bars or edge-to-edge UI** that needs to use as much available space as possible without colliding with system UI — if a design calls for fully custom chrome (not a system nav/toolbar/tab bar), this API, not a hand-measured layout, is the intended mechanism.

---

## New APIs: Split/Overlay Layout Containers (Arrangement Views)

```swift
// SwiftUI
NavigationStack {
    ArrangementView {
        PlayerView()
    } secondary: {
        UpNextView()
    }
    .arrangementViewStyle(.split)          // or: .split.axis(.horizontal), or .overlay
}
```

```swift
// UIKit — root VC of a UINavigationController; primary/secondary set via
// setViewController(_:for:); the axis is restricted via a UISplitArrangementType
// passed to updateArrangement(_:) (exact call shape not fully specified in the
// talk — treat this as the closest faithful paraphrase, not a verified signature)
let arrangementVC = UIArrangementViewController()
let navController = UINavigationController(rootViewController: arrangementVC)

arrangementVC.setViewController(PlayerViewController(), for: .primary)
arrangementVC.setViewController(UpNextViewController(), for: .secondary)
arrangementVC.updateArrangement(UISplitArrangementType(axis: .horizontal))
```

Reading z-index in an overlay arrangement — the talk says to query this to detect front/back placement and switch between collapsed/expanded representations, using the "up next" view as its example (the specific `zIndex > 0` → collapsed mapping below is this skill's inference from that example, not a verbatim rule stated in the talk):

```swift
// SwiftUI
@Environment(\.overlayArrangementZIndex) private var zIndex: Int
// inferred from the demo: zIndex > 0 → treat as collapsed/behind; else expanded/front

// UIKit
let primaryState = arrangementVC.state(for: .primary)
let collapsed = (primaryState?.zIndex ?? 0) > 0   // same inference as above
```

**Design implications**
- This is the concrete implementation of the "split arrangement / overlay arrangement" concepts in [iphone-duo.md](iphone-duo.md). A design spec calling for "split arrangement" or "overlay arrangement" maps directly to `.arrangementViewStyle(.split)` / `.arrangementViewStyle(.overlay)`.
- `.axis(.horizontal)` (or `.vertical`) restricts a split to one axis; if the arrangement can't split along the restricted axis given the current aspect ratio, it falls back to showing only the primary view — a design should account for that single-view fallback state, not assume the split is always visible.

---

## App Icons & Launch Screens — no Duo-specific requirement found

**Capabilities / Limitations**
- Neither "Configuring your app icon" (Xcode docs) nor the HIG App Icons page nor "Specifying your app's launch screen" mention iPhone Duo, dual displays, or an outer/inner icon or launch-image distinction, as of this research pass.
- Best read: a single standard 1024×1024 source icon (via Icon Composer or asset catalog) and the standard `UILaunchScreen` Info.plist configuration are still the whole story for Duo — but this is an **absence-of-evidence inference**, not a positive confirmation from Apple. Treat as "no news found," not "confirmed unchanged."

**Design implications**
- Don't budget separate icon or launch-screen design work for Duo unless/until Apple publishes a stated requirement.

---

## Companion Talks & Articles Cited Across This Skill

For direct citation when precision matters:
- "Design for iPhone Duo" (Marcos, Vince — Apple Design team) — HIG-level design principles.
- "Prepare Your App for iPhone Duo" (David Jackson — UI Frameworks) — Xcode/SDK/size-class/safe-area fundamentals.
- "Raise the Bar with iPhone Duo" (Maria, Anna) — vertical toolbar/tab-bar/sheet API surface, see [vertical-toolbars.md](vertical-toolbars.md).
- "Strike a Pose with Adaptive Layouts on iPhone Duo" (Maria, Harry) — displacement, reserved regions, arrangement views.
- "Leverage Multiple Displays and Scenes on iPhone Duo" (Chris Donigan, Alex Mueller) — hinge API, split-view multitasking, scene accessories.
- "Build a Great Camera Experience for iPhone Duo" (Tun) — see [camera.md](camera.md).
- "Modernize Your UIKit App" (WWDC26/"Dubdub26") — origin of the "appability" tool, general flexible-layout principles.
- "What's New in SwiftUI" (WWDC26/"Dubdub26") — visibility priority APIs (see [vertical-toolbars.md](vertical-toolbars.md)).
- "Support the Center Stage Front Camera in Your iOS App" (WWDC26) — square-sensor camera background, see [camera.md](camera.md).
- Developer articles: "Choosing a Camera by the Direction It Faces," "Supporting Device Rotation in Your Camera App" (both cited in [camera.md](camera.md)).

These talks are Apple's own primary source (reviewed directly, including on-screen code), which is why this skill treats their API names/behavior as higher-confidence than the general web research that originally seeded this file — even though the underlying tooling (Xcode 27.1) is still pre-GA.
