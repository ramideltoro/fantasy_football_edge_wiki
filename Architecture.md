# Architecture and security

## Request path

Browser → Cloudflare proxied DNS/TLS → backend VPS Caddy/TLS → `127.0.0.1:3100` → unprivileged Node.js container → Yahoo OAuth/Fantasy HTTPS endpoints.

The frontend is semantic HTML, responsive CSS, and browser JavaScript. The backend is Express 5 on Node.js 24, using fast-xml-parser to normalize Yahoo XML. There is no frontend build step. Dependency versions are locked in `package-lock.json`. Fonts load from Google Fonts with system font fallbacks.

## Data endpoints

| Portal route | Behavior |
| --- | --- |
| `GET /healthz` | Process liveness, independent of Yahoo configuration |
| `GET /api/status` | Whether credentials are configured and this browser has a session |
| `GET /auth/yahoo` | Creates a session-bound, single-use OAuth state and redirects to Yahoo |
| `GET /auth/yahoo/callback` | Validates state and exchanges authorization code server-side |
| `POST /api/disconnect` | Origin-checked removal of the current session |
| `GET /api/leagues` | Current calendar year's NFL leagues for the signed-in user |
| `GET /api/league/:key` | League teams, standings, scoreboard, available players, and owned-team roster |

League discovery calls `users;use_login=1/games;game_codes=nfl;seasons=YEAR/leagues`. League detail reads `/teams`, `/standings`, `/scoreboard`, and `/players;status=A;sort=OR;count=25`; the owned team's roster is fetched separately. A league key must first have been discovered in that browser's authenticated session. Outbound requests time out after 15 seconds; available Yahoo errors are translated into user-facing messages without returning tokens or raw upstream error bodies.

## Session and token handling

- Random 256-bit opaque session IDs in HttpOnly, Secure, SameSite=Lax cookies on production HTTPS.
- OAuth state is random, tied to the initiating browser, expires in ten minutes, and is consumed before token exchange.
- The session ID rotates after successful authorization.
- Session lifetime is seven days. Expired records are removed hourly.
- Access/refresh tokens remain server-side and refresh when access tokens are near expiration.
- Sessions are encrypted with AES-256-GCM and atomically written to disk. The local 32-byte encryption key and encrypted session file have mode 0600. The state directory is private to the container user.
- Both key and ciphertext are on the persistent volume. This protects copied session files alone, but does **not** protect against a host administrator or complete host/volume compromise.
- Every browser has its own session. There is no shared global Yahoo account and no email allowlist. Anyone may view the landing/demo; any Yahoo account allowed by the Yahoo app can authorize its own leagues once configured.
- Disconnect removes local authorization only; Yahoo-side revocation is separate.

## Server controls

Helmet sets CSP and other browser headers. API and auth responses are `no-store`. Referrer policy is `no-referrer`. JSON payloads are limited to 8KB. API requests are limited to 90/minute and authentication routes to 20/minute. Trust proxy is restricted to loopback; with Docker/proxy networking, limiting may aggregate requests under a proxy IP rather than individual visitors. This favors the small personal audience and does not trust arbitrary incoming forwarded headers.

The container runs as an unprivileged user, uses a read-only root filesystem, drops Linux capabilities, and prevents privilege escalation. It has a 256MB memory cap, one CPU limit, a private temporary filesystem, bounded logs, and a persistent data volume. The host port is loopback only. Caddy terminates origin TLS and supplies HSTS. Application code does not log OAuth URLs, credentials, or private league responses. Infrastructure log settings should continue to exclude callback query strings.

## Reliability limits

This is a single-instance application. Session state is loaded into memory and persisted in one encrypted file; do not scale to multiple replicas without replacing the store with coordinated storage. Concurrent refreshes are not distributed. A failed component of a league fetch currently returns a single error rather than partially rendering stale data. There is no automated backup or external monitoring configured by this release; Docker provides health status and restart-on-failure.
