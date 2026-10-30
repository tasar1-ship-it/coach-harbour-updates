# Coach Harbour Updates: handover

The parent communication app for Optimist sailing coaches. Updated 30 October 2026 so that a new session can pick up from this file alone.

This repo is public. Never add children's names, parents' numbers or the sign-up page's group key here.

## Where it stands

- **Version:** 8, service worker `coach-harbour-v8`, deployed 28 October 2026 and confirmed live.
- **Status:** working prototype. No coach has used it yet, and it has not been tried on a real phone.
- **Next step:** one coach tests it on a training day, on iPhone and Android, with a test WhatsApp group. Then fix what that finds.
- **Blocking the rollout:** the coach has not yet seen the app and has not agreed to use it. The draft message to parents promises better information about pick-up times, so check with the coach before that message goes out.

## Links

- Live app: https://tasar1-ship-it.github.io/coach-harbour-updates/
- Code: https://github.com/tasar1-ship-it/coach-harbour-updates (branch `main`, GitHub Pages)
- Design document (Claude Doc, private): https://claude.ai/artifact/CSR2g8MjFCXZCtFuXvvzyU
- Design document PDF: `~/Downloads/Coach Harbour Updates - Design Document_v3.pdf` (use v3; v2 describes features the app does not have, and v1 also describes the emergency screen)
- Overview card: "Lukas' coaches" section of https://claude.ai/artifact/CMsziPsrVbyovn81KqmZNP

## What it does

A coach on the boat taps a big button. The app writes a short message with the clock time and pickup place, shows a preview, and opens WhatsApp, Mail or Messages. The coach picks the group and taps send.

The main screen has an English/Español button, Example and Setup links, and a Training/Regatta switch, followed by these buttons:

| Button | Colour | Shown on | Message (Spanish) |
|---|---|---|---|
| On the water | blue | both | ¡Al agua! |
| Last race | blue | both | Empezamos la última prueba. |
| Ready to collect in 30 / 45 / 60 | blue | both | Vamos a puerto. Estarán listos sobre las {time} en {place}. |
| Heading in | neutral | both | Hemos acabado, vamos a puerto. |
| Ready now | green | both | Ya están listos en {place}. |
| Prize-giving | purple | regatta | Entrega de premios a las {time} en {place}. |
| Results | pink | regatta | ¡Resultados! plus a link or pasted standings |
| Change of plan | amber | both | Early finish, running late, training cancelled; racing abandoned on regatta days only |

- On regatta days the coach picks a fleet (Yellow, Blue, Red, or All). Messages then start with "Grupo amarillo:" and so on.
- Times round up to the next 5 minutes.
- Contacts are imported once, then tagged Parent or Off. Setup also holds the squad name, pickup place, languages, light theme, the WhatsApp pin message and a 7-day send log.
- The Example screen shows a training day and a regatta day at fixed times, built from the real message texts.

## Decisions already made

Do not reopen these unless Ronald does.

- **No automatic posting.** No app can post into a WhatsApp group created on a personal number. Meta's Cloud API groups only cover Official Business Accounts, with a reported limit of 8 participants. So the send is three taps: button, group, send.
- **Emergencies are not in the app.** Ronald decided this on 28 October 2026: the coaches handle them individually by phone. The emergency screen, its messages and the Club and Emergency contact tags were removed in v4. Do not add them back.
- **Attendance is not in the app.** The separate weekly sign-up page handles it (see below). Parents' sick and late notices also go there.
- **Ready-to-collect time, not harbour arrival.** The chat export showed that this is what parents need.
- **Spanish by default,** in the coach's own short style. The English button changes the screens only. With Spanish-only messages, the preview shows an "In English:" line that is not sent.
- **Training is a full use case,** not an afterthought to regattas. Regatta-only buttons are hidden on training days.
- **No server, no account.** Everything stays on the coach's phone.

## Related project: the weekly sign-up page

