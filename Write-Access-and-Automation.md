# Write access and automation

## Verified Yahoo limitation — September 9, 2026

The owner requested write access so portal actions and automation can update the Yahoo team.

The signed-in Yahoo Developer Network account has two existing applications, `fantasy-2` (confidential client) and `fantasy-football2` (public client). Both display **Fantasy Sports - Read**, with permissions disabled for editing. Neither exposes a write permission option. The new-application form does not offer a Fantasy Sports permission checkbox.

The [Yahoo Fantasy API access application](https://sports.yahoo.com/developer/access/) explicitly states: “Write access is not available at this time.” Lower on the same form, older/general guidance still invites use cases requiring read/write access in the notes. Treat the explicit availability notice and locked account permissions as the operative restriction. An exception request is possible, but approval must not be assumed.

No write key has been created, no existing application was modified, and no team-changing automation was activated. Read-only access must still be authorized and verified against the owner's league.

## Prepared exception-request notes

> Fantasy Football Edge is a personal portal at https://fantasy.ramideltoro.com for managing my own Yahoo Fantasy Football team. In addition to authenticated league, roster, scoreboard, standings, and available-player reads, I would like permission to submit lineup position changes from the portal and, if permitted, run owner-configured lineup maintenance automations. Any write would be restricted to the authenticated owner's team and Yahoo's legal roster and lock rules. The portal would show proposed changes, retain an action audit trail, and provide a way to disable automation. Please confirm whether read/write access can be granted as an exception, which write endpoints and scopes are supported, and whether unattended lineup changes are permitted. If write access is unavailable, please confirm that limitation; we will keep Yahoo transactions disabled and use read-only analysis.

This is a draft, not a submission. The safeguards described are proposed requirements for a future write-enabled release, not features already implemented. No claim is made that the app currently has an action audit trail or lineup automation engine.

## Implementation boundary

Before enabling writes:

1. Obtain Yahoo's actual write permission and confirm the API endpoints and any automation restrictions.
2. Reauthorize the owner's Yahoo account for the newly granted permissions.
3. Implement ownership verification, Origin/CSRF protection, legal slot validation, fresh roster/lock checks, proposed-change review, and a durable audit trail.
4. Verify a specifically approved live change and reconciliation with Yahoo before permitting unattended changes.
5. Define the automation's exact scope, schedule, decision rules, and override behavior with the owner.

Do not emulate write access by changing a scope string, sending unsupported requests, or automating Yahoo's UI behind the user's back.

## Read-only automation candidates

Automatic refresh, injury/bye-week checks, and proposed lineup recommendations can be built without write access. They are not yet implemented as scheduled jobs. No autonomous drops, adds, trades, waiver bids, or lineup substitutions have been authorized by a concrete rule set.
