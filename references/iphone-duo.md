# iPhone Duo (Foldable) — Design Fundamentals
Source: https://developer.apple.com/design/human-interface-guidelines/designing-for-iphone-duo (new HIG page, added September 9, 2026)

iPhone Duo is Apple's foldable iPhone: dual displays (outer + inner), a front-facing camera on each display, and a central hinge that lets the device hold multiple poses. This file covers what's *specific to Duo*; for everything not called out here, standard iOS design patterns apply — see [standard-iphone-vs-duo.md](standard-iphone-vs-duo.md) for the explicit carries-over/changes breakdown.

## Anatomy: Outer + Inner Display

**Capabilities**
- Outer display: used when the device is closed — has its own front-facing camera, in a fixed corner, always visible.
- Inner display: used when the device is open — has its own front-facing camera, hidden behind the display until active.
- Both displays run the same app; the system decides which is active based on fold state.

**Limitations**
- The outer camera position is fixed and always visible — it permanently occupies screen real estate on the outer display, not just at certain times.
- The inner camera is hidden until active, but still a reserved region the layout must account for once it activates.

**Design implications**
- Treat the outer-display camera cutout as a permanent layout constraint on that display, the same way Dynamic Island is permanent on standard iPhone — never assume a clean rectangular outer-display canvas.

---

## Device Poses

**Capabilities**
- Supports book-fold (partially folded, held like a book), flat (laid open on a surface), and stand (standing on its folded edge) poses, in addition to fully closed (outer display only) and fully open (inner display, flat).

**Limitations**
- "Supporting the device's various poses doesn't mean designing a custom layout for each one" — Apple explicitly discourages a per-pose bespoke-layout strategy.

**Design implications**
- Design against size classes (compact/regular) that the system derives from the current pose/fold state, not against an enumerated list of poses — a design spec that lists "book mode layout," "flat mode layout," "stand mode layout" as separate deliverables is working against the platform's intended adaptive model. One adaptive layout, driven by size class, is the correct scope.
- For games specifically: prefer changing aspect ratio over letterboxing/pillarboxing across poses — a fixed-aspect-ratio game concept needs explicit rework for Duo, not just black bars.

---

## Reserved Regions (hard layout constraints)

**Capabilities / Limitations** — four regions structure available space and must be treated as off-limits to arbitrary content:
1. **Outer front-facing camera** — always present on the outer display; expands to accommodate Live Activities.
2. **Inner front-facing camera** — present only once active, but reserved the moment it can activate.
3. **Folding region** — space around the hinge, conditional on current fold/pose state.
4. **System components** — status bar/home indicator-equivalents, which adapt to the above automatically.

**Design implications**
- Any full-bleed or edge-to-edge visual concept must be checked against all four reserved regions, not just the outer-camera cutout — a design that "just" avoids the camera notch but ignores the folding region will still break.
- Because the folding region is conditional (changes with pose), a static Sketch/Figma mockup validated in one pose is not sufficient evidence of feasibility — the design needs to be checked across at least closed, book-fold, and fully-open poses.

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

---

## Arrangement Views

**Capabilities**
- **Split arrangement**: divides the available area horizontally (in the wider pose) or vertically (in the taller pose).
- **Overlay arrangement**: positions views on top of one another; separates them into distinct panes when the device is partially folded.

**Limitations**
- Keep navigation *outside* arrangement views — navigation containers (e.g., a navigation split view) should wrap around an arrangement view, not live inside it. This is not a separate standalone HIG page (as of this writing, `arrangement-views` 404s); all current guidance on it lives on the Designing for iPhone Duo page itself.

**Design implications**
- A design that nests a tab bar or navigation stack *inside* an arrangement view's pane is against the stated pattern — navigation chrome belongs at the outer layer, wrapping the arrangement view.
- Overlay arrangement is the right tool for "two things that are the same content at different times, one at a time" (e.g., a stacked card that separates into two panes on partial fold) rather than for permanently distinct content, which should be a split arrangement instead.

---

## Vertical Controls (Toolbars & Tab Bars)

**Capabilities**
- Toolbars, tab bars, and navigation controls move to the side (a vertical arrangement) on iPhone Duo to preserve vertical space for content — except on the inner display in portrait pose.
- Toolbar items support a **visibility priority** system: assign priority at the group level first, then individual items within a group, to control what collapses into overflow under space pressure.
- A system overflow menu exists for items that don't fit — custom overflow UI should route into it rather than being reinvented.

**Limitations**
- Controls sit along one edge, so available content space is asymmetrical — safe areas must be respected so controls never cover content, not just avoided by eyeballing a mockup.
- "In general, don't override the default bar placement" — Apple explicitly discourages repositioning system bars away from their default vertical placement.
- Text-based buttons are discouraged in this layout ("keep text-based buttons to a minimum... prefer a symbol wherever one works") because labeled buttons stay locked to a horizontal bar, which doesn't fit the vertical control model.
- Every non-text-only toolbar item still needs both a title and a symbol (the title isn't optional just because a symbol is shown).

**Design implications**
- A toolbar/tab-bar design that assumes a fixed horizontal strip along the bottom (unmodified from a standard iPhone design) needs explicit rework for Duo's vertical control placement — this is one of the highest-likelihood "looks done on iPhone, breaks on Duo" gaps to check for.
- Order toolbar items with primary navigation (Back/Close) at the top of the vertical axis, then prominent actions (Done) — this ordering is prescriptive, not just a suggestion.
- Two named exceptions to "compress to overflow": in a navigation-focused experience, keep the tab bar and move toolbar items to overflow; in a task-oriented experience, do the opposite (minimize tab bar, keep toolbar actions visible) — scope which one applies per screen rather than applying one rule everywhere.
- A small number of layouts are explicitly allowed to break the vertical-controls model and span the full display width for an immersive, visual interface (Apple's own example: Calculator) — this is a deliberate, named exception, not a loophole to over-apply.

---

## Games on Duo

**Design implications**
- "Make your game playable in every device pose" is a stated requirement, not an aspiration — a game design that only works in one pose (e.g., only flat/landscape) is incomplete as a Duo concept.
- Prefer adapting aspect ratio dynamically over adding letterboxing/pillarboxing bars across poses.
