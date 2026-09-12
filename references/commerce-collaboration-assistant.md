# Commerce, Collaboration & Assistant Integration

## In-App Purchase
Source: https://developer.apple.com/design/human-interface-guidelines/in-app-purchase

**Capabilities**
- Four content types: consumable, non-consumable, auto-renewable subscription, non-renewing subscription.
- System-provided confirmation sheet, subscription management (upgrade/downgrade/cancel in-app), and a structured refund-request flow with categorized reasons and ~48-hour email confirmation.
- Offer codes (one-time or custom alphanumeric) redeemable via URL or in-app StoreKit UI.

**Limitations**
- System confirmation and refund sheets **cannot be modified or replicated** by the app.
- Total price must always be displayed for every in-app purchase type, correctly localized.
- Cancellation must always remain easy/accessible — guidance explicitly frames obstructing cancellation as a violation ("always make it easy... to avoid discouraging users"), which is also an App Review risk area.
- The store UI must be hidden entirely when purchases are restricted (e.g., parental controls) rather than shown in a disabled state.

**Design implications**
- Paywall/subscription flow mockups should treat the system confirmation and refund UI as fixed, non-designable components — don't spend design effort re-skinning them, and don't design a custom "fake" confirmation step.
- A subscription flow that hides or obstructs cancellation (e.g., requiring a support contact) is a compliance risk to flag, not just a UX nitpick.

---

## SharePlay
Source: https://developer.apple.com/design/human-interface-guidelines/shareplay

**Capabilities**
- Synchronous shared activities across devices, initiated from in-app controls, FaceTime calls, or shared links; supports Picture-in-Picture for shared video on iOS/iPadOS/macOS.
- visionOS: spatial Personas, defined spatial templates (Side-by-Side, Surround, Conversational, Custom up to 5 seats) for arranging participants relative to shared content.

**Limitations**
- SharePlay is fundamentally synchronous — asynchronous collaboration (contribute-on-own-schedule) requires a separate save/share-after-session mechanism, it isn't native to the feature.
- visionOS custom seat templates cap at 5 spatial Personas; seats must be spaced ≥1 meter apart.
- Naming/trademark constraint: "SharePlay" must be used only as a noun or verb exactly as specified — no invented forms like "SharePlayed."

**Design implications**
- A SharePlay concept for asynchronous/offline collaborative editing needs an explicit separate persistence/share mechanism layered on top — it's not something SharePlay itself provides.
- Multi-user spatial layouts on visionOS should be designed against the fixed template vocabulary (or a custom template respecting the 1-meter/5-seat constraints), not arbitrary freeform seating.

---

## Siri & App Intents
Source: https://developer.apple.com/design/human-interface-guidelines/siri

**Capabilities**
- App Intents framework exposes app actions/content ("Intents," "Entities," domain "Schemas") to Siri/Spotlight/Shortcuts — by default the system has zero awareness of what an app can do until this is implemented.

**Limitations**
- Responses must work in both audio-only and visual contexts and must be app-name-free and device-independent in phrasing — can't assume a screen is present or that Siri will "introduce" the app.
- "Siri" itself is never localized/translated; only "Hey" is locale-specific.

**Design implications**
- Any voice/Shortcuts-driven concept requires explicit App Intents integration work — it is not automatic just because the app has the relevant feature; budget this as real engineering scope tied to the design.
- Design conversational responses assuming no visual UI may be present (they must stand alone as speech), not as a screen-reading of the visual design.
