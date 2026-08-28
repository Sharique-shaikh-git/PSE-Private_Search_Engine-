# Privacy Comparison: PrivateEye vs Google vs DuckDuckGo

## Overview

This document compares the privacy features of PrivateEye against major search providers.

## Feature Comparison

| Feature | Google | DuckDuckGo | Bing | PrivateEye |
|---------|--------|------------|------|-------------|
| **User Tracking** | Yes (extensive) | Partial | Yes | **No** |
| **Search Profiling** | Yes | Partial | Yes | **No** |
| **Ad Targeting** | Yes | Yes | Yes | **No** |
| **IP Logging** | Yes | Yes | Yes | **No (with Tor)** |
| **Cookie Tracking** | Yes | No | Yes | **No** |
| **Referrer Data** | Yes | Yes | Yes | **No** |
| **Data Selling** | Yes | Yes | Yes | **Never** |
| **Source Code** | Closed | Closed | Closed | **Open source** |
| **Self-Hosted** | No | No | No | **Yes** |
| **Random Profiles** | No | No | No | **Yes** |
| **Tor Support** | No | No | No | **Yes** |
| **Proxy Support** | No | No | No | **Yes** |
| **Engine Count** | 1 | ~400 | 1 | **272+** |
| **Ad-Free** | No | No | No | **Yes** |
| **JavaScript Required** | Yes | Optional | Yes | **Optional** |
| **Data Retention** | Indefinite | 90 days | Indefinite | **None** |

## Detailed Analysis

### Google
- Tracks every search and builds detailed user profiles
- Uses search history for ad targeting
- Stores IP addresses indefinitely
- Shares data with third parties
- Serves personalized ads across all Google services
- Chrome browser sends browsing data to Google

### DuckDuckGo
- Does not store personal information
- BUT has a relationship with Microsoft (Bing)
- Microsoft trackers are present on some pages
- Less tracking than Google, but not zero
- No random profile generation
- No Tor integration

### Bing (Microsoft)
- Tracks user searches
- Builds advertising profiles
- Shares data with Microsoft ecosystem
- No privacy-first features
- Aggressive ad targeting

### PrivateEye
- **Zero tracking**: No profiles, no cookies, no history
- **Random profiles**: Each search appears to come from a different user
- **No ads**: Never serves advertisements
- **Self-hosted**: Your data never leaves your machine
- **Tor integration**: Route through Tor for maximum anonymity
- **Proxy support**: Use multiple proxies for IP rotation
- **Open source**: Full transparency, auditable code
- **No data retention**: Nothing is stored long-term
- **Referrer stripping**: Destination sites don't know your search query
- **272+ engines**: Better results from multiple sources

## Privacy Levels

### Level 1: Basic (Default PrivateEye)
- Random profiles per search
- No cookies sent to engines
- Referrer stripped
- **Risk**: Search engines see your IP

### Level 2: Enhanced (With Proxy)
- All Level 1 features
- Requests routed through proxy
- **Benefit**: Search engines see proxy IP, not yours

### Level 3: Maximum (With Tor)
- All Level 1 features
- All traffic routed through Tor network
- **Benefit**: Complete anonymity, IP hidden from everyone
- **Tradeoff**: Slower search speeds

## What Search Engines Know About You

### With Google
```
Query: "best coffee shops"
Google knows:
  - Your IP address
  - Your location (approximate)
  - Your search history
  - Your interests (from past searches)
  - Your device type
  - Your browser
  - Your operating system
  - Your screen resolution
  - Your language preferences
  - And much more...
```

### With PrivateEye
```
Query: "best coffee shops"
Search engines know:
  - Random IP (or Tor exit node)
  - Random browser profile
  - Nothing else
```

## Legal & Compliance

PrivateEye helps with:
- **GDPR**: No personal data processing
- **CCPA**: No data collection or sale
- **HIPAA**: No PHI exposure through search
- **Enterprise privacy**: No data leaves the network

## Conclusion

PrivateEye provides the highest level of search privacy available:
1. No tracking
2. No profiling
3. No ads
4. No data retention
5. Full control
6. Open source transparency

For maximum privacy, combine PrivateEye with Tor and a VPN.
