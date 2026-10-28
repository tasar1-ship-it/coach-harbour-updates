# Coach Harbour Updates — handover

- Current version: service worker `coach-harbour-v1` (first publish, 28 Oct 2026)
- Live URL: https://tasar1-ship-it.github.io/coach-harbour-updates/
- Design document: https://claude.ai/artifact/CSR2g8MjFCXZCtFuXvvzyU

## File layout

- `index.html` — the whole app (CSS and ES5 JavaScript inline): hub, preview, race results, emergency, setup screens
- `sw.js` — caches the app shell; bump `CACHE` on every deploy that changes a cached file
- `manifest.json`, `icon-180.png`, `icon-512.png` — home-screen install

## Constants as they are

- JavaScript: ES5 only (`var`, `function`), no build step, no CDN
- Persistence: localStorage only, prefix `chu1_` (`chu1_settings`, `chu1_contacts`, `chu1_log`); no Firebase
- Sending: Web Share API or `wa.me/?text=` for WhatsApp, `mailto:` with Bcc, `sms:` (iOS uses `sms:/open?addresses=`), `tel:`
- Contacts: Contact Picker API where available (Android Chrome), else `.vcf` file import

## Last change

First version: four screens, ES + EN presets, 2-second emergency hold, acknowledgement tick-list, 7-day send log.

## Known untested

On a real phone: share sheet into WhatsApp, multi-recipient SMS on iPhone and Android, Mail with Bcc, Contact Picker.
