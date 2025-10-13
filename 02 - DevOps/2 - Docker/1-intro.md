## Virtualization (Key Concept)
- When you reserve a portion of system resources (cpu cores, memory, i/o, etc.) to compose a virtual machine/system and you run an operating system on that virtual machine
- A "Hypervisor" is software that manages the lifecycle of each virtual machine (vmware, for example).
- Differs from containerization

## Containerization (Key Concept)
- When you create a lightweight environment that contains process' with a new root and possibly kernel features, like limiting the resources that the contained process' take up.
- The contained process' cannot exist outside of the container and therefore cannot interact with anything outside of the container

## Containerization vs Virtualization
- Virtualization runs an entirely unique operating system on the virtual machine
- Containerization shares the same operating system
  
## What Is Docker?
- Docker manages the lifecycles of containers, similar to a "hypervisor" for virtualization
- Docker uses a `dockerfile` to build an image, and images create the containerized runtime of your app
- Docker can also use a `docker-compose.yml` file to orchestrate/coordinate multiple containerized services that compose your app

## What is a Docker Image?
- An immutable, executable image that can be run by docker engine to create a runtime for your containerized app
- Updates require an entirely new image, so you must build a new one and provide a new image tag/version
- 

## Dockerhub And Other Image Registries
- Image registries/repos are used to distribute docker images from machine to machine
- From your dev environment, you build the docker image and upload it to dockerhub
- In your Staging and/or Production environment, you execute the image by referencing the image tag (docker will determine if it has the correct image, and download it if it doesn't)
- DockerHub is a paid for image registry, Github Container Registry (GHCR)


## Docker Commands
- view images on local machine: `docker images`
- view images being used by containers: `docker ps` or `docker ps -a` (includes non-running containers)
- build an image (locally):`docker build -t ghcr.io/<your-org-or-username>/<image-name>:<tag> .`
- remove an image (locally): `docker rmi <image:tag>` or `docker rmi <image-id> <image-id-2>` (multiple)
- start a container:
- stop a container: `docker stop <container>`
- remove a container: `docker rm <container>`
- remove "dangling" containers: `docker image prune` (safe)
- remove all unused containers: `docker image prune -a` (agressive)
- push docker image to ghcr: `docker push ghcr.io/<gitusername>/<imageName>:<tag>`

## Docker Compose Commands
- `docker compose --env-file .env.staging -f docker-compose.yml up -d`