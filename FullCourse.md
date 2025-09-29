🐳 ZERO TO HERO DOCKER COURSE — FULL 6 PHASES

---

PHASE 1: FOUNDATIONS OF DOCKER

---

Concepts:
- What is Docker, why containerization
- Containers vs VMs
- Docker architecture (Client, Daemon, Registry, Objects)
- Installation on Linux/Mac/Windows/WSL
- Running first containers

---

Examples:
1. Beginner: docker run hello-world
2. Intermediate: docker run -d -p 8080:80 nginx
3. Expert: docker run -it --env MODE=dev ubuntu bash
4. Advanced: docker run -d --restart=always --name redis redis
5. Pro: docker exec -it redis redis-cli

---

Lab Exercises:
- Basic: Install Docker and run hello-world
- Intermediate: Run Nginx, check logs, exec into it
- Advanced: Serve custom index.html via bind mount

---

Cheat Sheet:
```bash
docker --version
docker info
docker run hello-world
docker ps -a
docker stop <name>
docker rm <name>
docker logs <name>
docker exec -it <name> bash
docker system prune -f
```

---

Hidden Gems:
- docker exec vs docker run difference
- --restart policies for resiliency
- Docker CLI talks to daemon over /var/run/docker.sock

---

Interview Quickfire:
- Difference between containers and VMs
- Explain Docker architecture
- Why DevOps pipelines prefer Docker

---

PHASE 2: CORE CONCEPTS

---

Concepts:
- Docker Images (immutability, layers, caching)
- Containers lifecycle
- Storage: Bind mounts vs Volumes
- Networking: bridge, host, none, user-defined

---

Examples:
1. Beginner: docker images, docker ps -a
2. Intermediate: docker run -d -v myvol:/var/lib/mysql mysql
3. Expert: docker network create appnet; run API+DB on it
4. Advanced: docker history ubuntu:22.04
5. Pro: docker run -d -p 5000:5000 -v $(pwd):/app python:3.12

---
Lab Exercises:
- Basic: Run Nginx, inspect logs, exec in
- Intermediate: Run Postgres with named volume
- Advanced: Run Python client + Postgres on custom bridge network

---
Cheat Sheet:
```bash
docker images
docker ps -a
docker rm <container>
docker rmi <image>
docker volume create myvol
docker volume ls
docker volume inspect myvol
docker network create appnet
docker run --network appnet ...
```

---

Hidden Gems:
- docker pause/unpause
- docker system df shows disk usage
- Volumes persist even if container is removed
- User-defined bridge DNS resolution
- Bind mounts fragile in production

---

Interview Quickfire:
- Volumes vs bind mounts
- Container lifecycle
- How Docker caches image layers
- Networking between containers

---

PHASE 3: DOCKERFILE MASTERY

---

Concepts:
- Core instructions: FROM, RUN, COPY, CMD, ENTRYPOINT
- Config: WORKDIR, ARG, ENV, .dockerignore, EXPOSE
- Advanced: USER, HEALTHCHECK, ONBUILD, STOPSIGNAL, SHELL, VOLUME
- Multi-stage builds
- Base images: alpine, distroless, scratch
- Best practices: lean, secure, reproducible

---

Examples:
1. Beginner: Python Flask Dockerfile with CMD
2. Intermediate: Add .dockerignore, WORKDIR, ENV
3. Advanced: Multi-stage build for Go app
4. Expert: HEALTHCHECK instruction
5. Pro: Non-root USER + distroless

---

Lab Exercises:
- Basic: Dockerize Flask app
- Intermediate: Add .dockerignore and reduce context size
- Advanced: Multi-stage Go app and compare image sizes
- Expert: Add HEALTHCHECK and USER for hardening

---

Cheat Sheet:
```bash
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python","app.py"]

docker build -t myapp:1.0 .
docker run -d -p 5000:5000 myapp:1.0
docker history myapp:1.0
docker image prune -f
```

---

Hidden Gems:
- CMD vs ENTRYPOINT difference
- COPY vs ADD best practice
- Multi-stage builds shrink image drastically
- .dockerignore reduces build context
- Distroless removes attack surface

---

