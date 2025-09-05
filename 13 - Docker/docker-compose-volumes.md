## Docker Compose Volumes
- Volumes are a property that allows persistant storage outside of the container's writable layer
- Usecases:
  - data persistance across container restarts/rebuilds (without it, all data in the container is lost when it restarts/crashes/etc)
  - makes files available between containers or between host and container.
  - initialization/seeding

### Persisting Postgres Data
- Postgres stores everything in `/var/lib/postgresql/data`
- when docker uses volumes, it mounts an external storage location at the specified file directory
- in actuality, the docker engine has access to the host machine, and the container does not. The docker engine mediates the changes that need to be made in the external storage location.
- the container only sees `/var/lib/postgresql/data`, the host machine has the files stored in `/var/lib/docker/volumes/{volumeName}/_data/`

### Initialization And Seeding A Dockerized Database (optional)
- Optionally, you can create an initialization volume to seed a database
- the initiallization volume should be: `- ./db-init:/docker-entrypoint-initdb.d`
- The initialization only runs if the external storage location/directory generated/defined in volumes is empty
- in short, the initialization literally only runs 1 time, regardless of how many lifecycles have occured for the docker container

### Named Volumes (docker-managed storage)
- When defining named volumes in docker-compose, each volume follows this format: `<volumme_name>:<container_path>`
- This is similar to port mapping. The host machine has a directory for the named volume, and it is linked to the path the container thinks it is reading/writing to. The container tells the docker engine, write blank to the container_path. The docker engine writes the data to the true storage location, which is derived from the volume_name
- For example, if container writes to `/var/lib/postgresql/data`, docker engine writes to `/var/lib/docker/volumes/<volume_name>/_data`
- Named Volumes Pros:
  - portable/host-agnostic (due to no hard-coded paths)
  - safer dbs (due to storage being nested deeper and less likely to be edited by anything but docker)
  - easier to share between services (each service/container can reference the volume name, rather than a complicated path)
- Named Volumes Cons:
  - Need to declare volume name in top-level volumes property
  - harder to browse/copy (must dig deeper. Also, it is risky, because docker could be using these files. Best to stick to read-only operations if digging into docker-managed directories)

### Bind-Mounted Volumes
- When bind mounting volumes in docker-compose, each volume follows this format: `./<host_path>:<container_path>`
- Bind-mounted Volumes Pros:
  - immediate visibility in your project (great for logs/dumps)
  - easier to copy/rsync/gitignore
  - Using bind mounted volues does not require that you define volumes at the top-level (unlike named volumes)
- Bind-mounted Volumes Cons:
  - host-path quirks/permissions across various machines/OSes
  - risky for DB (editing can corrupt data)

### Mounting Volumes For Multiple Services
- You can also mount 1 volume in multiple places
- this can be done by declaring a top-level volume, and referencing it in the desired services
- **Use-Cases:**
  - Backups/inspection: mount the DB’s data volume read-only in a helper container.
  - Shared assets: e.g., an uploads folder read/write by the app; read-only by a worker or CDN sync job.
  - Unix sockets: Nginx ↔ app via a shared socket path.
  - Build/cache: share a cache volume between build and runtime containers.
  - Logs/artifacts: one service writes, another tails/ships (reader should use :ro).

### Example of Mounting Volume In Multiple Places

```yml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: ${PGUSER}
      POSTGRES_PASSWORD: ${PGPASSWORD}
      POSTGRES_DB: ${PGDATABASE}
    volumes:
      - db:/var/lib/postgresql/data # postgresql stores in /var/lib/postgresql/data by default.

  db_backup:
    image: postgres:16
    profiles: ["backup"] # ensures the db_backup container will not run, unless it is directed to start via manual entry or cron job (or similar tool)
    depends_on: 
      - db
    environment:
      PGHOST: db # named db to match the service named db (backup process knows to connect because of this line)
      PGPORT: ${PGPORT}
      PGUSER: ${PGUSER} 
      PGPASSWORD: ${PGPASSWORD}
      PGDATABASE: ${PGDATABASE}
    volumes:
      - db_backups:/db_backups # host folder for dump files
    command: >
      sh -eu -c '
        until pg_isready -h "$PGHOST" -p "$PGPORT" -U "$PGUSER" -t 2 >/dev/null; do
          echo "Waiting for Postgres..."; sleep 2;
        done;
        pg_dump -Fc -f /db_backups/${PGDATABASE}_$(date +%F_%H%M).dump
      '

volumes: # named volume definitions (necessary if services are referencing named volumes)
  db: {}
  db_backups: {}
```

### Using Data Dumps In Your Back Up Database Volume
- using a shell command: pg_dump creates a full point-in-time logical back up of the database (all or nothing) at the point the dump is triggered
- pg_dump files contain schema + data
- Breakdown of the command `sh -c "pg_dump -Fc -f /db_backups/${PGDATABASE}_$(date +%F_%H%M).dump"`
  - `sh` opens shell 
  - `c` specifies that a command string is to follow (also necessary for date variable in command string)
  - pg_dump `-Fc` tag creates custom format (compact and supports parallel store)
  - pg_dump `-f` tag tells pg_dump you are about to specify a filepath for the dump
  - `$(date +%F_%H%M)` inserts a timestamp into the backup.dump filename:
    - `%F` = `YYYY-MM-DD`
    - `%H%M` = hour/minute
  - Output Filename = `database_name_YYYY-MM-DD_HHMM.dump`
- Based on `volumes: - db_backup:/db_backups`, the backups will be found on the linux host machine at: `/var/lib/docker/volumes/db_backup/_data`


### Docker Volume Pruning
- 