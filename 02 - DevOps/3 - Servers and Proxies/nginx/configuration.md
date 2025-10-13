## Nginx Configuration Overview:  
- Nginx is usually configured using MULTIPLE .conf files
- For a non-containerized reverse proxy (your most likely setup), the configuration structure is as follows:
  ``` bash
  /etc/nginx/
  ├─ nginx.conf # for global settings
  ├─ sites-available/
  │  ├─ app1.conf # for app specific settings
  │  ├─ app2.conf # for app specific settings
  │  └─ app3.conf # for app specific settings
  └─ sites-enabled/ # symlinks to enabled vhosts

  NOTE: Store your ssl certs at /etc/ssl/private/<domain>.pem
  ```
- Using this structure has the following benefits:
  - Separation of concerns (between apps)
  - Toggle-ability - sites enabled allows you to specify which apps are available, without editing
  - Safer reloads - a broken app config won't force you to touch the global config

## Global Nginx Configuration File
- Process & Worker Settings:
  - user
  - worker_processes
  - events
- Top-level http settings:
  - include /etc/nginx/mime.types; ???
  - logging defaults and log_format definitions
  - performance toggles (sendfile, tcp_nopush, keepalive_timeout)
  - reasonable defaults (client_max_body_size, gzip, etc)
  - proxy header defaults (each app inherits these)
  - maps/upstreams (optional)
  - include /etc/nginx/sites-enabled/*.conf (for toggling active sites)

## App-level Nginx Configuration File
- 1 or 2 `server {}` blocks:
  - port 80 http - 301 to HTTPS (what does this mean?)
  - port 443 HTTPS with certs, HSTS, etc.
- App-specific routing:
  - `location/` with `proxy_pass` to the correct port (ie. `127.0.0.1:<port>`)
  - location rules (static file alias, health checks, `/api/`, web sockets)
- Per-app knobs
  - `auth_basic` (which is useful for gating staging services, per-app rate limiting, cache rules, custom logs)

## Global Nginx Config File Example
```nginx
# GLOBAL NGINX CONFIG
user www-data;
worker_processes auto;
worker_rlimit_nofile 100000;
pid /run/nginx.pid;

events {
  worker_connections 4096;
}

http {
  # CONTENT-TYPE HEADERS
  include /etc/nginx/mime.types;
  default_type application/octet-stream;

  # this value is dependent on your vps
  resolver 213.186.33.99 1.1.1.1 valid=300s;
  resolver_timeout 5s;

  # PRIVACY / SECURITY
  server_tokens off;
  add_header X-Content-Type-Options "nosniff" always; # (minimizes bugs & prevents some code injections involving plain text)

  # LOGGING
  log_format proxy '$remote_addr - $host "$request" $status '
    'upstream=$upstream_addr us=$upstream_status '
    'rt=$request_time urt=$upstream_response_time ref="$http_referer" ua="$http_user_agent"';
  access_log /var/log/nginx/access.log proxy;
  error_log  /var/log/nginx/error.log warn;

  # CORE PERFORMANCE TOGGLES
  sendfile on;
  tcp_nopush on;
  tcp_nodelay on;
  keepalive_timeout 30;
  keepalive_requests 1000;
  types_hash_max_size 2048;

  # BODY REQUEST LIMITS (can raise if website accepts large uploads)
  client_max_body_size 20m;

  # COMPRESSION
  gzip on;
  gzip_comp_level 5;
  gzip_vary on;
  gzip_proxied any;
  gzip_min_length 1024;
  gzip_types
    text/plain
    text/css
    application/json
    application/javascript
    application/xml
    application/rss+xml
    application/atom+xml
    image/svg+xml;
  gzip_disable "msie6";

  # REVERSE PROXY DEFAULTS (inherited by all servers/locations)
  proxy_http_version 1.1;
  proxy_redirect off;
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Host  $host;                        # Some frameworks prefer this over Host
  proxy_set_header X-Forwarded-Port  $server_port;                 # Lets apps reconstruct absolute URLs
  proxy_set_header User-Agent $http_user_agent;

  # WebSockets / Next.js HMR (WHAT IS THIS EXACTLY?)
  map $http_upgrade $connection_upgrade {
    default close;
    'websocket' upgrade;
  }
  proxy_set_header Upgrade    $http_upgrade;
  proxy_set_header Connection $connection_upgrade;

  # TIMEOUTS (tune per-app if you have long-running endpoints)
  proxy_connect_timeout 5s;
  proxy_send_timeout 60s;
  proxy_read_timeout 60s;

  # LOAD PER-APP VIRTUAL HOSTS
  include /etc/nginx/sites-enabled/*.conf;

}

```

```nginx
# PER-APP NGINX CONFIG
# Redirect 80 -> 443
server {
  listen 80;
  listen [::]:80;
  server_name freshairmedia.garrett-stewart.com;
  return 301 https://$host$request_uri;
}

# Cloudflare <-> origin TLS
server {
  listen 443 ssl;
  listen [::]:443 ssl;
  http2 on;
  server_name freshairmedia.garrett-stewart.com;

  # Cloudflare Origin cert (wildcard for *.garrett-stewart.com)
  ssl_certificate     /etc/ssl/cloudflare/garrett-stewart.com.origin.crt;
  ssl_certificate_key /etc/ssl/cloudflare/garrett-stewart.com.origin.key;
  ssl_protocols TLSv1.2 TLSv1.3;
  ssl_prefer_server_ciphers off;
  ssl_session_cache shared:SSL:10m;

  # HSTS (good since you’re behind CF on Full Strict)
  add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

  # If you enabled Cloudflare Authenticated Origin Pulls, uncomment:
  # ssl_client_certificate /etc/ssl/cloudflare/origin-pull-ca.pem;
  # ssl_verify_client on;

  # Proxy to your app
  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_http_version 1.1;
    proxy_set_header Host              $host;
    proxy_set_header X-Real-IP         $remote_addr;
    proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    # Helpful for Next.js/websockets
    proxy_set_header Upgrade           $http_upgrade;
    proxy_set_header Connection        $connection_upgrade;
    proxy_read_timeout 60s;
  }
}



```