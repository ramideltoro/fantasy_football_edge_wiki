# Fantasy Football Edge documentation

**Portal:** https://fantasy.ramideltoro.com  
**Code:** https://github.com/ramideltoro/fantasy_football_edge

Fantasy Football Edge brings a Yahoo league's roster, matchups, standings, and available players into a responsive football dashboard.

- [Yahoo setup and account connection](Yahoo-Setup.md)
- [Features and user guide](User-Guide.md)
- [Architecture and security](Architecture.md)
- [Deployment, operations, and recovery](Operations.md)
- [Validation and release history](Release-Notes.md)

## Current status

The first release is deployed to the backend VPS. Yahoo credentials are configured and OAuth sign-in succeeded. Live league discovery is blocked by Yahoo’s `additional_authorization_required` response; Fantasy API approval is still required. The public demo uses fictional players and scores and is labeled throughout.

## Write-access request

See [write access and automation](Write-Access-and-Automation.md) for the signed-in account findings, Yahoo's current write-access restriction, and a prepared exception-request draft. No write permission or team-changing automation is enabled.
