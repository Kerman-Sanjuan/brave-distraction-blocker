## ADDED Requirements

### Requirement: Work hours gate
The addon SHALL apply blocking only during the user-configured work hours, and SHALL leave all browsing untouched outside those hours.

#### Scenario: Navigation during work hours
- **WHEN** the current time is inside the configured work hours and the user navigates to a blocked URL
- **THEN** the navigation is redirected to the block page

#### Scenario: Navigation outside work hours
- **WHEN** the current time is outside the configured work hours and the user navigates to any URL
- **THEN** the page loads normally with no blocking and no feed hiding

#### Scenario: Hours edited at runtime
- **WHEN** the user changes work hours in the options page
- **THEN** the blocking state updates immediately to match the new schedule without reinstalling the addon

#### Scenario: Schedule boundary crossing
- **WHEN** the current time crosses into or out of configured work hours while the browser is open
- **THEN** blocking is enabled or disabled accordingly without manual intervention

### Requirement: Manual work hours configuration
The user SHALL be able to configure work hours manually in the addon options, including at minimum a start time, an end time, and the days of the week on which blocking is active.

#### Scenario: User sets work hours
- **WHEN** the user enters a start time, end time, and active days in the options page and saves
- **THEN** the settings persist across browser restarts and gate blocking as configured
