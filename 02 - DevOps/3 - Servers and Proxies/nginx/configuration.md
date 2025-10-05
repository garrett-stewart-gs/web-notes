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

  # sets user, reduces exposure/risk if worker is compromised
  user  www-data; # is it common to use www-data or should I make a custom one?

  # this matches the number of workers to the number of CPU cores available in the host machine
  worker_processes auto; 

  # stores the master process ID in the chosen path/file, so the system knows how to signal nginx
  pid /run/nginx.pid; 

  # debian/ubuntu convention: load any dynamic modules that have been enabled
  include /etc/nginx/modules-enabled/*.conf;

  events {
    # defines how many connections each worker handles (affects RAM usage)
    worker_connections 1024;
  }

  http {

    # CONTENT-TYPE HEADERS
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

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
    keepalive_timeout 65;
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

    # REVERSE PROXY DEFAULTS (inherited by all servers/locations)
    proxy_http_version 1.1;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    # do i need these here?
    proxy_set_header User-Agent $http_user_agent;
    proxy_set_header Cookie $http_cookie;
    proxy_set_header Accept-Language $http_accept_language;
    proxy_set_header Authorization $http_authorization;


    # WebSockets / Next.js HMR (WHAT IS THIS EXACTLY?)
    map $http_upgrade $connection_upgrade {
      default upgrade;
      ""      keep-alive;
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