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
