


### Example of Docker-Compose Ports Configuration
```yml
  services:
    web:
      build: .
      environment:
        PORT: 3000 # this port env variable is injected into nextjs
      command: ["npm","run","start", "--", "-H", "0.0.0.0"]
      ports:
        - "8080:3000"   # host:container. you can use .env variables for these too.
```