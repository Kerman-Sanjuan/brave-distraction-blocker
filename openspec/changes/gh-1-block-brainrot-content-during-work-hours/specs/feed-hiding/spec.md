## ADDED Requirements

### Requirement: Hide home and suggested feeds
While blocking is active, the addon SHALL hide home/suggested feed surfaces on otherwise-allowed sites via injected CSS, and SHALL remove that CSS when blocking is inactive.

#### Scenario: Home feed hidden during work hours
- **WHEN** work hours are active and the user opens an allowed site that has a home/suggested feed in the curated selector list
- **THEN** the feed surface is not visible on the page

#### Scenario: Feed visible outside work hours
- **WHEN** work hours are not active and the user opens the same site
- **THEN** the feed renders normally

#### Scenario: Blocking ends while a page is open
- **WHEN** work hours end while a page with a hidden feed is already open and the user reloads it
- **THEN** the feed renders normally on reload
