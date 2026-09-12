# Analytics setup: One Love, Two IDs

## Current setup (active)
GA4 is wired **directly** with Measurement ID `G-NMEYZ7ZWB5` (gtag.js): no Tag Manager needed. All `olti_*` events below are sent as GA4 events with their parameters. Only step 4 (register custom dimensions + key events in GA4) is still required.
If you later want GTM, set the `gtmId` tweak: it takes over and GA4 is then configured inside GTM (steps 1-3).

---

## 1. Create the containers
1. analytics.google.com → Admin → Create property "Y+ Interactive" (or reuse the yplus.ps property) → Web data stream → copy the **Measurement ID** (`G-XXXXXXX`).
2. tagmanager.google.com → Create container "Y+ Interactive" (Web) → copy the **Container ID** (`GTM-XXXXXXX`).

## 2. Put the GTM ID in the page
Either set the `gtmId` tweak (Tweaks panel) or edit `index.html`: search `"gtmId"` in `data-props` and set `"default":"GTM-XXXXXXX"`. Push to GitHub.
Temporary test without editing: open `…/index.html?gtm=GTM-XXXXXXX`.

## 3. In GTM (once)
- Tag: **Google Tag** → Tag ID = `G-XXXXXXX` → trigger *Initialization - All Pages*.
- Variables (Data Layer Variable, version 2): `language`, `mode`, `selected_id`, `chapter`, `choice`, `kind`, `choices`.
- Trigger: Custom Event, event name regex `^olti_.*`, tick *Use regex matching*.
- Tag: **GA4 Event** → Event Name = `{{Event}}` (built-in variable) → Event Parameters: language, mode, selected_id, chapter, choice, kind → trigger = the custom-event trigger above.
- Submit → Publish.

## 4. In GA4
Admin → Custom definitions → register `language`, `mode`, `selected_id`, `chapter`, `choice`, `kind` as event-scoped dimensions.
Mark `olti_story_complete` and `olti_action_started` as **Key events**.

## Events the page sends
| Event | Extra params | Meaning |
| --- | --- | --- |
| olti_load |: | page opened |
| olti_language_change | language | switched language |
| olti_how_open |: | opened "How does this work" |
| olti_story_start | selected_id | picked blue / green |
| olti_choice | chapter, choice | made a decision |
| olti_story_complete | selected_id, choices | reached the ending |
| olti_badge_earned |: | first completion on device |
| olti_action_started | kind (email/share) | pressed an action |
| olti_email_action / olti_share_native / olti_share_copy |: | which action |
| olti_reset |: | back to start (also kiosk idle) |

Every event also carries `language`, `mode` (online/kiosk), `selected_id`, `chapter`.

## Campaign links
- Instagram bio: `https://sarabta.github.io/one-love-two-ids/?utm_source=instagram&utm_medium=bio&utm_campaign=one-love-two-ids`
- Story/reel: `…&utm_medium=story`
- QR at an event: `…?utm_source=qr&utm_medium=print&utm_campaign=<city-event>`
- Kiosk: the page sends `mode=kiosk`, so filter that out of online reach numbers.

## Privacy
No personal data is collected by the page itself. In GA4: Admin → Data settings → enable *Google signals* **off**, IP anonymisation is default. For EU visitors (Italian/French students) enable **Consent Mode** basic in GTM or use the GA4 cookieless setting if you want to avoid a consent banner.
