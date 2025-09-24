# 🐳 Docker Interview Differences (Phase 1 → Phase 6)

---

## 🔰 Phase 1 – Basics

| Image 🖼️ | vs | Container 📦 |
|----------|----|--------------|
| Immutable, read-only blueprint | vs | Running instance of an image |
| Stored in registry/local cache | vs | Lives in memory, consumes CPU/RAM |
| Doesn’t run on its own | vs | Actively running process |
| Built in layers | vs | Adds writable layer on top of image |
| Portable across environments | vs | Ephemeral, dies when stopped unless persisted |

---

| Container ⚡ | vs | Virtual Machine 💻 |
|-------------|----|--------------------|
| OS-level virtualization (shares host kernel) | vs | Hardware-level virtualization (full OS) |
| Lightweight (MBs) | vs | Heavy (GBs) |
| Starts in seconds | vs | Starts in minutes |
| Shares host kernel | vs | Has its own guest kernel |
| Great for microservices | vs | Great for monolith/legacy apps |

---

## 🏗️ Phase 2 – Images & Dockerfile

| CMD 📝 | vs | ENTRYPOINT 🚀 |
|--------|----|---------------|
| Provides default command/args | vs | Defines main process |
| Easily overridden at runtime | vs | Harder to override |
| Use for flexible defaults | vs | Use for core execution |
| Example: `CMD ["python", "app.py"]` | vs | `ENTRYPOINT ["python"]` + `CMD ["app.py"]` |

---

| COPY 📂 | vs | ADD 📦 |
|---------|----|--------|
| Strictly copies files/dirs from host → image | vs | Same as COPY + fetch URLs & auto-extract archives |
| Predictable, safe | vs | Sometimes “too smart” |
| Recommended best practice | vs | Use only if special features needed |

---

| Build Context 🎒 | vs | .dockerignore 🚫 |
|------------------|----|-----------------|
| Directory sent to Docker daemon when building | vs | File to exclude unnecessary stuff |
| Everything inside is available to COPY/ADD | vs | Keeps builds faster & images smaller |
| Can bloat builds if too big | vs | Avoids `.git`, `node_modules`, logs |

---

| Base Image 🏗️ | vs | Scratch 🪣 |
|----------------|----|-----------|
| Pre-configured with OS/runtime (Ubuntu, Alpine, Python) | vs | Empty base image |
| Larger, more features | vs | Minimal, tiny images |
| Used for most apps | vs | Used for static binaries (Go, C) |
| Example: `FROM python:3.9` | vs | `FROM scratch` |

---

## 💾 Phase 3 – Volumes & Persistence

| Bind Mount 🔗 | vs | Named Volume 📦 |
|---------------|----|-----------------|
| Maps host directory → container path | vs | Managed by Docker in `/var/lib/docker/volumes` |
| Tight coupling with host filesystem | vs | Host-agnostic, portable |
| Great for dev (hot reload) | vs | Great for production persistence |
| Example: `-v $(pwd):/app` | vs | `-v myvol:/app/data` |

---

| -v 🆚 | vs | --mount 🛠️ |
|-------|----|------------|
| Old shorthand | vs | Modern explicit syntax |
| Less flexible | vs | Clearer and supports bind, volume, tmpfs |
| Example: `-v name:/data` | vs | `--mount type=volume,src=name,dst=/data` |

---

| Anonymous Volume ❓ | vs | Named Volume 🏷️ |
|---------------------|----|-----------------|
| Auto-generated, random hash name | vs | Explicitly created with name |
| Harder to track/manage | vs | Easy to reference/reuse |
| Often leftover → clutter | vs | Clean lifecycle control |

---

## 🌐 Phase 4 – Networking

| Bridge 🌉 | vs | Host 🖧 |
|-----------|----|---------|
| Default, isolated network | vs | Shares host network stack |
| Containers get private IP | vs | Container uses host IP |
| Requires port mapping (-p) | vs | No mapping needed |
| More isolation | vs | Better performance, less isolation |

---

| Default Bridge 🛤️ | vs | Custom Bridge 🛠️ |
|--------------------|----|------------------|
| Containers can only talk by IP | vs | Containers can talk by name (Docker DNS) |
| Limited, not scalable | vs | Preferred for multi-container apps |
| Auto-created | vs | Created manually with `docker network create` |

---

| EXPOSE 🚪 | vs | -p 🔌 |
|-----------|----|-------|
| Just documentation in Dockerfile | vs | Publishes container port to host |
| Doesn’t expose anything by itself | vs | Actively maps host:container |
| Example: `EXPOSE 80` | vs | `-p 8080:80` |

