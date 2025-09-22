# Local n8n via Docker Compose

## Prerequisites
- Docker Engine 20.10+ with the Docker Compose plugin (`docker compose`).
- [mkcert](https://github.com/FiloSottile/mkcert) installed locally (only if you want trusted HTTPS on Windows).

## First-time setup
1. Ensure the `docker-data/n8n` directory exists (created by default in the repo).
2. (Optional) Export `N8N_HOST` and `GENERIC_TIMEZONE` in your shell to override the defaults used in `docker-compose.yml`.
3. For reverse-proxy deployments the compose file sets `WEBHOOK_URL=https://localhost/` and `N8N_EDITOR_BASE_URL=https://localhost/` so OAuth redirects and webhooks use the HTTPS endpoint served by Caddy.

## Enable trusted HTTPS on Windows (optional but recommended)
1. Install mkcert (pick one):
   - `winget install --source winget FiloSottile.mkcert`
   - `choco install mkcert`
   - `scoop install mkcert` (after `scoop bucket add extras`)
   - or download `mkcert.exe` from the releases page and place it on your `PATH`.
2. Open a new PowerShell window (mkcert must be on the `PATH`).
3. Trust mkcert's local CA and generate certs inside the repo:
   ```powershell
   mkcert -install
   mkcert -cert-file certs/localhost.pem -key-file certs/localhost-key.pem localhost
   ```
   The `certs` directory stays untracked (`.gitignore` excludes it), so sensitive keys never hit Git.

## Start n8n (accessible at https://localhost)
```powershell
# from the repository root
docker compose up -d
```
Caddy listens on ports 443/80 and forwards traffic to the n8n container. Visit https://localhost to use n8n over HTTPS. The first run pulls the `n8nio/n8n` image and persists application data in `docker-data/n8n`.

## Stop n8n
```powershell
docker compose down
```

## Updating
Pull the latest images and restart:
```powershell
docker compose pull
docker compose up -d
```