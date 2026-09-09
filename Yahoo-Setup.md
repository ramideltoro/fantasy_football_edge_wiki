# Yahoo setup

## Required account action

Yahoo controls access to Fantasy Sports data. An account owner must create a Yahoo application, obtain Fantasy API approval, and authorize the app. This cannot be completed without their Yahoo authentication and any required review.

1. Sign in to the [Yahoo Developer Network](https://developer.yahoo.com/apps/) and create an application named **Fantasy Football Edge**.
2. Use `https://fantasy.ramideltoro.com` as the website URL.
3. Register this exact callback: `https://fantasy.ramideltoro.com/auth/yahoo/callback`.
4. Follow the [Fantasy Sports API access application](https://sports.yahoo.com/developer/access/). Request read-only NFL league, team, roster, matchup, standings, and available-player data. Do not request write access for this release.
5. Save the issued client ID and client secret as `YAHOO_CLIENT_ID` and `YAHOO_CLIENT_SECRET` in the owner's central credentials file, and install the same values in `/etc/fantasy-football-edge.env` on the backend VPS. Never paste credentials into GitHub or documentation.
6. Recreate the container so it receives the updated environment:

   ```sh
   cd /opt/fantasy-football-edge
   sudo docker compose up -d --force-recreate
   ```

7. Open the portal, click **Connect Yahoo**, and approve Yahoo's consent screen using the Yahoo account that owns the team. If multiple current-season NFL leagues are found, choose one from the league selector.

## Suggested API application description

Fantasy Football Edge is a personal fantasy football management dashboard for the owner's Yahoo NFL fantasy leagues. It reads the authenticated user's leagues and teams, current roster and injury/bye information, weekly matchup scores, standings, and a limited list of available players. It does not publish private league data or automate transactions. Yahoo OAuth tokens are stored encrypted on the application's backend. Team changes remain on Yahoo. Initial intended audience is the owner and their own leagues.

## Configuration

| Variable | Purpose |
| --- | --- |
| `APP_ORIGIN` | `https://fantasy.ramideltoro.com`; canonical OAuth redirect and Origin validation |
| `YAHOO_CLIENT_ID` | Yahoo application client ID |
| `YAHOO_CLIENT_SECRET` | Yahoo application client secret; server only |
| `YAHOO_API_BASE` | Defaults to `https://fantasysports.yahooapis.com/fantasy/v2`; use the API base assigned by Yahoo if different |
| `HOST` | `0.0.0.0` inside Docker |
| `PORT` | `3100` |
| `DATA_DIR` | `/app/data` inside the persistent Docker volume |

The authorization request uses the application's registered permissions rather than requesting extra scopes. OAuth endpoints are `https://api.login.yahoo.com/oauth2/request_auth` and `https://api.login.yahoo.com/oauth2/get_token`.

## Troubleshooting

- **Setup message:** one or both client credentials are absent. Set both and recreate the container.
- **Invalid redirect:** compare the registered redirect character for character, including HTTPS and callback path.
- **403 / API access not granted:** verify Fantasy API application approval and the API base assigned by Yahoo. OAuth success alone does not prove Fantasy API approval.
- **No leagues:** this release discovers NFL leagues for the current UTC calendar year. Check the signed-in Yahoo account and its season participation.
- **Expired session:** reconnect. Sessions last seven days; access tokens refresh server-side when needed.
- **Canceled consent:** return to Connect Yahoo and authorize again.

## Sources

- [Yahoo Fantasy Sports API](https://sports.yahoo.com/developer/)
- [Fantasy API documentation](https://sports.yahoo.com/developer/docs/)
- [Fantasy access application and default read-only access](https://sports.yahoo.com/developer/access/)
- [Yahoo OAuth authorization code flow](https://developer.yahoo.com/oauth2/guide/flows_authcode/)

Documentation checked September 9, 2026. Live integration awaits approved credentials and user consent.

## Registered application — September 9, 2026

A separate confidential-client application named **Fantasy Football Edge** was created with the owner's explicit approval of Yahoo's Developer Terms. App ID: `k95gYakw`. Homepage and callback match the production configuration above. Existing applications were not repurposed.

Yahoo issued client credentials, but the application currently shows no Fantasy Sports permissions. API access approval is still required. Credentials have not yet been copied into the central credentials file or installed on the VPS: the desktop's automatic approval review blocked Terminal UI access during the attempted secure transfer. No secret values were printed or committed. The owner can save the new values as `YAHOO_CLIENT_ID` and `YAHOO_CLIENT_SECRET` in their central credentials file so deployment can continue using the authorized server tools.

## Credentials installed and authorization reached

The owner saved the new credentials in the central credentials file. They were installed in the root-readable VPS environment file and the container was recreated. The public status endpoint now reports `configured:true`. The production Connect Yahoo flow successfully reaches Yahoo's authorization screen for Fantasy Football Edge using the signed-in account.

Yahoo presents a separate required acceptance of its OpenID and OAuth terms before the Agree button can be used. This acceptance and the OAuth callback/token exchange are pending. Reaching this screen does not establish Fantasy API permission.
