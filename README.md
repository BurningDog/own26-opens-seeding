# OWN 2026 Opens — Pool Seeding Walkthrough

A single-page static site explaining the pool seeding process for OWN 2026 Opens. Served via [Caddy](https://caddyserver.com/) in Docker, with automatic HTTPS via Let's Encrypt.

## Requirements

- Docker and Docker Compose
- A domain pointing at the host's public IP
- Ports 80 and 443 (or your chosen ports) open on the host and forwarded through any router/firewall

## Setup

1. Copy the example env file and edit it:

   ```sh
   cp .env.example .env
   ```

2. Set values in `.env`:

   | Variable     | Description                                        | Example                    |
   | ------------ | -------------------------------------------------- | -------------------------- |
   | `DOMAIN`     | The fully-qualified domain name for the site       | `seeding.yourdomain.com`   |
   | `HTTP_PORT`  | Host port mapped to Caddy's internal HTTP port 80  | `80`                       |
   | `HTTPS_PORT` | Host port mapped to Caddy's internal HTTPS port 443 | `443`                     |

3. Start the container:

   ```sh
   docker compose up -d
   ```

   Caddy will automatically obtain and renew a TLS certificate from Let's Encrypt on first start.

4. Visit `https://<DOMAIN>` in a browser.

## Redeploying

After updating `index.html` on the server, restart the container to pick up the changes:

```sh
docker compose restart
```

## Stopping

```sh
docker compose down
```

The TLS certificate is stored in the `caddy_data` Docker volume and persists across restarts.

## LAN-only / no HTTPS

If you don't need HTTPS, change `Caddyfile` to:

```
:80 {
    root * /srv
    file_server
}
```

Remove the `HTTPS_PORT` line from `.env` and the `"${HTTPS_PORT}:443"` port mapping from `docker-compose.yml`, then point your browser at `http://<host-ip>:<HTTP_PORT>`.
