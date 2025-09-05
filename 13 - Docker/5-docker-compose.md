## What Is Docker-Compose?
  - An advanced method for configuring docker containers and bundling containers together
  - useful for multi-container apps
  - modern naming convention: `compose.yml`
  - older naming convention: `docker-compose-yml` 


## Example Docker-Compose File
  ```yml
    services: # the dockerized environments that need to talk to eachother
    
      db: # name of a dockerized app (how does it know the name? file system?)
        image: postgres:16
        environment: # reads environment variables in sibling .env file
          POSTGRES_USER: ${PGUSER}
          POSTGRES_PASSWORD: ${PGPASSWORD}
          POSTGRES_DB: ${PGDATABASE}
        volumes: # using volumes prevents db from being destroyed when app stops
          - ${PGDATABASE}:/var/lib/postgresql/data # - {volumeName}:/{storagePath}
        healthcheck:
          test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER}"] # determines the command that is run during health check
          interval: 10s # determines the frequency that the healthcheck test repeats
          timeout: 5s
          retries: 5
        restart: always # ensures container starts when restarts (r)

      web:
        build: . # builds the container from a docker file in the current directory (. = cd)
        environment:
          PORT: ${WEB_CONTAINER_PORT} # nextjs uses PORT env variable by default
          PGHOST: db # named db to match the service named db (dockerized containers can reach eachother with this reference)
          PGPORT: ${PGPORT}
          PGUSER: ${PGUSER} # postgres has "postgres" username by default, but it is recommended that you register a unique username in postgres
          PGPASSWORD: ${PGPASSWORD} 
          PGDATABASE: ${PGDATABASE}
        command: ["npm", "run", "start", "--", "-H", "0.0.0.0"] # exec array (cleaner execution than shell string)
        ports:
          - "${WEB_HOST_PORT}:${WEB_CONTAINER_PORT}" # configures linux server port to direct traffic to the container port that is running the relavent service
        depends_on:
          - db # ensures that the db container starts first, increasing the chance that the db is operational first. You still need to assume a race condition will occur, and your apps that depend on db need to poll the database until the connection is established
        restart: always # ensures container starts when restarts (r)

      db_backup:
        image: postgres:16
        profiles: ["backup"]
        depends_on: 
          - db 
        environment: 
          PGHOST: db # named db to match the service named db (this is how it knows to connect to the correct database)
          PGPORT: ${PGPORT}
          PGUSER: ${PGUSER} # postgres has "postgres" username by default, but it is recommended that you register a unique username in postgres
          PGPASSWORD: ${PGPASSWORD} 
          PGDATABASE: ${PGDATABASE}
        volumes:
          - ${PGDATABASE}_backups:/db_backups
        command: >
          sh -eu -c '
            until pg_isready -h "$PGHOST" -p "$PGPORT" -U "$PGUSER" -t 2 >/dev/null; do
              echo "Waiting for Postgres..."; sleep 2;
            done;
            pg_dump -Fc -f /db_backups/${PGDATABASE}_$(date +%F_%H%M).dump
          '

    volumes: # where volume names are defined
      ${PGDATABASE}: {}
      ${PGDATABASE}_backups: {} 
  ```
