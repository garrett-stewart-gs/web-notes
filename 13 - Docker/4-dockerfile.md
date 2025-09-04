## What Is A Dockerfile?
  - It is a config file for docker 
  - More Accurately, it is a blueprint to build an image
  - It essentially dictates the properties of the docker container and what it runs inside of it


## Example Dockerfile
  ```
    FROM ubuntu:latest // tells docker to use the ubuntu image at its latest version
    RUN
    COPY
    RUN
    CMD []
  ```
  - what is docker cmd and entrypoint?
  - how to configure dockerfile to have container start when linux starts?