# Gestures & Input

## Gestures
Source: https://developer.apple.com/design/human-interface-guidelines/gestures

**Capabilities**
- Standard multi-touch vocabulary: tap, swipe, drag, touch-and-hold, double-tap, pinch/zoom, rotate.
- iOS/iPadOS additions: three-finger swipe (undo/redo), three-finger pinch (copy/paste), four-finger swipe (iPad app switching), shake (undo/redo).
- watchOS 11+: double-tap gesture drives scrolling, tab navigation, primary action — usable without touching the screen.

**Limitations**
- Custom gestures must not conflict with gestures that access system UI (e.g., edge-swipe reserved for system back/Control Center/Notification Center).
- A custom gesture can never be the *only* way to perform an important action — an alternative (button, menu) is mandatory for accessibility.
- Gestures requiring specific body movement/positions or complex multi-hand coordination exclude users with motor disabilities — must have alternatives.
- visionOS: the palm-up gesture zone is reserved exclusively for system overlays; apps cannot claim it.

**Design implications**
- Any swipe/gesture-based interaction concept must be checked against system-reserved gesture zones (especially screen edges) before being treated as feasible.
- Every gesture-driven action needs a discoverable, non-gesture fallback (button/menu item) — "gesture-only" is not a shippable design.

---

## Virtual Keyboards
Source: https://developer.apple.com/design/human-interface-guidelines/virtual-keyboards

**Capabilities**
- System offers many keyboard *types* matched to input semantics: email, URL, phone pad, number pad, decimal pad, web search, Twitter, etc. — the system can auto-suggest the right one.
- Return key can be customized (e.g., "Search" instead of default) to match the field's action.
- Apps can ship custom system-wide keyboard extensions (iOS/iPadOS/tvOS) that users install separately.
- Apps can present fully custom input views in place of the system keyboard within their own UI.

**Limitations**
- Custom/replacement keyboards **cannot** be used in secure text fields or phone-number fields — password entry always falls back to the system keyboard.
- Custom keyboards don't support hardware keyboard shortcuts.
- Not supported on macOS.

**Design implications**
- Never design a flow that assumes a custom keyboard will handle password/secure fields — plan for the system keyboard appearing there regardless of app-wide custom keyboard support.
- Match keyboard *type* to field semantics (numeric, email, URL) as a near-zero-cost UX win; this is a real API capability, not just a nice-to-have mockup detail.

---

## Buttons
Source: https://developer.apple.com/design/human-interface-guidelines/buttons

**Limitations**
- Minimum hit target: 44×44pt on iOS/iPadOS (60×60pt on visionOS) — this is a hard usability/accessibility floor, not a style preference.
- Only one or two "prominent" (primary-style) buttons should appear per view — visual weight, not size, should differentiate importance.

**Design implications**
- Any tap target smaller than 44×44pt in a mockup is a feasibility red flag for accessibility compliance, regardless of how it looks.
- Use button *role* (primary/cancel/destructive) semantically — destructive actions should not also be styled/positioned as the primary/default button.

---

## Text Fields
Source: https://developer.apple.com/design/human-interface-guidelines/text-fields

**Capabilities**
- Secure text fields hide input (passwords); number formatters natively support decimals/percentages/currency; system provides Clear buttons on iOS/iPadOS text fields.

**Limitations**
- Text fields are meant for small amounts of input (name, email) — large free text needs a text *view*, a different component with different affordances.
- tvOS/watchOS should minimize text entry entirely; watchOS should show a text field only when unavoidable (favor dictation/Scribble/voice).

**Design implications**
- A text-heavy form concept on watchOS is fighting the platform — redirect to voice/dictation-first input in the design instead.
