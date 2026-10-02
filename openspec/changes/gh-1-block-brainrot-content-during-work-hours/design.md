## Context

Brand-new repository; this change delivers the first build of the addon. Target is a Chromium Manifest V3 extension loaded unpacked into Brave. No existing code, specs, or pipelines constrain the design. The issue's success criteria map 1:1 onto the three capabilities in the proposal. [#1](https://github.com/Kerman-Sanjuan/brave-distraction-blocker/issues/1)

## Goals / Non-Goals

**Goals:**
- Manifest V3 extension installable on Brave via unpacked load.
- Hard-blocking of configured brainrot sites during manually configured work hours.
- User-editable block list and schedule from an options page, effective without reinstall.
- Reddit special case: block feeds/forums, allow post/comment pages.
- CSS hiding of home/suggested feeds on otherwise-allowed sites.

**Non-Goals:**
- Chrome Web Store publishing.
- Per-site schedules, usage stats, cross-device sync, pause/snooze.
- Cross-browser (Firefox/Safari) support.
- Blocking outside work hours.

## Decisions

### D1: Blocking mechanism — `declarativeNetRequest` dynamic rules vs. `webRequest` blocking

**Decision:** Use `declarativeNetRequest` (DNR) with dynamic rules that redirect matched URLs to the extension's block page.

**Rationale:** MV3 removed blocking `webRequest` for Chromium; DNR is the supported path and Brave supports it. Redirects are enforced at the network layer, defeating trivial DOM workarounds.

**Alternatives rejected:**
- `chrome.webRequest` blocking listener — not available in MV3 on Chromium/Brave.
- Content-script `window.stop()` — race-prone; content flashes before script runs.

### D2: Work-hours gating — rule updates vs. per-request time check

**Decision:** The MV3 service worker computes "is inside work hours" from stored settings, and enables/disables (registers/unregisters) the DNR redirect rule set accordingly, re-evaluating on storage changes and via alarms near schedule boundaries.

**Rationale:** No per-request time evaluation; DNR rules only exist inside work hours, so out-of-hours browsing is untouched by design. `chrome.alarms` wakes the worker for boundary transitions (MV3 workers are ephemeral).

**Alternatives rejected:**
- Checking time inside a `declarativeNetRequest` rule condition — DNR has no time conditions.
- Keeping rules always registered and deciding in the redirect target — leaky and complex.

### D3: Matching granularity — URL-pattern rules per user entry

**Decision:** Each block-list entry is a simple string (host or host+path prefix, e.g. `youtube.com/shorts`, `chollometro.com`). The options page compiles entries to DNR regexFilter/urlFilter rules; full-host entries block the whole site and host+path entries block only that subtree.

**Rationale:** The user said "the lock list can be defined on the addon" — plain strings without regex syntax keep it usable and safe.

**Alternatives rejected:**
- User-authored regex per entry — powerful but error-prone for a personal tool.

### D4: Reddit special case — allowlist for post paths

**Decision:** Implement Reddit as a seeded block-list entry for `reddit.com` plus a built-in allowlist of post/comment path shapes (e.g. `/r/<sub>/comments/...`, `/comments/...`, `/r/<sub>/s/...` share links) layered as an `allow` priority DNR rule above the block rule.

**Rationale:** DNR precedence lets `allow` beat `redirect` so search-result post links load normally while feed/forum pages redirect. The default Reddit seed keeps this nuance invisible to the user; removing the Reddit entry disables the special case.

**Alternatives rejected:**
- Content-script DOM detection of "feed vs. post" — fragile against Reddit's SPA routing and redesigns.

### D5: Feed hiding — `chrome.scripting` CSS injection

**Decision:** While blocking is active, the service worker registers a small set of `chrome.scripting.registerContentScripts`-style CSS (via `scripting.registerContentScripts` with css) targeting known home/suggested feed surfaces (YouTube home recommendations, etc.). Site CSS selectors ship as static, curated data in the addon.

**Rationale:** Simple, fast, no layout-thrash scripts; CSS injection is the standard MV3 approach.

**Alternatives rejected:**
- Per-site JS DOM removal — heavier and more brittle than CSS `display:none`.

### D6: Settings storage — `chrome.storage.local` + a `storage` area change listener

**Decision:** Store block list and work-hours config in `chrome.storage.local`; the service worker listens to `chrome.storage.onChanged` to recompile DNR rules and re-evaluate schedule immediately.

**Rationale:** Works out of the box for a single-machine personal addon (sync is out of scope); listener gives immediate effect on options change.

**Alternatives rejected:**
- `chrome.storage.sync` — cross-device sync explicitly out of scope for v1. [#1](https://github.com/Kerman-Sanjuan/brave-distraction-blocker/issues/1)

### D7: Block page — extension page with target URL shown

**Decision:** DNR redirect points to `chrome-extension://<id>/blocked.html?url=<encoded target>`, a static extension page stating the site is blocked during work hours.

**Rationale:** Static page is cheap, needs no service-worker round trip, and shows enough context to be understandable.

**Alternatives rejected:**
- `javascript:` URL in DNR redirect — not permitted.
- A full-screen overlay injected as content script — bypassable and duplicates DNR work.

## Risks / Trade-offs

- [Sites redesign selectors; feed hiding breaks silently] → Keep selectors in one curated data file documented for quick patching; feed hiding is best-effort, blocking is the hard guarantee.
- [DNR rule limits (default ~30k dynamic rules) are far above user needs] → Accept; no mitigation needed for a personal list.
- [MV3 service worker suspends and misses a schedule boundary] → Use `chrome.alarms` (minimum period handled) plus evaluate on `onStartup`, `onInstalled`, and `storage.onChanged` so the correct state converges even after a missed alarm.
- [Reddit post URL shapes change] → Allowlist lives in one curated file next to selectors, easy to update; worst case posts are blocked and the user removes the Reddit entry.
- [User typos a block-list entry] → Options page validates/normalizes entries (trim, lowercase, strip scheme) before saving and shows the parsed preview.

## Migration Plan

First release; no data or users to migrate. Install: clone repo, `brave://extensions` → Developer mode → Load unpacked → select the extension directory. Rollback: remove the extension.

## Open Questions

- None blocking; defaults for seed list and schedule can be tuned in the options page after install.
