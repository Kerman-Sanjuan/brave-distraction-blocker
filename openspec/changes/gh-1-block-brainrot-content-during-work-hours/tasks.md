## 1. Extension scaffold

- [ ] 1.1 Create `extension/manifest.json` with MV3 manifest: name, version, permissions (`declarativeNetRequest`, `storage`, `alarms`, `scripting`), service worker registration, options page registration, and block page/web accessible resource (D1, D7).
- [ ] 1.2 Create `extension/blocked.html` + `extension/blocked.css`, showing the blocked URL and a "blocked during work hours" message; read the `url` query param (D7, Requirement: Blocked page feedback).
- [ ] 1.3 Verify the unpacked extension loads in Brave (`brave://extensions` → Developer mode → Load unpacked) with no console errors.

## 2. Settings storage and options page

- [ ] 2.1 Create `extension/settings.js` defining the default settings shape (seeded block list: youtube.com/shorts, instagram.com/reels, tiktok.com, reddit.com, chollometro.com; default empty work-hours config) and read/write helpers over `chrome.storage.local` (D3, D6, Requirement: User-editable block list - seeded defaults).
- [ ] 2.2 Create `extension/options.html` + `extension/options.js`: form to edit work hours (start time, end time, active weekdays) and the block-list entries (add/remove), with entry normalization (trim, lowercase, strip scheme) at save (D6, Requirements: Manual work hours configuration, User-editable block list - Entry validation).
- [ ] 2.3 Confirm options page changes persist across browser restart via `chrome.storage.local`.

## 3. Work-hours gating

- [ ] 3.1 Create `extension/schedule.js` with a pure `isWithinWorkHours(now, config)` helper covering start/end times including overnight ranges, weekdays, and the no-config (inactive) case (D2, Requirement: Work hours gate).
- [ ] 3.2 Create `extension/service-worker.js`: on `onInstalled`, `onStartup`, `storage.onChanged`, and `alarms.onAlarm`, re-evaluate the schedule and sync blocking state; set an `alarm` before the next schedule boundary and a periodic safety re-check (D2, Scenarios: Schedule boundary crossing, Hours edited at runtime).

## 4. Hard blocking via declarativeNetRequest

- [ ] 4.1 Create `extension/rules.js`: compile block-list entries (host or host+path prefix) into DNR dynamic redirect rules pointing at `blocked.html?url=<encoded>`; include normalization and dedupe (D1, D3, Requirement: Hard block of configured sites).
- [ ] 4.2 In `extension/service-worker.js`, enable (register) the compiled DNR rules when inside work hours and disable (unregister) them outside, so out-of-hours browsing is untouched by design (D2, Scenarios: Navigation during/outside work hours).
- [ ] 4.3 Add the Reddit allowlist: a high-priority DNR `allow` rule for post/comment path shapes (`/r/<sub>/comments/*`, `/comments/*`, `/r/<sub>/s/*`) above the reddit.com block rule in `extension/rules.js` (D4, Requirement: Reddit post pages accessible → Requirement: Hard block of configured sites).
- [ ] 4.4 Verify manual test matrix in Brave: youtube.com/shorts blocked, youtube.com watch loads, chollometro.com blocked, reddit home blocked, reddit post URL loads, out-of-hours everything loads (Success criteria of #1).

## 5. Feed hiding

- [ ] 5.1 Create `extension/selectors.js`: curated CSS selector map for home/suggested feeds (e.g., YouTube home recommendations; entries for Reddit home grid are covered by blocking, keep list minimal) (D5, Requirement: Hide home and suggested feeds).
- [ ] 5.2 Create `extension/feed-hiding.js` (used from `extension/service-worker.js` via `chrome.scripting.registerContentScripts` with CSS): register the feed-hiding CSS when blocking is active, unregister when inactive (D5, Scenarios: Home feed hidden during work hours, Feed visible outside work hours).

## 6. Verification and docs

- [ ] 6.1 Add `extension/README.md` with local install instructions (Load unpacked) and a short usage guide for options (Migration Plan in design.md).
- [ ] 6.2 Run `openspec validate gh-1-block-brainrot-content-during-work-hours --strict` and exercise the block-page, schedule-boundary, and options-change scenarios end-to-end in Brave.
