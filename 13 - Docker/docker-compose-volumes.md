## Docker Compose Volumes
- A property that allows persistant storage outside of the container's writable layer
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

### Named-Mounting Volumes
- When defining volumes in docker-compose, each volume follows this format: `<volumme_name>:<container_path>`
- This is similar to port mapping. The host machine has a directory for the named volume, and it is linked to the path the container thinks it is reading/writing to. The container tells the docker engine, write blank to the container_path. The docker engine writes the data to the true storage location, which is derived from the volume_name
- For example, if container writes to `/var/lib/postgresql/data`, docker engine writes to `/var/lib/docker/volumes/<volume_name>/_data`

### Bind-Mounted Volumes



### Example Of DB Service Using Volumes For Data Persistance and DB Initialization
```yml
db:
  volumes:
    - ./db-init:/docker-entrypoint-initdb.d # bind mounted volume (db init / seed)
    - pgdata:/var/lib/postgresql/data  # name mounted volume (actual database data)
```