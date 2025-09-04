## Docker Compose Environment Parameter
- It injects environment variables into the container’s process environment at runtime.
- It overrides the .env variables that exist in a service's .env file (parent .env wins)
- These variables live only inside the container (local scope to that service).
- You can hard-code values, or reference values from your host/.env file with ${VAR} substitution.

### Environment Parameter Example
```yml
environment:
PORT: 3000        # hard-coded
DB_USER: ${USER}  # pulled from your host or .env file
```

### Postgres ENV Variables: db Service vs. web Service
- db Service uses POSTGRES_* naming convention (comes from the standard postgres docker image)
- web Service uses PG* naming convention (comes from libpq env variables like psql and pg_isready)





- db service env variables are used first time only? (during container init?)
- how does the database persist if the docker container crashes/restarts (volumes)
- how does the docker container know the db service has been initialized and doesn't get recreated?
- volumes can be configured to initialize postgres db, user, tables, and data. How so? how does this separate from maintaining the data?