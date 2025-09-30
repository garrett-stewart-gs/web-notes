## Docker CLI Commands
- `docker build -t <imageName>:<versionNumber/Tag> <directory>`
- `docker run <imageName>:<versionNumber/Tag>`


 


## Docker-Compose CLI Commands
- `docker compose up`
- `docker compose down`
- `docker compose start`
- `docker compose stop`

## Advanced Docker-Compose CLI Commands
- `docker compose --env-file .env.dev -f docker-compose.dev.yml up -d`
- `docker compose --env-file .env.prod -f docker-compose.prod.yml up -d`




- a docker parameter that defines the main process of the docker container
- it runs every time the container starts, so this is where you start your app!
- the command can be configured as an array or as a string.

### Docker Compose `command`  With Dockerfile `CMD` and `ENTRYPOINT`
- `command` overrides the `CMD`parameter in a docker file
- If the image has an `ENTRYPOINT`, your `command` becomes its arguments.
- If there’s no `ENTRYPOINT`, `command` is the executable.

### `command` Forms
- exec (array) vs. shell (string)
- It is recommended that you stick to exec arrays, and only use shell strings when you need additional functionality
- **exec pros:**
  - Runs the binary directly (no shell). Predictable argv splitting; no weird quoting.
  - Better signal handling: your process is PID 1 (vs a shell wrapper); stops cleanly.
  - Safer/faster: no shell injection, no extra process.
  - Avoids YAML quoting pitfalls for spaces/flags.
- **shell pros:**
  - You can use shell goodies: &&, ||, pipes, redirection, globbing, ${VAR}.
  - Handy for one-liners (pre-start cleanup, etc.).

### Nextjs HOSTNAME
- it is important to set the hostname to 0.0.0.0
- if no hostname is provided, then nextjs will not listen to connections from all avenues
- Example:
  ```yml
  command: ["npm", "run", "start", "--", "-H", "0.0.0.0"]
  ```