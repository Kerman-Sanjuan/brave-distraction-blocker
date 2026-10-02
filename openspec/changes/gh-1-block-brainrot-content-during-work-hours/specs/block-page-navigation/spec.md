## ADDED Requirements

### Requirement: Hard block of configured sites
The addon SHALL hard-block navigation to block-listed URLs during work hours by redirecting the navigation to an extension-served block page, and the original page MUST NOT load.

#### Scenario: Navigate to a full-site block entry
- **WHEN** work hours are active and the user navigates to chollometro.com
- **THEN** the browser shows the block page instead of the site content

#### Scenario: Navigate to a path-scoped block entry
- **WHEN** work hours are active and the user navigates to youtube.com/shorts/<id>
- **THEN** the browser shows the block page instead of the short

#### Scenario: Non-blocked path on a partially blocked site
- **WHEN** work hours are active and the user navigates to a youtube.com watch URL
- **THEN** the page loads normally

### Requirement: User-editable block list
The addon SHALL ship with a default block list containing YouTube Shorts (`youtube.com/shorts`), Instagram Reels, TikTok, Reddit feed/forum pages, and chollometro.com, and the user SHALL be able to add and remove entries from the options page without editing code.

#### Scenario: Seeded defaults on first install
- **WHEN** the addon is installed for the first time
- **THEN** the default block list is active without any user configuration

#### Scenario: User adds a site
- **WHEN** the user adds a new site to the block list in the options page and saves
- **THEN** navigations to that site are blocked during work hours immediately, without reinstalling the addon

#### Scenario: User removes a site
- **WHEN** the user removes a site from the block list in the options page and saves
- **THEN** navigations to that site load normally during work hours immediately

#### Scenario: Entry validation
- **WHEN** the user saves a block-list entry with stray whitespace, mixed case, or a URL scheme prefix
- **THEN** the entry is normalized before saving so the rule still matches the intended site

### Requirement: Blocked page feedback
The block page SHALL state that the site is blocked during work hours and show which URL was blocked.

#### Scenario: Block page displays target
- **WHEN** a navigation is redirected to the block page
- **THEN** the page identifies the blocked URL and indicates that blocking is due to work hours