---

| depends_on ⏳ | vs | healthcheck ❤️ |
|---------------|----|----------------|
| Controls startup order only | vs | Verifies if service is healthy/ready |
| Doesn’t wait for readiness | vs | Works with retries, restart policies |
| Useful but limited | vs | Critical for reliable dependencies |

---

## ⚙️ Phase 5 – Docker Compose

| Docker Run 🏃 | vs | Docker Compose 🛠️ |
|---------------|----|-------------------|
| CLI command → single container | vs | YAML file → multi-container apps |
| Requires lots of flags | vs | Declarative config (infra as code) |
| Networking/volumes manual | vs | Auto-handled |
| Example: `docker run -p 80:80 nginx` | vs | `docker-compose up -d` |

---

| Dockerfile ENV 🌍 | vs | Compose environment 📜 |
|-------------------|----|------------------------|
| Sets env vars inside image (build/runtime) | vs | Injects env vars at runtime |
| Persistent in container | vs | Flexible, often from `.env` file |
| Example: `ENV APP=prod` | vs | `environment: - APP=${APP}` |

---

| docker-compose down ⚡ | vs | docker-compose down -v 💣 |
|-------------------------|----|--------------------------|
| Stops containers & removes networks | vs | Same + removes volumes |
| Preserves persistent data | vs | Deletes persistent data |
| Safer for dev | vs | Use with caution (data loss!) |

---

## 🚀 Phase 6 – CI/CD & Production

| latest 🆚 | vs | Versioned Tags 🔖 |
|-----------|----|-------------------|
| Ambiguous pointer, may change | vs | Explicit (1.0, git-sha) |
| Not safe for prod | vs | Safe, reproducible, traceable |
| Example: `myapp:latest` | vs | `myapp:1.2.3` |

---

| Docker Scan 🔍 | vs | Trivy 🛡️ |
|----------------|----|-----------|
| Built-in, powered by Snyk | vs | External, open-source |
| Limited scope | vs | Rich ecosystem (OS pkgs + libs) |
| Good for quick checks | vs | Industry standard for pipelines |

---

| docker exec 🛠️ | vs | Immutable Redeploy 🔄 |
|-----------------|----|-----------------------|
| Enter running container → debug/patch | vs | Rebuild new image + redeploy |
| Temporary fix only | vs | Long-term, reproducible fix |
| Breaks immutability principle | vs | Immutable infrastructure best practice |

---

| docker stats 📊 | vs | cAdvisor/Prometheus 📈 |
|-----------------|----|------------------------|
| CLI-based, live resource usage | vs | Centralized monitoring & alerting |
| Good for quick checks | vs | Full observability in production |
| Per-container only | vs | Cluster-wide, historical metrics |

---

| Public Registry 🌍 | vs | Private Registry 🔐 |
|--------------------|----|---------------------|
| Docker Hub (rate limits, open) | vs | ECR, ACR, Harbor, Quay (enterprise) |
| Anyone can pull images | vs | Controlled access, integrated with IAM |
| Risk of typosquatting, malicious images | vs | Trusted, secure supply chain |

---

# 🐳 Docker Interview Differences (Phase 1 → Phase 6)

---

## 🔰 Phase 1 – Basics

Image 🖼️ vs Container 📦
- Image:
- Immutable blueprint (read-only).
- Stored on disk (Docker Hub, Registry, local).
- Doesn’t run by itself.
- Container:
- Running instance of an image (read-write layer on top).
- Lives in memory, consumes CPU/RAM.
- Ephemeral by default → dies = gone (unless volumes used).

Container ⚡ vs Virtual Machine 💻
- Container:
- OS-level virtualization (shares host kernel).
- Lightweight (MBs).
- Startup in seconds.
- VM:
- Hardware-level virtualization (Hypervisor, full guest OS).
- Heavy (GBs).
- Startup in minutes.

---

## 🏗️ Phase 2 – Images & Dockerfile

CMD 📝 vs ENTRYPOINT 🚀
- CMD:
- Provides default command/args.
- Overridable at runtime (`docker run image echo hi`).
- ENTRYPOINT:
- Defines the "main process".
- Not easily overridden.
- Best practice:
- ENTRYPOINT = app binary.
- CMD = default args.

COPY 📂 vs ADD 📦
- COPY:
- Purely copies files from host → image.
- Predictable, recommended for most cases.
- ADD:
- COPY + auto-extract archives + fetch from URLs.
- Can be “too smart” → avoid unless necessary.

