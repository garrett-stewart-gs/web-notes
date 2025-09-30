## What Is Docker-Compose?
  - A tool for defining multi-container docker applications
  - Each container is a "micro service" and each micro service usually depends on its sibling micro services
  - The micro services are bundled together into 1 image, so everything is shipped as 1 package
  - Configuration is managed in the `docker-compose.yml` file
  - modern naming convention: `compose.yml`
  - older naming convention: `docker-compose-yml` 

## Docker Compose Commands
  - `docker compose up`
  - `docker compose down`
  - `docker compose start`
  - `docker compose stop`

## Docker Compose File Properties Sequence (For readability)
  - image or build
  - command / entrypoint (if overridden)
  - container_name (if used)
  - profiles
  - depends_on
  - ports
  - environment
  - volumes
  - networks
  - restart, healthcheck, etc.


## Example Docker-Compose File
  ```yml
    services: # the dockerized environments that need to talk to eachother
    
      db: # name of a dockerized app (how does it know the name? file system?)
        image: postgres:16
        environment: # reads environment variables in sibling .env file
          POSTGRES_USER: ${POSTGRES_USER}
          POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
          POSTGRES_DB: ${POSTGRES_DB}
        volumes: # using volumes prevents db from being destroyed when app stops
          - db_data:/var/lib/postgresql/data # - {volumeName}:/{storagePath}
          - ./db/init:/docker-entrypoint-initdb.d:ro # sets entrypoint directory for initializing custom username/password/permissions + creating database/tables/seeding data
        healthcheck:
          test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER}"] # determines the command that is run during health check
          interval: 10s # determines the frequency that the healthcheck test repeats
          timeout: 5s
          retries: 5
        restart: unless-stopped # ensures container starts when restarts (r)

      web:
        image: ghcr.io/you/your-web:${WEB_TAG}   # pre-built image from CI/registry. your webapp repo must have a dockerfile that 
        user: "node" # avoids root-owned files on host
        depends_on:
          db: # ensures that the db container is running before the web service starts (requires db service to have healthcheck)
            condition: service_healthy 
        ports:
          - "${WEB_HOST_PORT}:${WEB_CONTAINER_PORT}" # configures linux server port to direct traffic to the container port that is running the relavent service
        environment:
          PORT: ${WEB_CONTAINER_PORT} # nextjs uses PORT env variable by default
          POSTGRES_HOST: db
          POSTGRES_PORT: 5432
          POSTGRES_USER: ${POSTGRES_USER} # postgres has "postgres" username by default, but it is recommended that you register a unique username in postgres
          POSTGRES_PASSWORD: ${POSTGRES_PASSWORD} 
          POSTGRES_DB: ${POSTGRES_DB}
        networks: # defines which docker networks this service connects to (optional, but must be defined below in order to use)
          - backend
          - frontend
        restart: always # ensures container starts when restarts (r)

      db_backup:
        image: postgres:16
        command: sh -c 'pg_dump -F c -f "/db_backups/$${PGDATABASE}_$(date +%F_%H%M).dump"'
        profiles: ["backup"] # used to control which services run from docker compose up --profile <profile> command
        depends_on: 
          db:
            condition: service_healthy
        environment: # Must use PG* env variable format for pgdump (for libpq and other postgres client tools)
          PGHOST: ${POSTGRES_HOST} # must match database service name (db, in this case)
          PGPORT: ${POSTGRES_PORT} # should be 5432, but env variable gives option of changing it
          PGUSER: ${POSTGRES_USER} # if not set, defaults to "postgres" which is not ideal for security
          PGPASSWORD: ${POSTGRES_PASSWORD} 
          PGDATABASE: ${POSTGRES_DB} 
        volumes:
          - db_backups:/db_backups
        networks: # defines which docker networks this service connects to (optional, but must be defined below in order to use)
          - backend
          - frontend

    volumes: # where volume names are defined
      db_data: {}
      db_backups: {} 

    networks: # where docker networks are defined (optional)
      frontend:
      backend: 
  ```
