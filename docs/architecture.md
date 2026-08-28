# PrivateEye — Architecture Deep Dive

## System Components

### 1. Caddy (Reverse Proxy)
- **Image**: `caddy:2`
- **Port**: 8080 (external) → 80 (internal)
- **Role**: Terminates connections, adds security headers, load balances
- **Config**: `Caddyfile`

### 2. SearXNG (Search Engine)
- **Image**: `searxng/searxng:latest`
- **Port**: 8080 (internal)
- **Role**: Meta search engine, queries 272+ external engines
- **Config**: `searxng/settings.yml`

### 3. Valkey (Cache)
- **Image**: `valkey/valkey:8-alpine`
- **Port**: 6379 (internal)
- **Role**: Caches search results, stores session data
- **Config**: Default

## Data Flow

```
User Search Query
       │
       ▼
┌─────────────┐
│   Browser   │
│ localhost:  │
│    8080     │
└──────┬──────┘
       │ HTTP
       ▼
┌─────────────┐
│   Caddy     │
│  (proxy)    │
│ Port 8080→80│
└──────┬──────┘
       │ HTTP
       ▼
┌─────────────┐     ┌─────────────┐
│  SearXNG    │────▶│   Valkey    │
│  (search)   │     │  (cache)    │
└──────┬──────┘     └─────────────┘
       │
       │ Random Profile + No Cookies
       ▼
┌─────────────────────────────────────┐
│         External Search Engines     │
│  ┌──────┐ ┌──────┐ ┌──────┐        │
│  │Google│ │ Bing │ │Wiki  │ ...    │
│  └──────┘ └──────┘ └──────┘        │
└─────────────────────────────────────┘
```

## Privacy Mechanisms

### Random Profile Generation
Each search request to external engines includes:
- Random User-Agent string
- Random Accept-Language header
- No cookies
- No persistent identifiers

### Request Anonymization
```
Request 1: User-Agent "Mozilla/5.0 (X11; Linux x86_64)..." + Profile-A: random
Request 2: User-Agent "Mozilla/5.0 (Windows NT 10.0)..." + Profile-B: random
Request 3: User-Agent "Mozilla/5.0 (Macintosh; Intel Mac OS X)..." + Profile-C: random
```

Each request appears to come from a different user.

### Referrer Stripping
When clicking a result:
- Original query: Hidden
- Referrer header: Removed
- Search engine: Not contacted
- Destination: Sees no referrer information

## Network Configuration

### Docker Network
```
searxng-network (bridge)
├── caddy (172.18.0.2)
├── searxng (172.18.0.3)
└── valkey (172.18.0.4)
```

### Port Mapping
```
Host:8080 → Caddy:80 → SearXNG:8080
```

## Storage

### Volumes
- `caddy_data`: Caddy TLS certificates and state
- `caddy_config`: Caddy configuration
- `searxng-data`: SearXNG favicon cache
- `valkey-data`: Valkey persistence (RDB snapshots)

## Scaling Considerations

For a local instance, single containers suffice. For multi-user:
- Add more SearXNG replicas
- Use Redis Cluster instead of single Valkey
- Add load balancer in front of Caddy
