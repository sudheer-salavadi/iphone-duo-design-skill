# Layout & Navigation

## Overview: Designing for iOS
Source: https://developer.apple.com/design/human-interface-guidelines/designing-for-ios

**Capabilities**
- iPhone provides Multi-Touch gestures, virtual keyboards, voice control (Siri/Voice Control), gyroscope/accelerometer data, and spatial interaction input.
- System-level surfaces apps can integrate with: Widgets, Home Screen quick actions, Spotlight, Shortcuts, Activity views.
- Devices support biometric authentication and location, which can be used (with permission) to remove friction from payments/sign-in.

**Limitations**
- Medium-size, high-resolution display only (vs. macOS/iPad's larger canvases) — design must work within a small, portrait-first viewport held one-handed.
- Viewing distance is typically 1-2 feet; users frequently multitask across apps, switching frequently.

**Design implications**
- Limit onscreen controls to primary tasks; push secondary actions to discoverable-but-hidden affordances.
- Design for reachability: controls in the middle/bottom of the screen are easier to hit one-handed than top corners — this is why edge-swipe-back and bottom-anchored primary actions are preferred patterns.
- Must gracefully adapt to orientation change, Dark Mode, and Dynamic Type — these are not optional/edge cases, they are baseline requirements.

---

## Layout
Source: https://developer.apple.com/design/human-interface-guidelines/layout

**Capabilities**
- Size classes (compact/regular, per axis) describe available space independent of device/orientation — the system tells you the actual layout regime, not just device model.
- Layout guides + safe areas exist specifically so content avoids system chrome and hardware cutouts (Dynamic Island, camera housing, home indicator) automatically.

**Limitations**
- iOS/iPadOS layout must be driven by size class, not device type or orientation — designing "for iPhone 15" vs. "for iPad" as fixed dimensions is the wrong mental model; the same view can be compact or regular depending on multitasking/Split View state. On iPhone Duo specifically, fold state is an additional driver of size class alongside device/orientation — see [iphone-duo.md](iphone-duo.md).

**Design implications**
- Never hardcode pixel layouts to a specific iPhone model; design against compact/regular breakpoints.
- Group related content with negative space/containers/separators, not just color, for scanability at a glance (relevant for one-handed, quick-glance use).
- Use progressive disclosure (nested views, disclosure triangles) rather than flat dense screens — screen real estate is the scarce resource.

---

## Navigation and Search
Source: https://developer.apple.com/design/human-interface-guidelines/navigation-and-search

**Capabilities**
- Standard navigation components: tab bars, sidebars, path controls (breadcrumbs), search fields, token fields.

**Limitations**
- This is a thin hub/index page in the current HIG structure; the substantive guidance lives on each component's dedicated page.

**Design implications**
- Prefer system-provided navigation components (tab bar, sidebar) over custom nav chrome — they inherit adaptivity (e.g., tab bar → sidebar on larger size classes) for free.

---

## Searching (Spotlight / in-app search)
Source: https://developer.apple.com/design/human-interface-guidelines/searching

**Capabilities**
- Apps can make their content indexable in systemwide Spotlight search via metadata donation; custom file types can supply Spotlight File Importer plug-ins and Quick Look generators for previews.

**Limitations**
- Only one primary/unified search surface is expected per app — scattering multiple "main" search entry points is discouraged (local, in-context filtering is fine).
- Search history display carries a privacy obligation: if shown, users must be able to clear it.

**Design implications**
- If search is core to the product, give it a first-class UI slot (dedicated tab/toolbar position), not a buried icon.
- Use scope indicators (placeholder text, scope bar, title) so users know what's being searched — critical when app content can also surface via systemwide Spotlight, where scope is ambiguous by default.

---

## Modality
Source: https://developer.apple.com/design/human-interface-guidelines/modality

**Capabilities**
- Alerts, sheets, popovers, full-screen modals, activity views/action sheets are all system-supported modal patterns with platform-appropriate dismissal conventions (top toolbar dismissal on iOS).

**Limitations**
- Only one modal should be presented at a time — stacking modals is explicitly discouraged (not a hard OS block, but a guideline violation likely to be flagged in review/critique).
- Modal tasks are expected to stay linear — no deep hierarchical navigation inside a modal.

**Design implications**
- Reserve modals for tasks that truly need focus/explicit dismissal; default to non-modal (push) navigation otherwise.
- Always provide an obvious, platform-conventional dismiss affordance, and confirm before discarding unsaved user-generated content.
