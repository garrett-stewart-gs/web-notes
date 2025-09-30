## How Docker Containers Are Configured
- `Dockerfile` is used to configure how the image is built
- `docker-compose.yml` file is used to compose a project that consists of multiple containers, enhancing modularization and separation of concerns
- Both files have different syntax and structure

### Dockerfile
- Dockerfile should exist inside the repo that you want to create an image from.
- It should be adjacent/sibling to the readme.md or the package.json or the node_modules folder

### Docker Compose File
- a docker configuration file that defines how an app's containerized components interoperate
- docker compose will setup a network so containers can talk to eachother
- docker compose can also specify and hook-up persistent storage on the host machine so that the container can access them (useful for database, or copying in app files for running a dev server)


### What Is A Docker Ignore File?
- a file that specifies what files/folders should be ignored during the build process
- useful for reducing image size and ensuring secrets are kept secret
- this affects what docker has access to when building an image (specifically what files it can COPY or ADD)
- context determines which dockerignore file is used, so if you have a complex container ecosystem with multiple build commands, you should explicitly define the context and the dockerfile in `docker-compose.yml`:
  - ```yml
    services: 
      web:
        build: # if you are using build in docker-compose, it is important to define context!
          context: ./apps/web
          dockerfile: 
  ```