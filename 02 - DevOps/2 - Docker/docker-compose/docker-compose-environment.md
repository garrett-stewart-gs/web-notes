## Docker Compose Environment Parameter
- It injects environment variables into the container’s process environment during startup.
- It overrides the .env variables that exist in a service's .env file (parent .env wins)
- These variables live only inside the container (local scope to that service).
- You can hard-code values, or reference values from your host/.env file with ${VAR} substitution.

### Environment Parameter Example
```yml
environment:
PORT: 3000        # hard-coded
DB_USER: ${USER}  # pulled from your host or .env file
```

### Specific Environment Variables (Official Images)
- many images require a specific variable name
  - POSTGRESQL:
    - `POSTGRES_USER` (required)
    - `POSTGRES_PASSWORD` (required)
    - `POSTGRES_DB` (required)
    - There are no built-in env variables for POSTGRES_PORT (defaults to 5432) or POSTGRES_HOST (use the service name of the database container)
  - NODE:
    - `PORT` (optional)
    - `POSTGRES_HOST` (required, if node app is connecting to a db. you will need to add the code that processes this variable)
    - `POSTGRES_PORT` (required, if node app is connecting to a db. you will need to add the code that processes this variable)
    - `POSTGRES_USER` (required, if node app is connecting to a db. you will need to add the code that processes this variable)
    - `POSTGRES_PASSWORD` (required, if node app is connecting to a db. you will need to add the code that processes this variable)
    - `POSTGRES_DB` (required, if node app is connecting to a db. you will need to add the code that processes this variable)


### Postgres ENV Variables: db Service vs. web Service
- db Service uses POSTGRES_* naming convention (comes from the standard postgres docker image)
- web Service uses PG* naming convention (comes from libpq env variables like psql and pg_isready)






