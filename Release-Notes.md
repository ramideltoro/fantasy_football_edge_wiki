# Release notes

## 2026-09-09 — Initial portal

Implemented the Fantasy Football Edge brand and responsive green/lime dashboard, with landing page, explicitly fictional demo, roster view, matchup view, available-player filtering, and standings. Implemented Yahoo authorization code flow, refresh tokens, encrypted session persistence, current-season league discovery, owned-team roster lookup, and normalization of Yahoo XML responses. Added production Docker deployment with a persistent volume, limited container permissions/resources, Cloudflare DNS, and Caddy HTTPS routing.

Published code to `ramideltoro/fantasy_football_edge` and documentation to `ramideltoro/fantasy_football_edge_wiki`.

## Validation

- JavaScript syntax checks passed for server and frontend.
- Eight automated tests passed: XML singleton/multiple/empty handling, zero-score preservation, optional-field normalization, public connection status, rejection of unauthenticated league reads, forged OAuth callback rejection, cross-origin disconnect rejection, and unconfigured OAuth setup/security headers.
- Dependency audit reported zero known vulnerabilities at installation time.
- Production Docker container reported `running healthy`.
- Caddy candidate configuration validated and the Let's Encrypt origin certificate was issued.
- Public root and health endpoint returned HTTP 200; public status correctly reported credentials unconfigured and browser disconnected.
- Existing backend health and Raspberry portal returned HTTP 200 after the proxy update.

## Pending live verification

Yahoo client credentials and Fantasy Sports approval were not available. No successful Yahoo sign-in, live league discovery, live roster retrieval, or token refresh against Yahoo has been claimed. These must be verified once the owner provides approved application credentials and completes OAuth consent. The tests use synthetic XML, not a captured live league fixture. There was no browser automation or screenshot QA in this release.

## 2026-09-09 — Write-access investigation

Inspected the owner's signed-in Yahoo developer account. Both existing fantasy applications expose locked read-only permissions. Yahoo's access application explicitly says write access is unavailable, although a lower note still invites exception details. Prepared an exception-request draft and documented required safeguards for a future write-enabled implementation. No existing Yahoo app was modified and no live team changes were attempted. Awaiting the owner's choice of existing versus separate OAuth application before continuing connection setup.

## 2026-09-09 — Separate Yahoo app created

Created confidential-client application `k95gYakw`, named Fantasy Football Edge, after explicit approval of the Developer Terms. Verified the app detail page, production homepage, and callback. No Fantasy Sports permissions have been granted. Credential installation and OAuth verification remain pending; desktop automatic approval review blocked Terminal UI access for secure credential transfer.
