# Deployment and operations

## Production inventory

| Item | Value |
| --- | --- |
| Public URL | https://fantasy.ramideltoro.com |
| Backend VPS | `65.75.201.18` |
| Checkout/source directory | `/opt/fantasy-football-edge` |
| Runtime | Docker Compose, Node.js 24 Alpine |
| Container | `fantasy-football-edge` |
| Host listener | `127.0.0.1:3100` |
| Environment file | `/etc/fantasy-football-edge.env`, mode 0600 |
| Persistent volume | `fantasy-football-edge_fantasy-edge-data` |
| Proxy | `/etc/caddy/Caddyfile` |
| DNS | Cloudflare proxied A record `fantasy.ramideltoro.com` → `65.75.201.18` |
| Origin certificate | Caddy-managed Let's Encrypt certificate |

Deployment added a dedicated Caddy site block. The preexisting configuration was backed up to `/etc/caddy/Caddyfile.before-fantasy-edge`; the candidate was validated before reload. Existing backend and Raspberry portal responses were checked after deployment.

## Updating code

The initial production directory was transferred from the source checkout and contains no Git credentials. To update, clone/pull the GitHub repository on a trusted workstation, run checks, transfer a clean archive, then rebuild:

```sh
npm ci
npm run check
npm test
git archive --format=tar.gz -o /tmp/ffe-release.tar.gz HEAD
scp /tmp/ffe-release.tar.gz 65.75.201.18:/tmp/ffe-release.tar.gz
```

On the VPS, use authorized sudo access:

```sh
sudo tar -xzf /tmp/ffe-release.tar.gz -C /opt/fantasy-football-edge
cd /opt/fantasy-football-edge
sudo docker compose up -d --build
sudo docker inspect --format '{{.State.Status}} {{.State.Health.Status}}' fantasy-football-edge
curl -fsS https://fantasy.ramideltoro.com/healthz
```

Archive-based updates do not remove deleted source files. Remove obsolete files deliberately during upgrades. Do not overwrite the environment file or delete the data volume. Updating source does not require changing Caddy unless routing changes. For environment changes, `docker compose up -d --force-recreate` is required.

## Checks and logs

```sh
sudo docker logs --tail 100 fantasy-football-edge
sudo docker inspect --format '{{.State.Health.Status}}' fantasy-football-edge
curl -fsS http://127.0.0.1:3100/healthz
curl -fsS https://fantasy.ramideltoro.com/api/status
```

Health `ok` means the app is running, not that Yahoo access is approved. A fresh browser currently receives `configured:false, connected:false`. After credentials are installed, `configured` should become true. Authenticated behavior must then be verified with the account owner.

## Backup and recovery

Back up `/etc/fantasy-football-edge.env`, the Caddy configuration, and the named Docker volume together into a private encrypted backup destination. Stop the container for a consistent session snapshot. The volume contains both `encryption.key` and `sessions.enc`; restoring one without its matching other file will prevent decryption. Treat the complete backup as credentials. No scheduled backup was introduced in this release.

To roll back code, transfer an archive of a known previous Git commit and rebuild. Preserve the volume and environment. To remove only the new proxy route, remove its dedicated block, validate the candidate, then reload Caddy; do not restore the old full file blindly if other services have since changed. Avoid `docker compose down -v`, which destroys session state. The app can restart with a fresh empty data volume, but all users must reconnect to Yahoo.

## Secrets

The owner's credentials remain in their designated central credentials file. Only Yahoo app credentials belong in this application's root-readable production environment file. No GitHub, Cloudflare, sudo, or unrelated service tokens are stored in the app, image, repositories, or documentation.
