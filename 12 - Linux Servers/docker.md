
# Docker and Host Machine Interactions

Docker containers and their interactions with the host machine are configured in the `docker-compose.yml` file. Each project can have its own `docker-compose.yml` file to define its specific container configurations, saved in the project's root directory.

# How a Docker container runs:

  1. **Starting a Docker Container**:
      - A Docker container is created from a Docker image. 
      - To run a container, it must be explicitly started using the `docker run` command or similar commands.
      - Example: `docker run <image_name>` creates and starts a container from the specified image.
 
  2. **How A Docker Container Should Be Configured To Start On System Start**:
      - By default, a docker container must be started manually to execute its processes. 
      - If the container is stopped, it can be manually restarted using the `docker start <container_id>` command.
      - The docker container can also be configured to start automatically in the `docker-compose.yml` or docker run command
      - Example with `docker run`:
        ```bash
        docker run --restart=always <image_name>
        ```

      - Here is an example of a `docker-compose.yml` file that includes the `restart: always` setting:

        ```yaml
        services:
          web:
            restart: always
          db:
            restart: always
        ```
        This configuration ensures that both the `web` and `db` services will automatically restart if they stop or if the Docker daemon restarts.

  3. **Docker will not automatically start apps in the container, unless configured to do so**
      - When Docker starts, it runs the command(s) specified by the CMD or ENTRYPOINT
      - If no commands are specified, nothing happens.
      - 


 

## Key Features of `docker-compose.yml`

- **Service Definitions**: Define multiple services (containers) in a single file.
- **Networking**: Automatically creates a network for the defined services.
- **Volumes**: Manage persistent data by defining volumes.
- **Environment Variables**: Pass environment variables to containers.
- **Scaling**: Scale services to multiple instances with a single command.
- **Dependency Management**: Define service dependencies and startup order.
