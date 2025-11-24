## Proxy Configuration for Nginx (Optimized for Plex, Nextcloud, Immich, and More)

This repository contains an Nginx reverse proxy configuration optimized for handling services like Plex, Nextcloud, and Immich. It focuses on smooth media streaming, WebSocket support, and large file uploads while preserving real client IPs and avoiding common proxy pitfalls.

## Using `server_proxy.conf` with Nginx Proxy Manager (NPM)

`server_proxy.conf` contains directives that are injected at the end of every proxy host configuration created in Nginx Proxy Manager. This keeps common proxy behavior (real IP handling, WebSocket support, timeouts, and uploads) consistent across all hosts without repeating settings in each proxy definition.

- Official project: https://nginxproxymanager.com/
- Default location for custom files: `/data/nginx/` (inside the NPM container)

### Quick Start
1. Copy `server_proxy.conf` into `/data/nginx/` on the NPM host or container.
2. Restart the NPM container (`docker restart <npm-container>`) to load the file.
3. Review the `set_real_ip_from` entries and adjust them to match your LAN, VPN, and overlay networks.

## Configuration Overview

### 1. Real IP Handling
Client IPs are forwarded correctly so services like Plex and Nextcloud can identify users. The provided `set_real_ip_from` ranges cover common private, Docker, and Tailscale (CGNAT) networks, but you should tailor them to your environment.

### 2. Proxy Headers
Optimized headers for Plex, Nextcloud, Immich, and other web services:

* WebSocket-friendly headers (`Upgrade`, `Connection`, and `proxy_http_version 1.1`).
* Correct forwarding of client IPs via `X-Real-IP` and `X-Forwarded-For`.
* Plex-specific headers such as `X-Plex-Client-IP` to ensure proper interaction between the proxy and Plex clients.

### 3. Timeouts & Buffering
To avoid stream dropouts and buffering issues during media playback (especially for Plex and Immich), the configuration disables request buffering and increases timeouts to keep long-lived streams stable:

* `proxy_request_buffering off`
* `proxy_buffering off`
* `proxy_connect_timeout 3600`
* `proxy_read_timeout 3600`

### 4. File Uploads & Compression
For services that handle large uploads (Nextcloud, Immich), the configuration allows up to 50GB per request and enables gzip compression for faster delivery of text-based assets.

### 5. Error Pages
Custom error pages can be added to `/data/nginx/error_pages/`. If you do not use custom pages, leave the include in place or remove it to avoid extra file lookups.

### 6. Plex Configuration
To ensure Plex correctly detects LAN clients rather than showing the proxy IP, add your public and LAN URLs in the Plex UI:

* **Settings → Network → Custom server access URLs**: `https://plex.domain.com,http://192.168.0.1:32400`


  
