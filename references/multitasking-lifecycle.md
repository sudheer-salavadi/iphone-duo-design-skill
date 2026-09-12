# Multitasking & App Lifecycle

## Multitasking
Source: https://developer.apple.com/design/human-interface-guidelines/multitasking

**Capabilities**
- iOS: Picture-in-Picture for video/FaceTime; app switcher.
- App can complete user-initiated background work (downloads, video processing) after the user backgrounds it.

**Limitations**
- "Every app needs to work well with multitasking" — apps must always be prepared to save/restore state because the system can suspend/background them at any time, unpredictably.
- Audio: primary interruptions (music/podcasts) pause indefinitely; secondary interruptions (notifications, turn-by-turn GPS) should duck volume then resume — apps don't get to dictate arbitrary custom interruption behavior.
- Notifications should be used sparingly for background task completion — not for routine background work.
- watchOS: multitasking (in this sense) is not supported.

**Design implications**
- Any design that assumes an app "stays alive" and mid-task state persists indefinitely in the background is not technically grounded — always design an explicit state-restore path.
- Don't design a notification-heavy strategy for background completion events; reserve notifications for genuinely important/time-sensitive completions.

---

## App Clips
Source: https://developer.apple.com/design/human-interface-guidelines/app-clips

**Capabilities**
- Lightweight, no-install app experience triggered via App Clip Codes (preferred), NFC tags, QR codes, location-based Siri Suggestions, Maps, Smart App Banners, Safari App Clip cards, Messages links, or links from other apps (iOS 17+).
- Supports Apple Pay express checkout and Sign in with Apple for low-friction identity/payment.

**Limitations**
- App Clips cannot perform background operations.
- No home screen presence; system can auto-delete stored data/the clip itself between launches — cannot assume persistence.
- No tab bars/complex navigation/settings — UI must be linear and single-purpose.
- Extended notification permission is capped at 8 hours after an App Clip launch, and must only be requested if functionally necessary; no purely promotional notifications.
- Card artwork requirements: 1800×1200px image, title ≤30 characters, subtitle ≤56 characters.
- "Ensure your App Clip is small" — smaller binary size directly speeds up launch (explicit performance/size constraint).
- No web views permitted — native components/frameworks only.

**Design implications**
- Scope an App Clip concept to a single linear task (unlock a bike, order coffee) — anything requiring settings, tab navigation, or persistent background state should be a full app instead.
- Because storage/background execution aren't guaranteed, don't design App Clip flows around "remembering" prior sessions.

---

## Launching / Launch Screens
Source: https://developer.apple.com/design/human-interface-guidelines/launching

**Capabilities**
- System auto-displays a launch screen (iOS/iPadOS/tvOS only) then transitions to the real first screen. macOS/visionOS/watchOS don't use launch screens at all.

**Limitations**
- Launch screen must look nearly identical to the actual first screen (static, not animated/branded) — it cannot contain text (localization breaks) or act as a branding/ad placement.
- "Launch instantly" — users expect interaction within a couple of seconds; this is a stated perf expectation, not just a nice-to-have.

**Design implications**
- Any splash-screen-as-branding-moment concept (logo animation, marketing message) is against guidance and will likely be flagged in review; design the launch screen as a boring placeholder matching screen 1's static chrome.
- Must design (or at least account for) state restoration so returning users resume where they left off rather than re-launching cold every time.
