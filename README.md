# PrivateEye — Private Search Engine

> *Your Search. Your Privacy. Your Control.*

A fully self-hosted, privacy-respecting meta search engine. Built for clients who demand **complete data ownership** — no tracking, no profiling, no third-party trust.

## Why PrivateEye?

Every search you make on Google, Bing, or DuckDuckGo is tracked, profiled, and sold to advertisers. PrivateEye gives you:

- **Complete privacy** — no tracking, no profiling, no ads
- **272+ search engines** — aggregates results from Google, Bing, Wikipedia, DuckDuckGo, and more
- **Random profiles** — each search uses a new anonymous profile
- **Self-hosted** — runs entirely on your machine, no cloud, no third-party trust
- **Tor support** — route searches through Tor for maximum anonymity
- **Custom branding** — fully branded as your own solution

## Architecture

```
┌───────────────────────────────────────────────────────┐
│                    Your Machine                        │
│                                                       │
│  Browser → http://localhost:8080                      │
│                                                       │
│  ┌─────────────────────────────────────────────────┐  │
│  │              Docker Desktop (WSL2)              │  │
│  │                                                 │  │
│  │  ┌──────────┐    ┌──────────────┐              │  │
│  │  │  Caddy   │───▶│   SearXNG    │              │  │
│  │  │ (reverse │    │ (PrivateEye  │              │  │
│  │  │  proxy)  │    │  UI + Engine)│              │  │
│  │  └──────────┘    └──────┬───────┘              │  │
│  │                         │                       │  │
│  │                    ┌────┴─────┐   ┌──────────┐ │  │
│  │                    │ Valkey   │   │   Tor    │ │  │
│  │                    │ (cache)  │   │ (proxy)  │ │  │
│  │                    └──────────┘   └──────────┘ │  │
│  └─────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────┘
          │
          ▼ (outbound queries via random profiles)
   ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
   │  Google  │ │   Bing   │ │ DuckDuck │ │ Wikipedia│
   │          │ │          │ │    Go    │ │  + 267   │
   └──────────┘ └──────────┘ └──────────┘ └──────────┘
```

## Privacy Features

### How Privacy Is Protected

| Layer | Protection | Effect |
|-------|-----------|--------|
| **Random Profiles** | New anonymous profile per search | Engines can't build a profile |
| **No Cookies** | Cookies stripped from external requests | Clean, isolated requests |
| **No Tracking** | No ads, no tracking content | Your data stays with you |
| **Referrer Stripped** | Destination sites don't know your query | Browsing stays private |
| **Tor Support** | Route through Tor network | IP completely hidden |
| **Security Headers** | Caddy adds protection headers | XSS, sniffing, framing blocked |

### Privacy Comparison

| Feature | Google | DuckDuckGo | PrivateEye |
|---------|--------|------------|------------|
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
| **SearXNG** | Meta search engine (272+ engines) |
| **Caddy** | Reverse proxy with security headers |
| **Valkey** | Redis-compatible cache |
| **Tor** | Anonymous proxy (optional) |
| **Docker Compose** | Container orchestration |

## Quick Start

### Prerequisites
- Docker Desktop installed and running
- Windows 10/11 with WSL2

### Launch

```bash
# 1. Navigate to project
cd "PSE - Private_Search_Engine"

# 2. Start all services
docker compose up -d

# 3. Open in browser
# http://localhost:8080
```

### Management

```bash
docker compose up -d          # Start
docker compose down           # Stop
docker compose logs -f        # View logs
docker compose ps             # Check status
docker compose down -v        # Full reset
```

## Configuration

### Config Files

| File | Purpose |
|------|---------|
| `docker-compose.yml` | Container orchestration |
| `.env` | Environment variables (secret key) |
| `searxng/settings.yml` | SearXNG configuration + branding |
| `searxng/static/themes/simple/img/` | Custom logo & favicon |
| `Caddyfile` | Reverse proxy config |

### Enable Tor (Maximum Privacy)

In `searxng/settings.yml`, under `outgoing:`:
```yaml
outgoing:
  proxies:
    all://:
      - socks5://tor:9050
```

### Custom Branding

Logo and favicon are in:
```
searxng/static/themes/simple/img/
├── searxng.svg      (main logo)
└── favicon.svg      (browser tab icon)
```

Replace these SVG files to rebrand for any client.

### Built-in Plugins

| Plugin | Description |
|--------|-------------|
| Calculator | Math expressions in search |
| Hash Values | Compute hash values |
| Self-Info | Check what sites know about you |
| Tracker URL Remover | Strip tracking parameters |
| Unit Converter | Convert units |
| Tor Check | Detect Tor connections |
| Infinite Scroll | Lazy-load results |

## API

```bash
# JSON
curl "http://localhost:8080/search?q=test&format=json"

# RSS
curl "http://localhost:8080/search?q=test&format=rss"

# Stats
# http://localhost:8080/stats

# Preferences
# http://localhost:8080/preferences
```

## Resume Talking Points

### Technical Skills Demonstrated
- **Docker**: Multi-container orchestration with Compose
- **Networking**: Reverse proxy, port mapping, proxy chains
- **Privacy Engineering**: Random profiles, no tracking, Tor integration
- **Security**: HTTP headers, rate limiting, referrer policy
- **Configuration Management**: YAML, environment variables
- **Infrastructure**: Self-hosted deployment

### Key Features for Interview
1. **Meta-search aggregation**: Queries 272+ engines simultaneously
2. **Privacy-first architecture**: Random profiles per search
3. **Self-hosted**: Full data sovereignty
4. **Proxy/Tor integration**: Multiple layers of anonymity
5. **Custom branding**: Client-ready white-label solution
6. **API access**: JSON/RSS endpoints for integration

## Troubleshooting

```bash
# Containers won't start
docker compose down -v && docker compose up -d

# Port conflict
# Change "8080:80" to "8081:80" in docker-compose.yml

# Check engine connectivity
docker compose exec searxng curl -s "https://google.com" | head -20

# Full reset
docker compose down -v
rm -rf searxng/static/
# Reconfigure and restart
```

## License

Built on open-source foundations:
- [SearXNG](https://github.com/searxng/searxng) — AGPL-3.0
- [Caddy](https://github.com/caddyserver/caddy) — Apache-2.0
- [Valkey](https://github.com/valkey-io/valkey) — BSD-3-Clause

---

**PrivateEye** — Built for clients who take privacy seriously.
