⚙️ Phase 5: Docker Compose

🎯 Objectives

- By the end of this phase, you should:
- Understand what Docker Compose is and why it’s used.
- Learn YAML syntax and structure of docker-compose.yml.
- Run multi-container applications with a single command.
- Use environment variables, networks, and volumes in Compose.
- Scale services using Compose.
- Explore overrides and environment-specific configurations.

---

📘 Concepts

🔹 What is Docker Compose?
- A tool for defining and running multi-container apps.
- Config defined in docker-compose.yml.
- One command → builds, runs, and connects containers.

---

🔹 Compose File Structure

- Common sections in docker-compose.yml:
  - version: → file format (v3 is common).
  - services: → define containers.
  - image: or build: → specify image.
  - ports: → port mapping.
  - volumes: → persistent storage.
  - environment: → env vars.
  - depends_on: → service dependencies.
  - networks: → network configuration.

---

🔹 Running Compose

- Start services:
```bash
docker-compose up
```

- Start background: 
```bash
docker-compose up -d
```

- Stop:
```bash
docker-compose down
```

---

🔹 Environment Variables

- Use .env file to keep configs.
- Example .env:
```bash
DB_USER=root
DB_PASS=secret
```
- Refer in docker-compose.yml:
```bash
environment:
  - MYSQL_USER=${DB_USER}
  - MYSQL_PASSWORD=${DB_PASS}
```
---

🔹 Scaling

- Run multiple instances of a service:
```bash
docker-compose up --scale web=3
```
---

🔹 Overrides
- Compose supports multiple files (e.g., docker-compose.override.yml)
- Useful for dev vs prod configs.

---

🧪 Examples (3 Levels)

✅ Beginner

Simple Web + DB app
docker-compose.yml:
```bash
version: "3.9"
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: example
```
👉 Run with:
```bash
docker-compose up -d
```

✅ Intermediate

Node.js + MongoDB app with volumes
```bash
version: "3.9"
services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - mongo
  mongo:
    image: mongo
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```

✅ Advanced

Compose with networks, env vars, and scaling
```bash
version: "3.9"
services:
  frontend:
    build: ./frontend
    ports:
      - "8080:80"
    depends_on:
      - backend
    networks:
      - appnet

  backend:
    build: ./backend
    environment:
      - DB_HOST=db
    networks:
      - appnet

  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db_data:/var/lib/postgresql/data
    networks:
      - appnet

volumes:
  db_data:

networks:
  appnet:
```
👉 Scale backend:
```bash
docker-compose up --scale backend=3
```

---

💼 Mini Project: Full-Stack To-Do App

- 1.Frontend: React app (served with nginx).
- 2.Backend: Node.js/Express.
- 3.Database: MongoDB with named volume.
- 4.Define all in one docker-compose.yml.
- 5.Start with docker-compose up -d.
👉 Visit frontend → confirm CRUD works through backend → data persists in Mongo.

---

🧠 Interview Tips

> Q: How is Docker Compose different from Kubernetes?
- A: Compose = single-host, simple orchestrator. K8s = multi-host, advanced orchestrator.

> Q: Difference between depends_on and health checks?
- A: depends_on controls startup order but doesn’t wait for readiness; need health checks or wait-for scripts.

> Q: How do you persist data in Compose?
- A: Use volumes: section with named volumes.

> Q: Can Compose define multiple networks?
- A: Yes, you can define multiple custom networks and connect services accordingly.

> Q: How do you scale a service in Compose?
- A: docker-compose up --scale <service>=N.

---

🪄 Hidden Gems

- Use profiles: in Compose to enable/disable services.
- Override Compose files (docker-compose -f file1 -f file2 up).
- docker-compose logs -f → live logs across all services.
- Compose v2 is now a plugin (docker compose instead of docker-compose).

---

🏠 Homework (5 Tasks)

- 1.Create a 2-service Compose file (nginx + mysql).
- 2.Add .env file for database credentials.
- 3.Scale a service to 3 replicas and test load balancing.
- 4.Use named volumes in Compose and confirm persistence.
- 5.Override configs with docker-compose.override.yml for dev vs prod.

---

## ⚙️ Phase 5 – Missing Pieces / Deep Dives

🔹 1. Compose File Versions
- version: "3" is most common today.
- Newer Docker supports Compose Spec → works with v2 & v3 unified.
👉 Trick question: “Which Compose version should you use?” → Best answer: Use the latest Compose Spec unless restricted by old Docker Engine.

---

🔹 2. Restart Policies
- To keep services running even if they fail/restart:
```bash
restart: always
restart: unless-stopped
restart: on-failure
```
👉 Interview trap: Compose doesn’t handle node failures like Kubernetes.

---

🔹 3. Healthchecks
- Built-in readiness probes:
```bash 
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
  interval: 30s
  retries: 3
```
- Critical because depends_on does not wait for service readiness, only startup.

---

🔹 4. Build Options
- Compose can build images on the fly:
```bash
build:
  context: ./app
  dockerfile: Dockerfile.dev
  args:
    NODE_ENV: development
```
👉 Useful for passing build-time args.

---

🔹 5. Logging Configuration
- Each service can define logging drivers:
```bash
logging:
  driver: "json-file"
  options:
    max-size: "10m"
    max-file: "3"
```
👉 Avoids disk bloat in long-running Compose setups.

---

🔹 6. Resource Limits
- Control CPU & memory for services:
```bash
deploy:
  resources:
    limits:
      cpus: "0.5"
      memory: "512M"
```
👉 Note: deploy section works fully in Swarm, partially in plain Compose.

---

🔹 7. Secrets & Configs
- Compose supports secure configs (Compose v3+):
```bash
secrets:
  db_password:
    file: ./db_password.txt
```
👉 More common in Swarm, but worth knowing.

---

🔹 8. Networking in Depth
- Compose auto-creates a default network for all services.
- You can define multiple:
```bash
networks:
  frontend:
  backend:
```
- Attach services selectively for isolation.

---

🧠 Extra Interview Q&A

> Q: Does depends_on wait until a service is fully ready?
- A: No. It only controls startup order. Use healthchecks or wait-for scripts.

> Q: How are environment variables passed in Compose?
- A: Either inline under environment: or from a .env file.

> Q: What happens if you don’t define networks in Compose?
- A: Docker creates a default network and connects all services to it.

> Q: Can you scale a service that uses a local volume?
- A: Generally no, because local volumes can’t be shared across replicas (use external storage).

> Q: How do you persist DB data in Compose?
- A: Define a named volume under volumes: and mount it to the DB container.

> Q: Difference between docker run -p and ports: in Compose?
- A: Both publish container ports to host, but Compose handles it declaratively in YAML.

> Q: What happens to volumes/networks when you run docker-compose down?
- A: By default, named volumes persist. Use --volumes flag to remove them.



