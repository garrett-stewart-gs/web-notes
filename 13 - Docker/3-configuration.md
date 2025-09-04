## How Docker Containers Are Configured
  - A docker container is generated from running a docker image
  - A docker image is generated 
  from a directory that contains a `dockerfile` and/or `docker-compose.yml`
  - The  should also have the project folders you want to run in the docker container
  - The directory should look something like this:
    ```
    myapp/ # docker project folder (used to generate docker image)
      .env
      docker-compose.yml
      web/        # your Next.js or static assets build, or Dockerfile
      api/        # your API code + Dockerfile
    ```



## What Is A Docker Image?
 - It is an immutable executable container image
 - You cannot change the image, you must build a new one whenever you make changes to your app

### Docker File Commands/Components
  - FROM = defines what image the docker container should use
  - RUN = runs the given commands on the image. You can pass multiple commands to 1 RUN command, and you can have multiple RUN commands in a dockerfile
  - COPY = 
  - CMD = The default command for the docker image. This will always run, unless it is overridden with the cli when the container is instantiated.


### What Is A Docker Ignore File?
  - At the container root, you can add a .dockerignore file
  - It specifies the files/folders that should be ignored, which keeps the docker image small