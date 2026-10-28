# Coach Harbour Updates — handover

- Current version: service worker `coach-harbour-v8` (28 Oct 2026)
- Live URL: https://tasar1-ship-it.github.io/coach-harbour-updates/
- Design document: https://claude.ai/artifact/CSR2g8MjFCXZCtFuXvvzyU

## File layout

- `index.html` — the whole app (CSS and ES5 JavaScript inline): hub, preview, change of plan, prize-giving, race results, setup, example screens
- `sw.js` — caches the app shell; the page itself is network-first (cached copy only when offline), icons and manifest cache-first; still bump `CACHE` on every deploy
- `manifest.json`, `icon-180.png`, `icon-512.png` — home-screen install

## Constants as they are

- JavaScript: ES5 only (`var`, `function`), no build step, no CDN
- Persistence: localStorage only, prefix `chu1_` (`chu1_settings`, `chu1_contacts`, `chu1_log`); new settings keys are merged from `DEFAULTS` on load, so no prefix bump was needed in v2; no Firebase
- UI labels: Spanish is the HTML text; English labels live in `UI.en` and are swapped in by `applyUi()`
- Sending: Web Share API or `wa.me/?text=` for WhatsApp, `mailto:` with Bcc, `sms:` (iOS uses `sms:/open?addresses=`); no `tel:` since v4
- Contacts: Contact Picker API where available (Android Chrome), else `.vcf` file import

## Last change

v8 (28 Oct 2026): Results button pink (--pink #ff5fa8) instead of teal.

v7 (28 Oct 2026): service worker serves the page network-first, falling back to the cached copy offline. Before, it was cache-first, so a new version only showed on the second open (Ronald saw no colours after v6). Phones that still run the old worker may need one more reload, or removing and re-adding the home-screen icon once.

v6 (28 Oct 2026): hub Prize-giving button purple (--purple #b07cff), Results teal (--teal #22c3c3), so they no longer look like Ready now (green).

v5 (28 Oct 2026): fleet buttons always show their colour (yellow, blue, red border and tint); the chosen fleet is filled solid.

v4 (28 Oct 2026): emergency feature removed, because emergencies are handled individually by the coaches. Gone: the hold-to-open EMERGENCY button, the emergency screen (112/VHF step, SMS to all, call list, acknowledgement tick-list, all-safe message), the emergency and all-safe message texts, and the Club and Emergency contact tags (tags are now Parent or Off; contacts saved with an old tag load as Off). `settings.acks` may still sit in old saved settings; nothing reads it.

v3 (28 Oct 2026): English/Español pill top left (same idea as the sailing sign-up page; switches the screens only, and with Spanish-only messages the preview shows an "In English:" line that is not sent). Training/Regatta switch on the hub replaces the Setup checkbox; on training days Prize-giving, Results and "Racing abandoned" are hidden. New Example screen (hub "Example", Setup "See an example"): a training day and a regatta day at fixed clock times, built from the real presets, plus "Try it with sample data". English messages turn a leading "el/la" in the pickup place into "the".

v2 (28 Oct 2026), from reading the squad's parent-group chat export: hub rebuilt around the coach's real sequence (on the water, last race, heading in, ready to collect in 30/45/60, ready now), colour-fleet prefix on regatta days, pickup place per day, prize-giving screen, results link kept per day, change-of-plan screen (early finish, abandoned, late, cancelled), Spanish UI and Spanish-only messages by default, squad name only on SMS/email.

v1: four screens, ES + EN presets, 2-second emergency hold, acknowledgement tick-list, 7-day send log.

## Known untested

On a real phone: share sheet into WhatsApp, multi-recipient SMS on iPhone and Android, Mail with Bcc, Contact Picker.
