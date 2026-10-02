# Issue #1: Brave addon to block brainrot content during work hours

https://github.com/Kerman-Sanjuan/brave-distraction-blocker/issues/1

## User story

As a worker using Brave, I want a browser addon that hard-blocks brainrot content (YouTube Shorts, Instagram Reels, TikTok, Reddit forums, chollometro.com) during my manually configured work hours, so that I don't procrastinate while working.

## Context / problem

This repo is brand new — this is the first feature and first build of the addon. During work hours I constantly drift into shorts/reels/feeds, which destroys focus. Blocking needs to be self-managed: the block list and the work schedule are defined by me in the addon, not hardcoded. Reddit is a special case: forum/feed browsing is procrastination, but individual post pages are legitimate when looking up information, so posts must stay reachable.

## Requirements

- The addon MUST be a Chromium extension (Manifest V3) installable on Brave.
- The addon SHALL ship with a default block list: YouTube Shorts (`youtube.com/shorts`), Instagram Reels, TikTok, Reddit feed/forum pages, and chollometro.com.
- The user SHALL be able to add and remove sites from the block list in the addon options (no code edits required).
- The user SHALL be able to configure work hours manually in the addon; blocking is active only inside those hours.
- Blocked URLs MUST be hard-blocked: navigation shows a blocked page instead of the content.
- Reddit post/comment pages SHALL remain accessible during work hours; only feed and forum/subreddit list pages are blocked.
- Home/suggested feeds on otherwise-allowed sites MUST be hidden via CSS while blocking is active.

## Success criteria

- During configured work hours: navigating to YouTube Shorts, Instagram Reels, TikTok, or chollometro.com shows the block page.
- During configured work hours: Reddit feed/forum pages are blocked but individual post URLs (e.g. opened from a search) load normally.
- Home/suggested feeds are hidden on allowed sites while blocking is active.
- Out of configured work hours, all sites behave normally with no blocking or hiding.
- The user can change the block list and work hours from the addon options and the change takes effect without reinstalling.

## Out of scope

- Chrome Web Store publishing.
- Per-site schedules.
- Usage statistics / analytics.
- Sync of settings across devices.
- Pause/snooze button.
- Cross-browser support (Firefox, Safari) — Brave/Chromium only.
