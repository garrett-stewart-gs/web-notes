## What Is A Dockerfile?
  - It is a blueprint for building a docker image
  - Must be named: "Dockerfile" (no file extension, no other variations)
  - It essentially dictates the properties of the docker container and what it runs inside of it

## DockerFile Keywords:
  - FROM:
    - Tells docker which image on dockerhub will be used for building the new image for your project
    - It specifies the starting point of the new image
    - Always used on the 1st line of a docker file, and subsequent lines are used to add on to the base image in creating a new image
  - ENV:
    - Allows you to inititate env variables inside the docker file  
    - Usually better to define them externally or in the docker-compose file, so that you do not need to rebuild the image when changing the env variables
  - RUN: 
    - Used to specify commands that run when building the docker image
    - these commands only execute in the container, and they do not affect the host machine
    - You can have multiple RUN commands used in your docker file, unlike CMD
  - COPY: 
    - Allows you to copy files from the host machine INTO the container.
    - Similar commands could be created using RUN, except it COPY executes on the host machine, not inside the container!
  - CMD: 
    - Executes and entry point linux command
    - You can only have 1 CMD command in any dockerfile
  - ENTRYPOINT:
    - 
  - WORKDIR: 
    - 
  - USER:
    - Sets the user to the given argument. The argument must be a user that exists
    - You usually create a user/group with the RUN command. However, many official images already include a generic user (ie. node has a user named "node")
    - If you do not set a user, dockerfile will use the root user, which has full permissions and presents a security risk from attackers
  
  

## Dockerfile Best Practices
  - Always use an official image from dockerhub, such as node or postgres
    - Note: these official images are built up in the same way you would do it *ideally*, and it is battle-tested
  - Do not use :latest tag OR omit any tag at all. Instead use a specific image version
    - ```FROM node``` === ```FROM node:latest``` is worse than ```FROM node:17.0.1```
    - using the latest version tag can cause unpredictable bugs
    - using a specific version tag also gives your team transparency to what specific version is used in the container environment/project
  - Choose an official image variation suits your project, leaner images are superior to larger images
    - There are variants with different OS's, minified installations, etc. 
    - Smaller images with less stuff also has fewer security vulnerabilities
    - for example: ```FROM node:17.0.1``` is worse than ```FROM node:17.0.1-alpine```, if the alpine version has everything you need
  - Optimize Caching Image Layers By **Ordering Image Layers/Commands From Least Frequently Changed to Most Frequently Changed**
    - Each command in the dockerfile creates an image layer
    - If you update an image via the dockerfile, the cached layers will not need to be rebuilt, and any layer that was changed, or is 'downstream' of a layer that was changed, will need to be rebuilt
    - So, the layers that are subject to change should appear later in the dockerfile to optimize caching and speed up subsequent image builds
    - You can also split up commands, such as copying the package.json & package-lock.json, running npm install, AND THEN copying the nodejs app (which has the updated code). this would work because dependencies change less than the code you are working on!
    - Make sure you create a dedicated user/group and set required permissions
    - Use Multi-stage builds to separate the build stage and the runtime stage
      - This allows you to use the build dependencies, but exclude them in the final image (reducing final image size)
    - Scan your image for security vulnerabilities using ```DOCKER SCAN <imageName>:<imageTag>```
      - Note: You must be signed in to dockerhub in your CLI to do so


### Dockerfile Multi-Stage Builds
- It is common to use the following stages:
  - Base - Defines the OS & software used in the base container environment, such as Ubuntu Linux, nodejs or postgresql
  - Deps - installs the dependencies (early definition = good caching)
  - Builder - copies source code and builds project files into stand alone output
  - Runner - the runtime image

## Example Dockerfile
  ```dockerfile

### BASE STAGE ###
FROM node:24-bookworm-slim AS base

# Runtime defaults
ENV NODE_ENV=production \
    NEXT_TELEMETRY_DISABLED=1 \
    NPM_CONFIG_AUDIT=false \
    NPM_CONFIG_FUND=false \
    NPM_CONFIG_LOGLEVEL=warn

# Working dir (created if missing)
WORKDIR /app

# Give non-root 'node' user ownership so it can write as needed
RUN chown node:node /app

# Run as non-root
USER node
### END BASE ###


### DEPS STAGE ###
FROM base AS deps

# Install devDeps for building
ENV NODE_ENV=development \
    NPM_CONFIG_CACHE=/home/node/.npm

RUN mkdir -p /home/node/.npm

# Maximize cache: copy only manifests first
COPY --chown=node:node package.json package-lock.json ./

# Reproducible install; cache npm artifacts between builds
# RUN --mount=type=cache,id=npm-cache,target=/home/node/.npm \
#     npm ci

RUN --mount=type=cache,id=npm-cache-node,target=/home/node/.npm,uid=1000,gid=1000 \
    npm ci

### END DEPS ###


### BUILDER STAGE ###
FROM base AS builder

# Cache for Next builds (optional but speeds CI)
ENV NEXT_CACHE_DIR=/tmp/next-cache
RUN mkdir -p /tmp/next-cache

# Bring in deps installed in the deps stage
COPY --chown=node:node --from=deps /app/node_modules ./node_modules

# Bring in app source (ensure .dockerignore excludes node_modules, .next, .git, .env*)
COPY --chown=node:node . .

# Build Next.js; keep its cache hot between builds
RUN --mount=type=cache,id=next-cache,target=/tmp/next-cache \
    npm run build

# Trim devDependencies after build to slim down what the runner copies
RUN npm prune --omit=dev
### END BUILDER ###


### RUNNER STAGE ###
FROM base AS runner

# Copy only what's needed to run
COPY --chown=node:node --from=builder /app/package.json ./package.json
COPY --chown=node:node --from=builder /app/.next ./.next
COPY --chown=node:node --from=builder /app/node_modules ./node_modules
# If you don't have /public, remove the next line
COPY --chown=node:node --from=builder /app/public ./public

# No EXPOSE/PORT here—docker-compose will inject PORT and map host:container
CMD ["npm", "run", "start"]
### END RUNNER ###

  ```



  - what is docker cmd and entrypoint?
  - how to configure dockerfile to have container start when linux starts?

## Project Builds & DockerFile
  - The Dockerfile itself should build the project (ie. npm run build) for production
  - You should only manually build 