## How To Structure Your Docker Project
  - Have an "infra repo" for your docker project/folder on github
  - This infra repo should contain:
    - `docker-compose.dev.yml` (dev-specific compose file)
    - `docker-compose.prod.yml` (prod-specific compose file)
    - `.env.dev.example` (placeholders/safe defaults for env files, committed to git)
    - `.env.dev` (env file for dev environment, not committed to git)
    - `.env.prod.example` (placeholders/safe defaults for env files, committed to git)
    - `.env.prod` (env file for prod environment, not commited to git)
    - `db/`
      - `init/` (one-time SQL/SH bootstrap, read-only bind mount)
        - database
        - users/roles
        - permissions
        - schemas
        - extensions
        - etc.
      - `seeds/` (ensure this is separate from init, never seed a production database!)
      - `backups/`
        - `dev/`
        - `prod/`
    - `scripts/`
      - dev-up.sh
      - dev-down.sh
      - prod-up.sh
      - prod-down.sh
    - `proxy/` (nginx/traefik/caddy config + certs dir if you run a reverse proxy)
    - `secrets/` (for highly sensitive info: api keys, passwords)
    - `systemd/` (optional unit file to auto-start the stack on boot)
    - `.gitignore` (ignore .env*, db/backups/**, logs, dumps)
    - `README.md` (runbook: how to deploy, backup, restore, rollback)
  - Docker Compose will ensure the correct images are installed, and download them if necessary
  - This structure keeps your code out of the repo (so it is not a repo within a repo). 


### Docker Compose Dev (webapp + db)
```yml

  services:

    db:
      image: postgres:16
      environment:
        POSTGRES_USER: ${POSTGRES_USER}
        POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
        POSTGRES_DB: ${POSTGRES_DB} # ie. <dbName>_dev
      volumes: 
        - db_data:/var/lib/postgresql/data
        - ./db/init:/docker-entrypoint-initdb.d:ro
      ports:
        - "5432:5432"
      healthcheck:
        test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER} -d $${POSTGRES_DB} -h 127.0.0.1"] 
        interval: 10s
        timeout: 5s
        retries: 5
      restart: unless-stopped 

    webapp:
      image: node:20-bookworm
      working_dir: /app # creates a directory inside the container that we are working inside
      user: "node" 
      depends_on:
        db:
          condition: service_healthy
      command: > # this command runs npm ci to update container's node_modules if it is empty OR package-lock.json is newly updated compared to STAMP file. Once node_modules are confirmed, it starts a dev server
        bash -lc 'set -e;
          LOCK=package-lock.json; STAMP=node_modules/.installed-lock;
          if [ ! -d node_modules ] || [ "$LOCK" -nt "$STAMP" ]; then
            npm ci --no-audit --no-fund --prefer-offline;
            touch -r "$LOCK" "$STAMP";
          fi;
          npm run dev
        '
      ports:
        - "${WEB_APP_HOST_PORT}:${WEB_APP_CONTAINER_PORT}"
      environment:
        PORT: ${WEB_APP_CONTAINER_PORT}
        POSTGRES_HOST: db
        POSTGRES_PORT: 5432
        POSTGRES_USER: ${POSTGRES_USER}
        POSTGRES_PASSWORD: ${POSTGRES_PASSWORD} 
        POSTGRES_DB: ${POSTGRES_DB}
        CHOKIDAR_USEPOLLING: "true" # dev only. ensures dev container has up-to-date files (others' default, such as nodemon, eslint watchers, vite, etc.)
        WATCHPACK_POLLING: "true" # dev only. ensures dev container has up-to-date files (nextjs default)
      volumes:
        - ${WEB_APP_SRC}:/app # binds web app code into container's working directory
        - web_node_modules:/app/node_modules # prevents reinstallation on every restart
      restart: unless-stopped

  volumes:
    db_data: {}
    web_node_modules: {}

```

### ENV.DEV
```bash

DOPPLER_TOKEN= # Doppler secrets manager ACCESS TOKEN
COMPOSE_PROJECT_NAME=myapp_dev

WEB_APP_SRC= #ABS PATH TO WEB APP
WEB_APP_HOST_PORT=3000 
WEB_APP_CONTAINER_PORT=3000

POSTGRES_HOST=db # service name for docker compose
POSTGRES_PORT=5432
# POSTGRES_USER, POSTGRES_PASSWORD, POSTGRES_DB should be set in secrets manager (doppler)

```

### Docker Compose Prod (webapp + db + db_backup)
```yml

  services:
    db:
      image: postgres:16
      environment:
        POSTGRES_USER: ${POSTGRES_USER}
        POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
        POSTGRES_DB: ${POSTGRES_DB} # ie. <dbName>_prod
      volumes: 
        - db_data:/var/lib/postgresql/data
        - ./db/init:/docker-entrypoint-initdb.d:ro
      healthcheck:
        test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER} -d $${POSTGRES_DB} -h 127.0.0.1"] 
        interval: 10s
        timeout: 5s
        retries: 5
      restart: unless-stopped 

    webapp:
      image: ghcr.io/you/your-web:${WEB_APP_TAG}
      user: node # dockerfile base image must have this user OR dockerfile must build this user OR compose file must omit this
      depends_on:
        db:
          condition: service_healthy
      ports:
        - "${WEB_APP_HOST_PORT}:${WEB_APP_CONTAINER_PORT}"
      environment:
        PORT: ${WEB_APP_CONTAINER_PORT}
        POSTGRES_HOST: ${POSTGRES_HOST}
        POSTGRES_PORT: ${POSTGRES_PORT}
        POSTGRES_USER: ${POSTGRES_USER}
        POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
        POSTGRES_DB: ${POSTGRES_DB}
      restart: unless-stopped

    db_backup:
      image: postgres:16
      command: sh -c 'pg_dump -F c -f "/db_backups/$${PGDATABASE}_$(date +%F_%H%M).dump"'
      profiles: ["backup"]
      depends_on:
        db: 
          condition: service_healthy
      environment:
        PGHOST: ${POSTGRES_HOST} # Must use PG* env variable format for pgdump (for libpq and other postgres client tools)
        PGPORT: ${POSTGRES_PORT} # Must use PG* env variable format for pgdump (for libpq and other postgres client tools)
        PGUSER: ${POSTGRES_USER} # Must use PG* env variable format for pgdump (for libpq and other postgres client tools)
        PGPASSWORD: ${POSTGRES_PASSWORD} # Must use PG* env variable format for pgdump (for libpq and other postgres client tools)
        PGDATABASE: ${POSTGRES_DB} # Must use PG* env variable format for pgdump (for libpq and other postgres client tools)
      volumes:
        - ./db/backups:/db_backups
      
  volumes:
    db_data: {}

```

### ENV.PROD
```bash

DOPPLER_TOKEN= # Doppler secrets manager ACCESS TOKEN
COMPOSE_PROJECT_NAME=myapp_prod

WEB_APP_DOCKER_IMAGE_TAG= # image tag from docker hub for your 
WEB_APP_HOST_PORT=3000 
WEB_APP_CONTAINER_PORT=3000

POSTGRES_HOST=db # service name for docker compose
POSTGRES_PORT=5432
# POSTGRES_USER, POSTGRES_PASSWORD, POSTGRES_DB should be set in secrets manager (doppler)

```