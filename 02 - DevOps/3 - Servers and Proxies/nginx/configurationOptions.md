## Configuration Options / Explanations
Top-level (master process / OS integration)

- user www-data;
Runs worker processes as the www-data user (Debian/Ubuntu default). Minimizes damage if a worker is compromised.

- worker_processes auto;
Spawns one worker per CPU core (detected automatically). Good default for modern servers.

- pid /run/nginx.pid;
Stores the master process ID here so system tools (systemd, nginx -s reload) know how to signal Nginx.

- include /etc/nginx/modules-enabled/*.conf;
Debian/Ubuntu mechanism for loading dynamic modules (e.g., geoip, headers-more) that you’ve enabled. Harmless if directory is empty.

events { ... } (connection handling)

- worker_connections 1024;
Max simultaneous connections per worker. With worker_processes auto, effective ceiling is cores × 1024. Increase if you expect many concurrent clients.

- multi_accept on; (commented)
If enabled, a worker accepts as many queued connections as possible per event loop cycle. Leave off unless you know your load pattern.

http { ... } (applies to all HTTP servers)

- include /etc/nginx/mime.types;
Loads the standard mapping (e.g., .css → text/css, .svg → image/svg+xml) so browsers interpret files correctly.

- default_type application/octet-stream;
Fallback MIME type when a file extension isn’t recognized.

Logging

- log_format main ...
Defines a named log layout (main) including client IP, time, raw request line, status, bytes sent, referer, user-agent, and any existing X-Forwarded-For.

- access_log /var/log/nginx/access.log main;
Writes request logs using the main format to the default access log path.

- error_log /var/log/nginx/error.log warn;
Writes errors of level warn and higher (warn, error, crit, alert, emerg). Useful noise reduction compared to notice/info.

Core performance toggles

- sendfile on;
Uses the kernel’s zero-copy path to send static files efficiently.

- tcp_nopush on;
Optimizes packetization by sending response headers in a single packet (especially helpful with sendfile).

- tcp_nodelay on;
Ensures small, interactive responses (like API JSON chunks) aren’t delayed on keepalive connections.

- keepalive_timeout 65;
Keeps idle connections open for reuse up to 65 seconds. Improves latency and reduces handshake overhead.

- types_hash_max_size 2048;
Grows the internal hash table for MIME lookups to avoid hash collisions when many types are loaded.

Privacy/footprint

- server_tokens off;
Removes Nginx version from Server: header and error pages to reduce fingerprinting.

- Request body size

- client_max_body_size 20m;
Cap uploads (POST/PUT) to 20 MB. Adjust per your apps; too low yields “413 Request Entity Too Large.”

Compression

- gzip on;
Enables gzip compression for responses.

- gzip_comp_level 5;
Trade-off between CPU cost and size saved. 1–9; 5 is a pragmatic middle.

- gzip_vary on;
Adds Vary: Accept-Encoding, so caches/CDNs keep separate compressed/uncompressed variants.

- gzip_proxied any;
Allows compression even when the request passed through proxies.

- gzip_min_length 1024;
Skip compressing very small responses (<1KB), where overhead exceeds benefit.

- gzip_types ...;
Lists extra MIME types to compress beyond the default text/html. Includes CSS, JS, JSON, XML, SVG, etc. (Binary formats like PNG/JPEG are already compressed and omitted.)

Reverse-proxy defaults (you’ll inherit these in every server {} / location {})

- proxy_http_version 1.1;
Use HTTP/1.1 to upstreams. Required for WebSockets and connection reuse.

- proxy_redirect off;
Prevents Nginx from rewriting Location/Refresh headers coming back from your app (you typically want your app to control redirects).

- proxy_set_header Host $host;
Preserve the original Host (domain) so your app can generate correct absolute URLs and apply virtual-host logic.

- proxy_set_header X-Real-IP $remote_addr;
Pass the client’s IP (as seen by Nginx) to your app.

- proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
Append the client IP to the standard proxy chain header, preserving any upstream proxy info if present.

- proxy_set_header X-Forwarded-Proto $scheme;
Tells your app whether the original request was http or https (useful for generating secure links, cookies).

WebSockets upgrade helper

- map $http_upgrade $connection_upgrade { default upgrade; "" keep-alive; }
If the client sends an Upgrade: header (i.e., WebSockets/SSE), set $connection_upgrade to upgrade; otherwise set it to keep-alive. Using keep-alive (not close) avoids killing normal connections.

- proxy_set_header Upgrade $http_upgrade;
Forwards the client’s Upgrade header to your app when present (required for WebSockets).

- proxy_set_header Connection $connection_upgrade;
Sends Connection: upgrade for upgraded requests, Connection: keep-alive otherwise.

Upstream timeouts (sane starting points)

- proxy_connect_timeout 5s;
How long Nginx will wait to establish a TCP connection to your app (e.g., 127.0.0.1:3000).

- proxy_send_timeout 60s;
How long Nginx will allow for sending the full request to the app (covers slow upstream reads).

- proxy_read_timeout 60s;
How long Nginx waits for the app to respond (covers long-running handlers). Raise if you have heavy endpoints.

Load per-app virtual hosts

- include /etc/nginx/sites-enabled/*.conf;
Pulls in every enabled server block (one file per app/domain). This is where you’ll put:

- server { listen 80; server_name app1.example.com; location / { proxy_pass http://127.0.0.1:3000; } }, etc.

- If you later add TLS, add separate server { listen 443 ssl; ... } per app.
