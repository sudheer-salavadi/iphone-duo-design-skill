# Notifications & Widgets

## Notifications
Source: https://developer.apple.com/design/human-interface-guidelines/notifications

**Capabilities**
- Appears as Lock Screen/Home Screen/desktop banners, app icon badges, Notification Center entries, and rich communication-style UI with contact photos.
- Up to **4 customizable action buttons** per notification (with SF Symbols icons), enabling actionable notifications without opening the app.
- watchOS: Short Looks (glance on wrist raise) and Long Looks (detailed, up to 4 custom actions, customizable sash color/blur); Double Tap executes the first non-destructive action.

**Limitations**
- Explicit user permission is mandatory before sending notifications; users can further customize style/timing in Settings — app has no way to force delivery style.
- Badges must reflect only unread notification counts — using badge numbers for unrelated data (scores, weather) is explicitly disallowed by guidance.
- No sensitive/personal/confidential info should be placed in notification content (previews can be seen by anyone glancing at the device).
- Foreground notifications must be handled gracefully (e.g., subtle badge/data update) rather than showing a full alert banner while the app is open.
- Duplicate notifications for the same content are disallowed; notifications should never be used to command a specific in-app task (that's what an alert/action is for).

**Design implications**
- A notification-content mockup should assume it may render as a generic/redacted string ("Shipment," "Friend request") when previews are hidden by the user/OS — design the fallback generic text alongside the rich version.
- Don't design flows relying on custom notification real estate beyond 4 actions plus title/body/media — anything more elaborate must live in-app.
- Watch-specific glance UI (Short Look) must communicate essential info instantly since it can disappear the moment the wrist lowers — don't bury the key fact.

---

## Widgets & Home/Lock Screen Surfaces
Source: https://developer.apple.com/design/human-interface-guidelines/widgets

**Capabilities**
- Families/sizes (iPhone points): Small 170×170, Medium 364×170, Large 364×382 (Extra Large/Extra Large Portrait are iPad/Mac/Vision Pro only). Accessory widgets: Circular 76×76, Inline 257×26, Rectangular 172×76.
- Supports limited interactivity: buttons and toggles (e.g., mark-complete) directly in the widget, plus deep-linking taps that open the app at a specific location.
- Surfaces across Home Screen, Lock Screen, StandBy, CarPlay, macOS Desktop/Notification Center, Apple Watch complications/Smart Stack, and Vision Pro spatial surfaces.
- Rendering modes: full-color, accented (system tints groups), and vibrant (Lock Screen, desaturated/blurred).

**Limitations**
- **No continuous/real-time updates** — the system throttles refresh frequency based on several factors outside direct app control; widgets are inherently "glanceable but stale-tolerant," not live dashboards.
- Any non-button/toggle tap area launches the whole app — a widget cannot have arbitrary complex custom touch handling.
- Lock Screen accessory widgets render monochromatic (vibrant mode desaturates); no custom tint colors; must maintain enough gray-level contrast to remain legible on Always-On Display.
- Minimum text size 11pt; no rasterized/baked-in text.
- StandBy widgets should avoid background colors (must blend seamlessly) and render with a monochrome red tint in low-light/night mode.
- Update-highlighting animations capped at ~2 seconds (consistent with Live Activities).

**Design implications**
- A "live ticking counter" or real-time widget concept is not technically feasible as designed — reframe around periodic refresh with a visible last-updated timestamp if freshness matters to the user.
- Lock Screen/StandBy widget mockups that rely on brand colors won't render as designed — they must be validated in monochrome/vibrant rendering, not just full-color mode.
- Interactive widget concepts should be limited to 1-2 simple controls (toggle/button); anything requiring multi-step interaction belongs in the full app, reached via tap.

**Coverage gap**: dedicated Lock Screen, Always-On Display, and StandBy HIG pages 404 on the current site (folded into this page in the 2025 restructure). The StandBy/Lock Screen specifics above are all that could be confirmed — don't invent additional specifics for those surfaces.
