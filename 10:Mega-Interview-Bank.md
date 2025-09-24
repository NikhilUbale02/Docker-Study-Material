# 🐳 Docker Mega-Interview Bank (Phase 1 → 6)

---

## 🔰 Phase 1 – Basics

> Q. What problem does Docker solve?  
- A. "Works on my machine" issue. Standardizes environments across dev, test, prod.

> Q. Can Docker run on Windows/Mac? How?  
- A. Yes, via Docker Desktop → uses lightweight VM behind the scenes.

> Q. How do containers achieve isolation?  
- A. Linux namespaces (PID, NET, IPC, MNT, UTS) + cgroups for resources.

> Q. What’s the difference between Docker Engine and Docker Desktop?  
- A. Engine = actual container runtime (Linux). Desktop = GUI wrapper for Mac/Win.

> Q. Can a container run multiple processes?  
- A. Technically yes, but best practice = one main process per container.

> Q. How do you check detailed info about Docker host?  
- A. `docker info`.

> Q. Difference between stopping and removing a container?  
- A. Stopped → data exists until removed. Remove → deletes metadata & FS (unless volumes).

> Q. What happens if you delete an image that a container uses?  
- A. Container continues running (uses cached FS). You can’t start new one.

---

## 🏗️ Phase 2 – Images & Dockerfile

> Q. What is a Docker image layer?  
- A. Immutable, cached snapshot of a file change. Stacked to form full image.

> Q. Why is caching order in Dockerfile important?  
- A. To reuse unchanged layers and avoid rebuilding whole image unnecessarily.

> Q. How do you inspect the ENV vars baked into an image?  
- A. `docker inspect <image>` → "Config.Env".

> Q. How to override CMD when running container?  
- A. `docker run image new_command`.

> Q. How to override ENTRYPOINT?  
- A. `docker run --entrypoint /bin/bash image`.

> Q. What is `.dockerignore` and why important?  
- A. Excludes files from build context (e.g., `.git`, secrets, node_modules). Prevents bloated images.

> Q. Difference between ARG and ENV?  
- A. ARG = build-time only. ENV = runtime & baked into image.

> Q. What is multi-stage build?  
- A. Separate build and runtime stages. Reduces image size by discarding build tools.

> Q. Why use Distroless images?  
- A. Even smaller than Alpine, no shell, reduced attack surface.

> Q. When would you use `FROM scratch`?  
- A. Minimal images (Go/C), for smallest footprint.

> Q. How do you view exact commands used to build image layers?  
- A. `docker history <image>`.

> Q. Can you modify a running container’s filesystem and commit changes?  
- A. Yes, `docker commit`. But bad practice → not reproducible.

---

## 💾 Phase 3 – Volumes & Persistence

> Q. How do you persist DB data in Docker?  
- A. Mount volume to `/var/lib/mysql` or `/var/lib/postgresql/data`.

> Q. Can two containers share the same volume?  
- A. Yes, volumes are sharable, useful for shared configs or DB read/writes.

> Q. When to use bind mount vs named volume?  
- A. Bind mount → dev environments (live file sync). Named volume → production (safer, portable).

> Q. How to see where a volume lives on host?  
- A. `docker volume inspect` → Mountpoint.

> Q. What’s the difference between volume prune and system prune?  
- A. Volume prune = only unused volumes. System prune = all unused images, networks, containers, and volumes (with flag).

> Q. How do tmpfs volumes differ from regular ones?  
- A. tmpfs = memory only, faster, non-persistent.

> Q. What is a data-only container?  
- A. Old pattern → container created only to hold volumes, mounted with `--volumes-from`.

> Q. How do you backup/restore a Docker volume?  
- A. Use tar inside busybox/alpine with mounted volume.

> Q. What happens if two containers write to the same volume simultaneously?  
- A. Possible conflicts. Docker doesn’t provide locking — app-level consistency required.

---

## 🌐 Phase 4 – Networking

> Q. How does Docker provide service discovery?  
- A. Via built-in DNS in custom networks (maps container name → IP).

> Q. How do you list all Docker networks?  
- A. `docker network ls`.

> Q. What’s the difference between `docker run -p` and `--network host`?  
- A. `-p` maps ports in bridge mode. Host mode shares host’s stack → no need for mapping.

> Q. Can containers on different networks talk?  
- A. No, unless connected to a common network via `docker network connect`.

> Q. How do overlay networks work?  
- A. Virtual network across multiple Docker hosts, requires Swarm/K8s.

> Q. When would you use none network mode?  
- A. For isolated containers (security, testing).

> Q. Why might DNS fail inside containers?  
- A. If not on user-defined network (default bridge lacks name resolution).

> Q. How do you capture traffic between containers?  
- A. Use `netshoot` with tcpdump or wireshark.

> Q. Can you run two containers listening on port 80?  
- A. Yes, if they use bridge mode with different host mappings (`-p 8080:80`, `-p 9090:80`).

---

## ⚙️ Phase 5 – Docker Compose

> Q. Why is Docker Compose important in CI/CD?  
- A. Encapsulates multi-service apps, repeatable, version-controlled infra.

> Q. How do you inject secrets in Compose?  
- A. Use `.env` file or secrets (Swarm/K8s integration).

> Q. What’s the difference between `docker-compose up` and `docker-compose up --build`?  
- A. `up` uses existing images. `--build` forces rebuild.

> Q. Can you run Compose without a `Dockerfile`?  
- A. Yes, if you use `image:` instead of `build:`.

> Q. What happens when you scale stateless vs stateful services in Compose?  
- A. Stateless = works fine. Stateful (like DB) = risk of data corruption without shared storage.

> Q. How do you view logs of all services at once in Compose?  
- A. `docker-compose logs -f`.

> Q. Can Compose orchestrate across multiple hosts?  
- A. No, limited to single host. For multi-host → Swarm/K8s.

---

## 🚀 Phase 6 – CI/CD & Production

> Q. How do you integrate Docker with Jenkins pipelines?  
- A. Use pipeline stages to build, test, scan, and push images to registry.

> Q. What are strategies for tagging Docker images in CI?  
- A. Semantic versions, Git SHA, branch names, multi-tags.

> Q. How do you secure Docker images in a pipeline?  
- A. Vulnerability scans, sign images, use minimal base, avoid root user.

> Q. How do you enforce only signed images in prod?  
- A. Enable Docker Content Trust (`DOCKER_CONTENT_TRUST=1`).

> Q. How do you manage Docker secrets in prod?  
- A. Use Swarm secrets, K8s secrets, Vault, or cloud providers.

> Q. How to monitor Docker in production?  
- A. Logs → ELK/EFK stack. Metrics → Prometheus+cAdvisor. Alerts → Grafana/Alertmanager.

> Q. What is the difference between docker stats and cAdvisor?  
- A. Stats = live CLI metrics. cAdvisor = historical + Prometheus integration.

> Q. Your container is running as root. Why is that dangerous? How do you fix it?  
- A. Root in container = root on host if breakout. Use `USER` directive.

> Q. How do you reduce Docker attack surface?  
- A. Alpine/Distroless base, non-root, scan regularly, pin versions.

> Q. Container hogs disk space. How to debug?  
- A. Check logs (log rotation), inspect volumes, prune unused objects.

> Q. Why avoid `docker exec` in prod?  
- A. Breaks immutability. Best practice: rebuild & redeploy.

> Q. What’s the role of BuildKit?  
- A. Faster builds, caching, build secrets, multi-platform builds.

> Q. How do you implement canary deployments with Docker?  
- A. Requires orchestrator (Swarm/K8s). Run new version alongside old, route partial traffic.
