## Why

During work hours I constantly drift into shorts, reels, and feed sites, which destroys focus. Blocking must be self-managed: the block list and the work schedule are defined in the addon, not hardcoded. [#1](https://github.com/Kerman-Sanjuan/brave-distraction-blocker/issues/1)

## What Changes

- Ship a Chromium Manifest V3 extension installable on Brave as the repo's first addon.
- Provide a user-editable block list, seeded with defaults: YouTube Shorts, Instagram Reels, TikTok, Reddit feed/forum pages, and chollometro.com.
- Provide manually configured work hours; blocking is active only inside those hours.
- Hard-block matched URLs by redirecting navigation to an extension block page.
- Treat Reddit as a special case: feed and forum/subreddit list pages are blocked, but post/comment pages remain reachable.
- Hide home/suggested feeds on otherwise-allowed sites via injected CSS while blocking is active.
- Provide an options page to edit the block list and work hours without reinstalling.

## Capabilities

### New Capabilities

- `work-hours-blocking`: Gate all blocking behavior on manually configured work hours; outside them, browsing is untouched.
- `block-page-navigation`: Match navigations against the block list and redirect them to an extension-served block page.
- `feed-hiding`: Inject CSS on allowed sites to hide home/suggested feeds and brainrot surfaces that are not full-blocked.

### Modified Capabilities

<!-- None - no existing specs. -->

## Impact

- All-new code; no existing extension files are affected. Adds: `manifest.json`, an MV3 service worker (declarativeNetRequest rules or blocking logic), block page, options page, and content scripts for Reddit feed selection and CSS feed hiding.
- No external dependencies; MV3 browser APIs only (`declarativeNetRequest`, `storage`, ` scripting`).
- Settings persist via `chrome.storage.sync` or `chrome.storage.local`.
- Scope is Brave/Chromium only; no Web Store publishing. [#1](https://github.com/Kerman-Sanjuan/brave-distraction-blocker/issues/1)

## Changelog

- Added: Brave (Chromium MV3) addon that hard-blocks brainrot sites (YouTube Shorts, Instagram Reels, TikTok, Reddit feed/forum pages, chollometro.com) during user-configured work hours.
- Added: options page to edit the block list and work hours.
- Added: Reddit post pages remain accessible while feed/forum pages are blocked.
- Added: CSS hiding of home/suggested feeds on allowed sites during work hours.
