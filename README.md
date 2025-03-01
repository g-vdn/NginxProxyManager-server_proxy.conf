## Proxy Configuration for Nginx (Optimized for Plex, Nextcloud, Immich, and More)

This repository contains a Nginx reverse proxy configuration optimized for handling services like Plex, Nextcloud, and Immich.<br>
It ensures smooth media streaming, WebSocket support, and optimized performance for large file uploads, all while preserving real client IPs and bypassing common proxy-related issues.

## server_proxy.conf for Nginx Proxy Manager (NPM)
The server_proxy.conf file contains a set of configurations that are appended to all proxy hosts within Nginx Proxy Manager (NPM). This setup works due to how NPM handles custom configurations, where this file is injected at the end of each proxy host’s configuration.

This approach ensures that common proxy settings (such as client IP handling, WebSocket support, and timeouts) are consistently applied across all proxy hosts without needing to manually configure them for each service.

Official page: https://nginxproxymanager.com/

## Configuration Overview

### 1. Real IP Handling

This configuration ensures that the real IP addresses of clients are properly forwarded, allowing services like Plex and Nextcloud to correctly identify the client’s IP address.

The configuration includes multiple set_real_ip_from entries for networks such as local (10.x.x.x), VPN (WireGuard, Tailscale, ZeroTier), and Docker networks.

### 2. Proxy Headers

Optimized headers for Plex, Nextcloud, Immich, and other web services:

* Support for WebSocket connections.
* Correct forwarding of client IPs via X-Real-IP and X-Forwarded-For.
* Plex-specific headers such as X-Plex-Client-IP to ensure proper interaction between the proxy and Plex clients.

### 3. Timeouts & Buffering

To avoid stream dropouts and buffering issues during media playback (especially for Plex and Immich), the configuration disables request buffering and increases timeouts to ensure stable streaming:

* `proxy_request_buffering off`
* `proxy_connect_timeout 3600`
* `proxy_read_timeout 3600`

### 4. File Uploads & Compression

For services like Nextcloud and Immich, which often deal with large file uploads:

* The configuration sets client_max_body_size to 50GB for handling large files.
* Gzip compression is enabled for faster page load times, optimizing text and media files.

### 5. Error Pages

Custom error pages can be added to /data/nginx/error_pages/. If not desired, this block can be removed.

### 6. Plex Configuration

To ensure Plex can detect your LAN clients by IP correctly, and not display the proxy IP for all:

Add **domain.com** and **192.168.0.1:32400** to the Plex discovery URL settings in<br>
Plex web interface -> Settings -> Network -> Custom server access URLs -> `https://plex.domain.com,http://192.168.0.1:32400`


  
