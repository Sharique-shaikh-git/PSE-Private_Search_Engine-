# PSE - Private Search Engine

A fully self-hosted, privacy-respecting meta search engine built with SearXNG, Docker, and Caddy.

## Why This Project?

Every search you make on Google, Bing, or DuckDuckGo is tracked, profiled, and sold to advertisers. This project gives you:

- **Complete privacy** — no tracking, no profiling, no ads
- **272+ search engines** — aggregates results from Google, Bing, Wikipedia, DuckDuckGo, and more
- **Random profiles** — each search uses a new anonymous profile
- **Self-hosted** — runs entirely on your machine, no cloud, no third-party trust
- **Tor support** — route searches through Tor for maximum anonymity
- **Proxy support** — use HTTP/SOCKS proxies for IP rotation

## Architecture

```
┌───────────────────────────────────────────────────────┐
│                    Your Windows PC                     │
│                                                       │
│  Browser → http://localhost:8080                      │
│                                                       │
│  ┌─────────────────────────────────────────────────┐  │
│  │              Docker Desktop (WSL2)              │  │
│  │                                                 │  │
│  │  ┌──────────┐    ┌──────────────┐              │  │
│  │  │  Caddy   │───▶│   SearXNG    │              │  │
│  │  │ (reverse │    │ (search UI   │              │  │
│  │  │  proxy)  │    │  + engine)   │              │  │
│  │  └──────────┘    └──────┬───────┘              │  │
│  │                         │                       │  │
│  │                    ┌────┴─────┐                 │  │
│  │                    │ Valkey   │                 │  │
│  │                    │ (cache)  │                 │  │
│  │                    └──────────┘                 │  │
│  └─────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────┘
          │
          ▼ (outbound queries via random profiles)
   ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
   │  Google  │ │   Bing   │ │ DuckDuck │ │ Wikipedia│
   │          │ │          │ │    Go    │ │  + 267   │
   └──────────┘ └──────────┘ └──────────┘ └──────────┘
```

## How Privacy Is Protected

### 1. Random Search Profiles
Every search creates a **new anonymous profile**. Search engines see a different user each time — they cannot build a profile about you.

### 2. No Cookies Sent to Engines
SearXNG never sends cookies to external search engines. Each request is clean and isolated.

### 3. No Tracking or Ads
Unlike Google or DuckDuckGo, SearXNG does not serve ads or tracking content. Your data stays with you.

### 4. Referrer Header Stripped
When you click a result, the referrer header is removed — the destination website doesn't know what you searched for.

### 5. IP Address Hidden (with Tor/Proxy)
- **Without proxy**: Search engines see your local IP
- **With Tor**: Your IP is completely hidden
- **With proxy**: Search engines see the proxy's IP

### 6. HTTP Security Headers
The Caddy reverse proxy adds security headers:
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `Referrer-Policy: no-referrer`
- `Permissions-Policy` (restrictive)

## Privacy Comparison

| Feature | Google | DuckDuckGo | PSE (Ours) |
|---------|--------|------------|-------------|
| User tracking | Yes | Partial | **No** |
| Profiling | Yes | Partial | **No** |
| Ads | Yes | Yes | **No** |
| IP hidden | No | No | **Yes (with Tor)** |
| Data sold | Yes | Yes | **Never** |
| Source code | Closed | Closed | **Open source** |
| Self-hosted | No | No | **Yes** |
| Engine count | 1 | ~400 | **272+** |
| Random profiles | No | No | **Yes** |
| Referrer stripped | No | No | **Yes** |

## Tech Stack

| Component | Purpose |
|-----------|---------|
| **SearXNG** | Meta search engine (aggregates 272+ engines) |
| **Caddy** | Reverse proxy with automatic HTTPS |
| **Valkey** | Redis-compatible cache for performance |
| **Docker** | Containerization and orchestration |
| **Docker Compose** | Multi-container management |

## Quick Start

### Prerequisites
- Docker Desktop installed and running
- Windows 10/11 with WSL2 enabled

### Installation

```bash
# 1. Clone or navigate to this project
cd "D:\project\temp project\PSE ( Private_Search_Engine)"

# 2. Start all containers
docker compose up -d

# 3. Verify containers are running
docker compose ps

# 4. Open in browser
# http://localhost:8080
```

### Management Commands

