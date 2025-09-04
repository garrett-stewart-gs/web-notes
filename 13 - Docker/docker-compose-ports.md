


### Example of Docker-Compose Ports Configuration
```yml
  services:
    web:
      build: .
      environment:
        PORT: 3000
        HOSTNAME: 0.0.0.0
      command: ["npm","run","start"]
      ports:
        - "8080:3000"   # host:container
```