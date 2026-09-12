# iPhone Duo (Foldable) — Design Fundamentals
Sources: https://developer.apple.com/design/human-interface-guidelines/designing-for-iphone-duo (HIG page, added September 9, 2026), plus Apple's Sept 2026 Tech Talks — "Design for iPhone Duo," "Strike a Pose with Adaptive Layouts on iPhone Duo," and "Leverage Multiple Displays and Scenes on iPhone Duo" (transcripts reviewed directly, including their on-screen code samples).

iPhone Duo is Apple's foldable iPhone: dual displays (outer + inner), a front-facing camera on each display, and a central hinge that lets the device hold multiple poses. This file covers what's *specific to Duo* at the design-pattern level; for the engineering/API surface behind these patterns, see [xcode-development.md](xcode-development.md) (core layout/scene APIs), [vertical-toolbars.md](vertical-toolbars.md) (the full toolbar/tab-bar API), and [camera.md](camera.md) (dual front-camera APIs). For everything not called out anywhere in this skill, standard iOS design patterns apply — see [standard-iphone-vs-duo.md](standard-iphone-vs-duo.md) for the explicit carries-over/changes breakdown.

## Anatomy: Outer + Inner Display

**Capabilities**
- Outer display: used when the device is closed — has its own front-facing camera, in a fixed corner, always visible. Wider and shorter than a traditional iPhone display.
- Inner display: used when the device is open — has its own front-facing camera (the first under-display camera on iPhone), hidden until active.
- Both displays run the same app; the system decides which is active based on fold state. Both are Super Retina XDR OLED with ProMotion and Always-On support (see [device-specs.md](device-specs.md)).

**Limitations**
- The outer camera position is fixed and always visible — it permanently occupies screen real estate on the outer display, not just at certain times. It expands to accommodate Live Activities and the redesigned status bar sits alongside it.
- The inner camera is hidden until active, but still a reserved region the layout must account for once it activates — if a camera viewfinder is central to your experience, keep important content/controls clear of that area.

**Design implications**
- Treat the outer-display camera cutout as a permanent layout constraint on that display, the same way Dynamic Island is permanent on standard iPhone — never assume a clean rectangular outer-display canvas.
- On the *closed* display specifically, controls move to the **right side** (not just "the side") — this is deliberately tuned for right-thumb reachability while holding the device one-handed, leaving an uninterrupted content area to the left comparable to a traditional iPhone's usable width.

---

## Device Poses

**Capabilities**
- Supports book-fold (partially folded, held like a book), flat/laptop-like (open, inner display facing you, set on a table), stand (standing on its own, resting on its hinge "head"), in addition to fully closed (outer display only) and fully open (inner display, flat).

**Limitations**
- "Supporting the device's various poses doesn't mean designing a custom layout for each one" — Apple explicitly discourages a per-pose bespoke-layout strategy.
- **Portrait + partially folded is a distinct, named exception to the general rule**: interactive elements move to the **bottom half** of the display (not the side), to stay reachable while keeping the device stable resting on a surface. This is different from the landscape/book-fold behavior below.

**Design implications**
- Design against size classes (compact/regular) that the system derives from the current pose/fold state, not against an enumerated list of poses — a design spec that lists "book mode layout," "flat mode layout," "stand mode layout" as separate deliverables is working against the platform's intended adaptive model. One adaptive layout, driven by size class, is the correct scope.
- An **optional** hands-free "tabletop" layout is explicitly sanctioned when the device is propped up (laptop-like or standing): media/visibility-at-a-distance content works well in the **top region**, tappable controls work well in the **bottom region** (a stable surface for touch). If you build this optional layout, it must still expose the same controls and hierarchy as every other pose — never tie functionality to one specific pose.
- For games specifically: "make your game playable in every device pose" is a stated requirement, not an aspiration. Prefer changing aspect ratio over letterboxing/pillarboxing across poses — a fixed-aspect-ratio game concept needs explicit rework for Duo, not just black bars.

---

## Reserved Regions (hard layout constraints)

**Capabilities / Limitations** — four regions structure available space and must be treated as off-limits to arbitrary content:
1. **Outer front-facing camera** — always present on the outer display; expands to accommodate Live Activities.
2. **Inner front-facing camera** — present only once active, but reserved the moment it can activate.
3. **Folding region** — space around the hinge, conditional on current fold/pose state. Queryable in code as an **occlusion or division region** — see below.
4. **System components** — status bar/home indicator-equivalents, which adapt to the above automatically.