```bash
# Start the search engine
docker compose up -d

# Stop the search engine
docker compose down

# View logs
docker compose logs -f core

# Restart with fresh containers
docker compose down && docker compose up -d

# Access SearXNG shell (for troubleshooting)
docker compose exec -it --user root core /bin/sh -l

# Pull latest images
docker compose pull && docker compose up -d
```

## Configuration

### Main Config Files

| File | Purpose |
|------|---------|
| `docker-compose.yml` | Container orchestration |
| `.env` | Environment variables (secret key) |
| `searxng/settings.yml` | SearXNG configuration |
| `Caddyfile` | Reverse proxy config |

### Enabling Tor (Maximum Privacy)

1. Add Tor container to `docker-compose.yml`:
```yaml
  tor:
    image: dperson/torproxy
    restart: unless-stopped
    ports:
      - "9050:9050"
    networks:
      - searxng-network
```

2. Update `searxng/settings.yml`:
```yaml
outgoing:
  using_tor_proxy: true
  proxies:
    all://:
      - socks5://tor:9050
```

3. Enable Tor check plugin:
```yaml
plugins:
  searx.plugins.tor_check.SXNGPlugin:
    active: true
```

4. Restart: `docker compose down && docker compose up -d`

### Enabling Proxy

In `searxng/settings.yml`, under `outgoing:`:
```yaml
outgoing:
  proxies:
    all://:
      - http://your-proxy:8080
      - socks5://your-proxy:1080
```

### Changing Search Engines

In `searxng/settings.yml`, modify the `engines:` section:
```yaml
use_default_settings:
  engines:
    remove:
      - google      # Remove Google
    # or keep only specific engines:
    # keep_only:
    #   - duckduckgo
    #   - wikipedia
```

### Changing Theme

In `searxng/settings.yml`:
```yaml
ui:
  default_theme: simple
  theme_args:
    simple_style: "dark"    # Options: auto, light, dark, black
```

### Built-in Plugins

| Plugin | Description |
|--------|-------------|
| Calculator | Evaluate math expressions in search |
| Hash Values | Compute hash values |
| Self-Info | Check what sites know about you |
| Tracker URL Remover | Strip tracking parameters |
| Unit Converter | Convert units in search |
| Hostnames | Custom hostname rules |
| Tor Check | Detect Tor connections |
| Infinite Scroll | Lazy-load results |
| OA DOI Rewrite | Academic paper DOI links |

## Built-in Features

### Search API (JSON)
```bash
curl "http://localhost:8080/search?q=test&format=json"
```

### Search API (RSS)
```bash
curl "http://localhost:8080/search?q=test&format=rss"
```

### Stats Page
Visit `http://localhost:8080/stats` to see search statistics.

### Preferences
Visit `http://localhost:8080/preferences` to customize your search experience.

## Resume Talking Points

### Technical Skills Demonstrated
- **Docker**: Multi-container orchestration with Compose
- **Networking**: Reverse proxy, port mapping, proxy chains
- **Privacy**: Random profiles, no tracking, Tor integration
- **Security**: HTTP headers, rate limiting, referrer policy
- **YAML**: Configuration management
- **Linux/CLI**: Container management and troubleshooting

### Key Features for Interview
1. **Meta-search aggregation**: Queries 272+ engines simultaneously
2. **Privacy-first architecture**: Random profiles per search
3. **Self-hosted**: Full data sovereignty
4. **Proxy/Tor integration**: Multiple layers of anonymity
5. **Plugin system**: Extensible architecture
6. **API access**: JSON/RSS endpoints for integration

## Troubleshooting

### Containers won't start
```bash
docker compose down -v
docker compose up -d
```

### Port 8080 already in use
Change the port in `docker-compose.yml`:
```yaml
ports:
  - "8081:80"  # Change to 8081 or another port
```

### Search engines returning no results
Check if SearXNG can reach external engines:
```bash
docker compose exec core curl -s "https://google.com" | head -20
```

### Reset everything
```bash
docker compose down -v
rm -rf searxng/
# Re-clone and reconfigure
```

## License

This project uses:
- [SearXNG](https://github.com/searxng/searxng) — AGPL-3.0
- [Caddy](https://github.com/caddyserver/caddy) — Apache-2.0
- [Valkey](https://github.com/valkey-io/valkey) — BSD-3-Clause

## Acknowledgments

- [SearXNG Documentation](https://docs.searxng.org/)
- [NetworkChuck Tutorial](https://youtube.com/@NetworkChuck)
- The open-source privacy community

---

**Built with privacy in mind. Your searches, your data, your control.**
