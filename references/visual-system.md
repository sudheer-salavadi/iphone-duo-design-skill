# Visual System: Materials, Dark Mode, SF Symbols, App Icons

## Materials (Liquid Glass)
Source: https://developer.apple.com/design/human-interface-guidelines/materials

**Capabilities**
- "Liquid Glass" is the current unified system material for controls/navigation (tab bars, sidebars); it lets background content show through while keeping controls legible, and system components adopt it automatically.
- Standard content-layer materials come in 4 thicknesses (ultraThin/thin/regular/thick) with corresponding vibrancy levels for text.

**Limitations**
- Liquid Glass should **not** be used in the content layer (only the functional/control layer) — using it broadly creates visual-hierarchy confusion, per explicit guidance.
- Quaternary vibrancy should be avoided on thin/ultraThin materials due to insufficient contrast.
- On visionOS, the system "glass" material is largely fixed/unmodifiable — apps get a constrained set of thickness options (thin/regular/thick), not arbitrary custom glass effects.

**Design implications**
- A mockup that applies heavy glass/blur styling to ordinary content cards (not controls) is against current HIG guidance — reserve glass materials for chrome/controls, not arbitrary decorative content containers.
- Don't invent custom translucency systems where a system material thickness already exists — it won't automatically get contrast/vibrancy correctness the system version gets.

---

## Dark Mode
Source: https://developer.apple.com/design/human-interface-guidelines/dark-mode

**Capabilities**
- System supports Auto (time-based) switching while an app is running; iOS/iPadOS use two elevation-based background tones (base + elevated) to convey depth in dark mode.

**Limitations**
- Apps should not add a redundant in-app light/dark toggle — this contradicts the system-level setting and adds user burden ("more work for people").
- Contrast minimums: 4.5:1 general, 7:1 recommended for small text — same ballpark as the Accessibility page but stated specifically for Dark Mode combined with Increase Contrast/Reduce Transparency settings, where a naive dark-on-dark design can become unreadable.

**Design implications**
- Any design must be validated in both appearances plus Increase Contrast/Reduce Transparency on — a design that "only really works in light mode" is not shippable without rework.
- Icons should preferably use SF Symbols (auto-adapt); custom bespoke icon sets need explicit dual-appearance variants, which is added design/production cost to budget for.

---

## SF Symbols
Source: https://developer.apple.com/design/human-interface-guidelines/sf-symbols

**Capabilities**
- 9 weights matched to San Francisco font weights, 3 scales (small/medium/large) for aligning icon weight with adjacent text without manual tuning.
- 4 rendering modes: monochrome, hierarchical, palette (2+ colors), multicolor (fixed semantic colors, e.g., red trash).
- 12 built-in animation presets (Bounce, Pulse, Variable Color, Wiggle, Draw On/Off, etc.) usable with no custom animation code.

**Limitations**
- Symbol availability is OS-version-gated — a symbol used in a mockup may not exist/render on older supported OS versions.
- Custom symbols must not replicate Apple hardware products or Apple-owned feature icons, and should avoid re-implementing built-in variants (enclosures/badges) that the component library already provides.

**Design implications**
- Icon designs should default to SF Symbols (free adaptivity to weight/scale/Dark Mode/Dynamic Type) rather than custom raster icons, unless brand differentiation specifically requires it — custom icons cost more to keep accessible/adaptive.
- When proposing custom icon animation, check whether one of the 12 built-in SF Symbols animation presets already covers it before assuming custom animation work is required.

---

## App Icons
Source: https://developer.apple.com/design/human-interface-guidelines/app-icons

**Capabilities**
- Users can pick between default/dark/clear/tinted appearance variants (iOS/iPadOS/macOS) and select from app-provided alternate icons.
- Multi-layer icon composition (background + foreground layers) gets system-driven depth effects (specular highlight, parallax) for free via Icon Composer.

**Limitations**
- Fixed export sizes/shapes per platform: iOS/iPadOS/macOS 1024×1024 (system applies rounded-rect mask); tvOS 800×480 landscape; visionOS 1024×1024 circular; watchOS 1088×1088 circular.
- Must supply *unmasked*, square source layers — the system, not the designer, applies the final masking/effects; manual drop-shadows/bevels/blur are explicitly disallowed since they conflict with system-applied effects.
- Cannot replicate Apple hardware products or reproduce actual app screenshots/UI as the icon.

**Design implications**
- Icon design work should stop at "flat, layered, centered" source art — do not hand-finish it with shadows/gloss, since the system re-renders those effects itself; over-designing this will visually conflict with the OS.
- Any icon concept must be validated across 4 appearance variants (default/dark/clear/tinted), not just one "hero" rendering.