It serves the same group (Club Nàutic Arenal, parents' group "Optimist Desarrollo 1"), in a separate repo: `~/Developer/sailing-signup`, with its own handover file. It holds attendance, sick and late marks, and the coaches' dashboard. The two apps share no code or data. Its group link contains a secret key, so never copy that link into this repo.

## Files

- `index.html`: the whole app, with inline CSS and JavaScript. Screens: `hub`, `preview`, `plan`, `prize`, `results`, `setup`, `example`.
- `sw.js`: service worker. The page is network-first with the cached copy as offline fallback; icons and manifest are cache-first.
- `manifest.json`, `icon-180.png`, `icon-512.png`: home-screen install.
- `.claude/launch.json`: git-ignored and does not work (see Testing).

## Code conventions

- ES5 only (`var`, `function`), no build step, no CDN, no framework.
- localStorage only, prefix `chu1_`: `chu1_settings`, `chu1_contacts`, `chu1_log`. New settings keys go in `DEFAULTS` and merge on load, so they need no prefix bump. Old saved settings may still hold `acks`; nothing reads it.
- Message texts are in `TEXT.es` and `TEXT.en`. `build(kind, opts)` assembles them, adding the fleet prefix and place.
- Screen labels: Spanish is the HTML text, with `data-t` keys. English is in `UI.en`, swapped in by `applyUi()`.
- Sending: Web Share API or `wa.me/?text=` for WhatsApp, `mailto:` with Bcc, `sms:` (iOS uses `sms:/open?addresses=`). No `tel:` links since v4.
- Contacts: Contact Picker API where available (Android Chrome), otherwise `.vcf` file import.

## Deploy

1. `git pull`, then edit and test.
2. Bump `CACHE` in `sw.js` whenever `index.html`, the manifest or an icon changes.
3. Add a line to the history below, in the same commit.
4. Push to `main` under the `tasar1-ship-it` account. GitHub Pages can take up to 10 minutes.
5. Confirm with `curl -s https://tasar1-ship-it.github.io/coach-harbour-updates/sw.js | head -1`.

Phones still running a worker older than v7 may need one more reload, or removing and re-adding the home-screen icon once.

## Testing

- `preview_start` picks up `~/.claude/launch.json` (the data room server) rather than this project's config.
- The Browser pane has refused `file://` pages and some local ports. `python3 -m http.server 8793 --bind 127.0.0.1` from this folder worked on 28 October, but the same port was refused a day later.
- Service worker registration fails in the Browser pane on localhost. This is a pane limit, not an app bug.
- The most reliable check is headless Chrome: add a small script to a temporary copy that clicks buttons and writes results to the page, run it with `--headless=new --virtual-time-budget=3000 --dump-dom`, then remove the copy.

## Not yet tested

Tested so far in a browser at phone size only: message texts, results cleaning, `.vcf` parsing, the Training/Regatta switch, the Example screen.

Not tested on a real phone:

- share sheet into WhatsApp, and which route works (`navigator.share` or `wa.me`)
- multi-recipient SMS on iPhone and Android
- Mail with Bcc
- Contact Picker and `.vcf` import
- network-first updates and the offline fallback in the service worker

## Features the app does not have

The design document was corrected on 30 October 2026 to describe what the app really does. These were in earlier versions of the document and were never built. Build one only if Ronald asks:

- editing a message in the app before sending (the coach can still change the words in WhatsApp, Mail or Messages)
- a "Not confirmed" mark and reminder for unsent messages (the send log only records whether the phone was online)
- an app PIN, a language per contact, and special spoken labels for screen readers
- a sail day that resets at midnight (only the results link is per-day)

When the app changes, update the design document too, then rebuild the PDF. The PDF is printed with headless Chrome from a separate print layout, because the document's own PDF export splits tables and drawings across pages. That layout lived in the session scratch folder and is not in this repo.

## Open questions

- Is a 3-tap WhatsApp send acceptable to the coach, or is a club WhatsApp Business number worth setting up?
- Does the club's sign-up form already cover coach messaging, or does it need the one-line consent note in the design document?
- Which languages should be preloaded?

## Privacy

- The app stores parents' names, numbers and emails on the coach's phone only, and nothing about children.
- Routine messages speak about the group, never one child.
- The squad's WhatsApp export held children's full names and an identity number. It was read for design only, and on 28 October 2026 was moved to `~/Downloads/_Admin/_To delete` for Ronald to empty. Never copy from it into shareable output.
- `loadSample()` uses fake numbers (`+3460000000x`) and `example.com` addresses.

## History

All on 28 October 2026.

- **v8:** Results button pink (`--pink #ff5fa8`).
- **v7:** page network-first in the service worker. Before, a new version only showed on the second open.
- **v6:** Prize-giving purple, Results teal.
- **v5:** fleet buttons always show their colour; the chosen fleet is filled solid.
- **v4:** emergency feature removed. Contact tags are now Parent or Off; contacts saved with an old tag load as Off.
- **v3:** English/Español button, Training/Regatta switch on the main screen, Example screen.
- **v2:** rebuilt from the parents' chat export: ready-to-collect times, fleets, pickup place, prize-giving, results link, change of plan, Spanish default.
- **v1:** first prototype.