Interview Quickfire:
- CMD vs ENTRYPOINT with examples
- COPY vs ADD
- Explain multi-stage builds
- How to secure Dockerfile
- Why use non-root user

---

PHASE 4: DOCKER COMPOSE BASICS

---

Concepts:
- Purpose: orchestrate multi-container apps
- YAML structure: version, services, volumes, networks
- Service to service communication via DNS
- Lifecycle: up, down, ps, logs

---

Examples:
1. Beginner: Single-service Nginx
2. Intermediate: Flask API + Postgres
3. Advanced: Add named volume for Postgres
4. Expert: Add env vars via .env file
5. Pro: Inspect default network, container DNS

---

Lab Exercises:
- Basic: Nginx on port 8080
- Intermediate: Flask API + Postgres Compose file
- Advanced: Add named volume for DB persistence
- Expert: Use .env for credentials

---

Cheat Sheet:
```bash
docker compose up -d
docker compose down
docker compose down -v
docker compose ps
docker compose logs -f web

docker-compose.yml:
version: "3.9"
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: secret
volumes:
  dbdata:
```

---

Hidden Gems:
- docker compose down -v removes volumes
- .env auto-loaded
- Service DNS works by name
- command: overrides Dockerfile CMD

---

Interview Quickfire:
- docker run vs docker compose up
- How services connect internally
- Volumes in Compose
- Risks of down -v

---

PHASE 5: ADVANCED COMPOSE

---

Concepts:
- Override defaults with command/entrypoint
- depends_on vs healthcheck
- Scaling services with --scale
- Using .env files
- Profiles (dev/test separation)
- Compose in CI/CD pipelines
- Limitations vs Kubernetes

---

Examples:
1. Beginner: Override Nginx command
2. Intermediate: depends_on + healthcheck for DB readiness
3. Advanced: Scale API to 3 replicas
4. Expert: Use .env file for secrets
5. Pro: Profiles for dev/test

---

Lab Exercises:
- Basic: command override for Nginx
- Intermediate: healthcheck for Postgres
- Advanced: Scale Flask API replicas
- Expert: .env + profiles

---

Cheat Sheet:
```bash
command: ["nginx","-g","daemon off;"]

depends_on:
  db:
    condition: service_healthy

healthcheck:
  test: ["CMD","pg_isready","-U","postgres"]
  interval: 10s
  retries: 5

docker compose up --scale api=3
docker compose --profile dev up -d
docker compose config
```

---

Hidden Gems:
- depends_on not readiness, healthcheck needed
- Scaling only for stateless services
- Override env file with --env-file custom.env
- docker compose config to validate

---

Interview Quickfire:
- Why Compose not production orchestrator
- depends_on vs healthcheck
- Scaling DB containers?
- What are profiles
- Using Compose in CI/CD

---

PHASE 6: TROUBLESHOOTING + BEST PRACTICES

---

Concepts:
- Debugging containers: exits, ports, logs
- Disk usage bloat and pruning
- Log flooding and rotation
- docker stop vs kill
- Production best practices:
  - use .dockerignore
  - pin versions
  - non-root USER
  - multi-stage builds
  - prefer volumes
  - avoid latest

---

Examples:
1. Beginner: Container exits immediately, fix CMD
2. Intermediate: Port conflict resolution
3. Advanced: Disk cleanup with system prune
4. Expert: Log rotation with log-driver
5. Pro: Debugging builds with --progress=plain

---

Lab Exercises:
- Basic: Run with wrong CMD, debug logs
- Intermediate: Simulate port conflict, resolve
- Advanced: Fill disk with images, prune
- Expert: Debug failed container with exec

---

Cheat Sheet:
```bash
docker logs <id>
docker ps -a
docker inspect <id>
docker system df
docker system prune -a -f
docker build --progress=plain --no-cache .
docker stop <id>
docker kill <id>
docker top <id>
```

---

Hidden Gems:
- docker events live stream
- docker run --rm auto-cleans
- docker top shows processes inside
- --no-cache for rebuilds

---

Interview Quickfire:
- CMD vs ENTRYPOINT
- COPY vs ADD
- Volumes vs bind mounts
- Why avoid latest
- docker system prune effects
- stop vs kill
- depends_on limitations

---

🐳 END OF DOCKER COURSE — ZERO TO HERO

---