Programmatically, these map to two kinds of **reserved regions** (full API detail in [xcode-development.md](xcode-development.md)):
- **Division regions** — divide a larger area into multiple smaller areas. The fold is a division region: it has **zero width and is inactive when the device is flat**, and becomes active (nonzero width) only when partially folded.
- **Occlusion regions** — occlude (hide) part of your view's bounds without dividing the area. The active FaceTime camera is an occlusion region: active only while the camera is in use, inactive otherwise.
- Both kinds can be queried **including inactive state** — useful for layout decisions that should stay stable across a transition (e.g., preferring an even number of grid columns regardless of whether the fold is currently active, so column count doesn't jump when the device folds/unfolds).

**Design implications**
- Any full-bleed or edge-to-edge visual concept must be checked against all four reserved regions, not just the outer-camera cutout — a design that "just" avoids the camera notch but ignores the folding region will still break.
- Because the folding region is conditional (changes with pose), a static Sketch/Figma mockup validated in one pose is not sufficient evidence of feasibility — the design needs to be checked across at least closed, book-fold, and fully-open poses.
- Querying *inactive* regions is a legitimate design tool, not just an engineering detail — use it to justify layout decisions ("always reserve space as if the fold could appear") that avoid jarring changes at the moment of a pose transition.

---

## Displacement (the core adaptation pattern)

**What it is**: displacement adjusts the **frame of existing elements** based on available space — moving, resizing, or reorganizing what's already there — to keep content visible, reachable, and unobstructed as the device folds. It is not about hiding or removing functionality; keep content, functionality, and layout available in every pose. Apple's own analogy: a photo printed across two facing pages of a book becomes hard to read near the spine — content and controls that span the fold behave the same way, so displacement moves them clear of it.

**Capabilities**
- Scope is flexible: a single element, an entire container, or a larger part of a layout can all be displaced — choose the scope that matches the content.
- Elements that work together should displace **together**, to preserve their visual relationship (e.g., a context menu for a selected photo moves *with* the photo and aligns around the fold, rather than being centered independently).
- The system automatically displaces lightweight contextual UI for you: action sheets, alerts, menus, and popovers reposition around a reserved region automatically. A split-view app (e.g. Reminders) keeps both columns visible by adjusting their width into an even 50/50 split around the fold. A grid layout (e.g. Fitness) can preserve outer margins while increasing spacing around the hinge, keeping each grid cell within its own region instead of splitting one across the fold.
- Beyond position and size, other visual properties of a displaced element can adapt too (not just frame geometry).

**Limitations**
- **Continuous scrolling content (articles, feeds, documents, lists) does not displace.** These already adapt through scrolling — moving them between regions would interrupt continuity, so leave them alone.
- Moving an element far from its source weakens the visual relationship between them — avoid excessive displacement distance even when it's technically possible.
- Where an element should displace *to* depends on pose, and is not a single fixed rule:
  - **Book-fold (partially folded, portrait/landscape held like a book)**: elements like alerts move to the **trailing side** — this keeps them near where they'll end up once the device fully closes, preserving continuity into the outer-display experience.
  - **Tabletop/stand pose**: top region for visibility-at-a-distance content (e.g. an alert), bottom region for interactive controls (e.g. media controls) — see the optional tabletop layout note under Device Poses.
  - When multiple regions are plausible, prioritize whichever is most contextually relevant — e.g. a focused search field stays positioned over the content/keyboard it's searching, adapting width and position as the device folds, the same way search-over-keyboard behaves on standard iPhone.

**Design implications**
- Before spec'ing a custom displacement behavior, check whether a system component already does it for free (split views, sheets, alerts, popovers all auto-displace) — only manually-laid-out, custom UI needs the reserved-regions API applied by hand (see [xcode-development.md](xcode-development.md)).
- A design that hides content/controls on fold rather than repositioning them is not "displacement" as Apple defines it, and likely violates the "keep functionality available regardless of pose" principle — repositioning, not removal, is the correct default.
- A design that displaces a continuously-scrolling feed/list is fighting the intended model — leave scrollable content alone and displace only non-scrolling elements (controls, alerts, fixed-position content).

---

## Dynamic Layouts

**Capabilities**
- System-provided layout margins and safe area insets automatically account for reserved regions and adapt as the device folds/unfolds.
- Standard layout containers (e.g., the split view used in Notes) resize their panes automatically in response to fold state.

**Limitations**
- Fixed widths and any display-specific dependency (hardcoding "outer display is X points wide") are explicitly against guidance — the outer/inner display sizes are not meant to be treated as fixed targets.

**Design implications**
- A mockup built to one exact outer-display pixel width, without also validating against the inner display and mid-fold transition, is not Duo-feasible as specified — validate against size classes, the same discipline as standard iPhone compact/regular, not fixed dimensions.
- Prefer adopting a system layout container (split view, arrangement view) that adapts automatically over hand-rolling custom fold-aware layout logic — the platform explicitly optimizes for this.

---

## Split Views on Duo

(See also the generic [Split Views](https://developer.apple.com/design/human-interface-guidelines/split-views) HIG page — split views on standard iOS are only used in regular, not compact, environments.)

**Capabilities**
- A split view expands on the inner display (revealing primary + detail panes side-by-side) and collapses to a single pane on the outer display — the same adaptation model as a regular-vs-compact size class transition on standard iPhone/iPad.
- Example from Apple's own apps: Mail shows the email list *or* a single email when closed (outer display), and both side-by-side when open (inner display).

**Limitations**
- This adaptation is automatic only if the split view is built with the standard system component — a custom-built "looks like a split view" pattern does not get this behavior for free.

**Design implications**
- Any master-detail concept (list + detail, inbox + reading pane) is a strong natural fit for Duo and should be designed as a system split view, not a custom two-pane layout — this is the single clearest "this maps directly to a system capability" pattern for Duo.
- Three named strategies exist specifically for adapting content to the wide **inner** display, beyond a plain split view: (1) split views for multi-level hierarchy, (2) "optimize for the wide inner screen" — e.g. a vertically-stacked layout that rearranges into two columns when there's more horizontal space (Apple's Music example), and (3) presenting a tab bar as a **sidebar** on the inner display — reserved for information-dense apps (Apple's Health example), not appropriate for every app. Whichever is used, don't limit functionality to one pose or the other, and don't change the app's hierarchy between outer and inner displays — people open/close the device frequently mid-task, so the experience must stay predictable and consistent.

---

## Arrangement Views

**Capabilities**
- **Split arrangement**: divides the available area horizontally (in the wider pose) or vertically (in the taller pose) — the axis can also be restricted to one direction only.
- **Overlay arrangement**: positions views on top of one another (z-stacked); on partial fold, an overlay arrangement can instead position primary/secondary content side by side, giving the secondary content more room.
- Choosing between them: follow your app's existing patterns first — an HStack/VStack-like layout maps to split, a ZStack-like layout maps to overlay. Absent an existing pattern, use **overlay** when there's a clear foreground/background relationship where the background can tolerate being partially obscured (e.g. Accessibility Reader: controls in front, scrollable readable content behind). Use **split** when there's a main/detail relationship where **neither view should ever be obscured** (e.g. Podcasts: transcript is detail info about the main playing content).

**Limitations**
- Keep navigation *outside* arrangement views — navigation containers (e.g., a navigation stack or split view) should wrap around an arrangement view, not live inside it; **arrangement views provide no navigation infrastructure of their own.**
- **Don't nest an arrangement view inside a scrollable container** (List/ScrollView) — this is an explicit, separate constraint from the navigation-nesting rule.
- This is not a separate standalone HIG page (as of this writing, `arrangement-views` 404s); all current qualitative guidance on it lives on the Designing for iPhone Duo page itself — the concrete API (`ArrangementView`/`UIArrangementViewController`) is documented in [xcode-development.md](xcode-development.md).

**Design implications**
- A design that nests a tab bar or navigation stack *inside* an arrangement view's pane is against the stated pattern — navigation chrome belongs at the outer layer, wrapping the arrangement view.
- Overlay arrangement is the right tool for "two things with a foreground/background relationship, one at a time or layered" (e.g., a stacked card that separates into two panes on partial fold) rather than for permanently-equal-priority content, which should be a split arrangement instead.

---

## Vertical Controls (Toolbars & Tab Bars)

**Capabilities**
- Toolbars, tab bars, and navigation controls move to the side (a vertical arrangement) on iPhone Duo to preserve vertical space for content — except on the inner display in portrait pose, which keeps familiar horizontal bars (there's ample vertical space there already). This vertical region is shared with dynamic system elements (Live Activities, the redesigned status bar), not exclusively owned by app content.
- Toolbar items support a **visibility priority** system: assign priority at the group level first, then individual items within a group, to control what collapses into overflow under space pressure.
- A system overflow menu exists for items that don't fit — custom overflow UI should route into it rather than being reinvented.
- **This entire section has a much deeper, code-level companion**: [vertical-toolbars.md](vertical-toolbars.md) covers the full API surface (opting in, item ordering APIs, axis-behavior overrides, badges, overflow/compression configuration, and how to disable it) sourced directly from Apple's "Raise the Bar with iPhone Duo" talk — load it before scoping any toolbar/tab-bar work for Duo.

**Limitations**
- Controls sit along one edge, so available content space is asymmetrical — safe areas must be respected so controls never cover content, not just avoided by eyeballing a mockup.
- "In general, don't override the default bar placement" — Apple explicitly discourages repositioning system bars away from their default vertical placement.
- Text-based buttons are discouraged in this layout ("keep text-based buttons to a minimum... prefer a symbol wherever one works") because labeled buttons stay locked to a horizontal bar, which doesn't fit the vertical control model. One confirmed exception: toolbar items too wide for the vertical rail (a text button, a segmented control) explicitly **stay in the navbar** rather than being forced or minimized.
- Every non-text-only toolbar item still needs both a title and a symbol (the title isn't optional just because a symbol is shown) — the title is used whenever the item moves into overflow or an expanded/accessible representation.

**Design implications**
- A toolbar/tab-bar design that assumes a fixed horizontal strip along the bottom (unmodified from a standard iPhone design) needs explicit rework for Duo's vertical control placement — this is one of the highest-likelihood "looks done on iPhone, breaks on Duo" gaps to check for.
- Order toolbar items with primary navigation (Back/Close) at the top of the vertical axis, then prominent actions (Done) — this ordering is prescriptive, not just a suggestion.
- Two named exceptions to "compress to overflow": in a navigation-focused experience, the toolbar compresses first so the tab bar/primary destinations stay visible (this is the *default*); in a task-oriented experience, the tab bar compresses first to preserve toolbar actions — scope which one applies per screen rather than applying one rule everywhere.
- A small number of layouts are explicitly allowed to break the vertical-controls model and span the full display width for an immersive, visual interface (Apple's own named example: Calculator, a "bottom-heavy single-page app") — this is a deliberate, named exception, not a loophole to over-apply. Control-sparse sheets (e.g. one with only a single close button) are a second named case where disabling the vertical bar is preferred.

---

## Sheets

**Capabilities / behavior by display**
- **Outer display**: if a sheet has a toolbar, it displays **vertically** by default.
- **Inner display**: sheets are **centered** by default (an iPad-style regular-width presentation), with horizontal items — not vertical, in either orientation.
- A "preferred placement" option lets you move a sheet to the left or right of the inner display: a sheet placed on the **left** has no vertical bar; one placed on the **right** gets one. This placement is tied to the physical hardware side, not mirrored for right-to-left languages — content adapts around the bar, but the bar itself stays on the same physical side of the device.
- On the outer display, disabling a sheet's vertical bar (for sheets with only one toolbar button) makes it stop short of the camera, and the status bar repositions accordingly.

**Limitations**
- When the device is partially folded, sheets (like several other system components: alerts, menus, toolbar buttons) **slide over to avoid resting in the fold** — a built-in "fold-avoidance" behavior. Scrollable content is exempt from this — it doesn't need to avoid the fold region.

**Design implications**
- Don't assume "sheets behave like the rest of the app's vertical-bar model" — sheets have their own distinct, display-dependent rules (vertical on outer-with-toolbar, centered-and-horizontal on inner by default) that a design spec should call out explicitly rather than inheriting the general toolbar guidance.
- Use system sheet/alert/menu/popover components wherever possible specifically to inherit fold-avoidance for free — a fully custom modal built from scratch does not get this behavior automatically.

---

## System Multitasking & Picture-in-Picture

**Capabilities**
- iPhone Duo introduces a system-level **50/50 split-view multitasking** mode: dragging an app to the side (built on the standard iPhone Home Gesture) creates two independently-usable halves of the display. **Every app participates in this — it is not opt-in.**
- Controls in split-view multitasking sit along the **outer edge** of whichever half the app occupies (e.g., an app on the left gets its controls on the left edge) — kept away from the shared center/fold region.
- Picture-in-Picture video can be **pinned to the top of the screen**; the current app resizes vertically to fit the remaining space. If the device is then partially folded, the pinned video extends to fill half the screen, and the app continues to adjust vertically in real time.
- A camera app specifically can show supplementary UI on the **outer** display (e.g. a teleprompter) while the main camera UI stays on the **inner** display, via the Camera Capture Accessory — see [camera.md](camera.md) and [xcode-development.md](xcode-development.md) for the API.

**Limitations**
- The system provides (at least) two distinct multitasking layouts — plain 50/50 split, and the video-pinned/stacked layout — but an app is expected to **handle both the same way**, via size class and scene geometry, not with bespoke per-layout logic.
- New app windows/scenes can only be created on the **inner** display — see [xcode-development.md](xcode-development.md) for the exact constraint and API.

**Design implications**
- Any design for Duo should assume split-view multitasking is always a possible context, not a special mode to design for separately — controls anchored to "the edge of the screen" need to mean "the edge of whichever half the app currently occupies," which the system handles via safe areas/reserved regions if standard components are used.
- A "watch a video while doing something else" concept should default to the system's PiP-pinning behavior rather than a custom floating-player build, since real-time vertical resizing across fold states is already handled by the platform for this specific pattern.