Build Context 🎒 vs .dockerignore 🚫
- Build Context:
- Directory sent to Docker daemon at build time.
- Everything inside can be copied via COPY/ADD.
- .dockerignore:
- Excludes unnecessary stuff (e.g., `.git`, `node_modules`).
- Keeps images slim & builds fast.

Base Image 🏗️ vs Scratch 🪣
- Base Image:
- Pre-configured (Ubuntu, Alpine, Python).
- Provides OS/runtime layers.
- Scratch:
- Empty → true “from scratch”.
- Used for minimal/static binaries (Go, C).
- Zero bloat, tiny images.

---

## 💾 Phase 3 – Volumes & Persistence

Bind Mount 🔗 vs Named Volume 📦
- Bind Mount:
- Maps **specific host path** → container.
- Tight coupling with host FS.
- Great for dev hot-reload, risky in prod.
- Named Volume:
- Managed by Docker in `/var/lib/docker/volumes`.
- Host-agnostic, portable, easier to back up.
- Preferred in production.

-v 🆚 --mount
- `-v`:
- Old shorthand, ambiguous syntax.
- Example: `-v name:/data`.
- `--mount`:
- New, explicit syntax.
- Example: `--mount type=volume,src=name,dst=/data`.
- Better for clarity & scripting.

Anonymous Volume ❓ vs Named Volume 🏷️
- Anonymous:
- Auto-generated, random hash name.
- Easy to forget → orphaned volumes.
- Named:
- Explicitly created.
- Reusable & easier to manage.

---

## 🌐 Phase 4 – Networking

Bridge 🌉 vs Host 🖧
- Bridge:
- Default mode.
- Container gets private IP.
- Requires `-p` to expose to host.
- Host:
- Container shares host network stack.
- No port mapping needed.
- Less isolation, higher perf.

Default Bridge 🛤️ vs Custom Bridge 🛠️
- Default Bridge:
- Containers can’t resolve each other by name (IP only).
- Custom Bridge:
- Embedded DNS → containers resolve each other by **name**.
- Recommended for multi-container apps.

EXPOSE 🚪 vs -p 🔌
- EXPOSE:
- Metadata/documentation in Dockerfile.
- Doesn’t actually publish ports.
- -p:
- Explicitly maps host:container ports.
- Required for external access.

depends_on ⏳ vs healthcheck ❤️
- depends_on:
- Only controls startup order.
- Does NOT wait for service readiness.
- healthcheck:
- Defines readiness probes.
- Works with restart policies & service deps.

---

## ⚙️ Phase 5 – Docker Compose

Docker Run 🏃 vs Docker Compose 🛠️
- docker run:
- CLI command, one container at a time.
- Lots of flags, manual networking.
- Docker Compose:
- YAML config (infra as code).
- Spins up multi-container stacks with one command.
- Auto-handles networks & volumes.

Dockerfile ENV 🌍 vs Compose environment 📜
- ENV (Dockerfile):
- Defines environment variables baked into the image.
- Persistent across runs unless overridden.
- environment (Compose):
- Injects runtime env vars.
- Can use `.env` files → keeps secrets/config separate.

docker-compose down ⚡ vs docker-compose down -v 💣
- down:
- Stops containers & removes networks.
- Volumes persist.
- down -v:
- Also nukes volumes → **data gone**.

---

## 🚀 Phase 6 – CI/CD & Production

latest 🆚 versioned tags 🔖
- latest:
- Floating pointer, can change anytime.
- Dangerous → unpredictable builds.
- versioned tags:
- Explicit (`app:1.0`, `app:commit-sha`).
- Safe, reproducible, traceable.

Docker Scan 🔍 vs Trivy 🛡️
- Docker Scan:
- Built-in (powered by Snyk).
- Quick checks, limited ecosystem.
- Trivy:
- Rich scanning (OS pkgs + libs).
- Open-source, widely used in CI/CD.

docker exec 🛠️ vs Immutable Redeploy 🔄
- exec:
- Debug/fix inside running container.
- Temporary, not best practice in prod.
- Immutable redeploy:
- Proper way → rebuild image & redeploy.
- Keeps infra reproducible.

docker stats 📊 vs cAdvisor/Prometheus 📈
- docker stats:
- Real-time resource usage per container (CLI).
- cAdvisor/Prometheus:
- Full observability → metrics, history, alerting.
- Used in production monitoring.

Public Registry 🌍 vs Private Registry 🔐
- Public (Docker Hub):
- Open, subject to rate limits.
- Private (ECR, ACR, Harbor, Quay):
- Enterprise-grade, secure, integrated with cloud IAM.
- Control access & compliance.
