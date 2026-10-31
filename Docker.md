# Docker

## Install

- [Docker Desktop](https://www.docker.com/products/docker-desktop)

## Local

- **Run:** docker run -p PORT:CONTAINER_PORT -d --name CONTAINER IMAGE

- **Docker Compose:** docker compose up --build -d

## Build and Push Image

1. docker build . --file FILE --tag REGISTRY/PROJECT/IMAGE

2. docker tag REGISTRY/PROJECT/IMAGE REGISTRY/PROJECT/IMAGE:VERSION

3. docker login https://REGISTRY

4. docker push --all-tags REGISTRY/PROJECT/IMAGE

## Examples

### application.mjs

```mjs
"use strict";
import { createServer } from "http";
const server = createServer((request, response) => response.end("Success"));
server.listen(5000, () => console.log("Running: http://localhost:5000"));
```

### dockerfile

```dockerfile
FROM node:alpine
WORKDIR /application
COPY . .
CMD ["node", "application.mjs"]
```

### docker-compose.yaml

```yaml
services:
  SERVICE:
    image: IMAGE
    container_name: CONTAINER
    restart: always
    build:
      context: .
      dockerfile: dockerfile
    networks:
      - network
    ports:
      - 80:80
networks:
  network:
```
