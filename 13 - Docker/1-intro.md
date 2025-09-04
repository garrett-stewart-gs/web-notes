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
  - Once installed, docker can create a container from a container image
  - Container images can be uploaded and downloaded from dockerhub

## Docker Workflow
  - Create a folder with a dockerfile and any apps that should run inside the docker container
  - Edit the dockerfile to configure the container (docker-compose-yml)
  - Build a docker image file ```docker build .```
  - Execute the docker image to create the container and run the app(s): ```docker run <imageName>:latest```
  - Upload the docker image to dockerhub (or other cdn)
  - Download the docker image using the machine you want to run the dockerized app
