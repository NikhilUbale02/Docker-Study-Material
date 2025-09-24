# 🐳 Docker Interview Differences (Phase 1–6)

---

## Phase 1 – Basics
Image vs Container
- Image: Blueprint, read-only, immutable.
- Container: Running instance of image, writable layer added.

Container vs VM
- Container: OS-level virtualization, lightweight, shares host kernel.
- VM: Hardware-level virtualization, heavy, full guest OS.

---

## Phase 2 – Images & Dockerfile
CMD vs ENTRYPOINT
- CMD: Default command, can be overridden at runtime.
- ENTRYPOINT: Main process, harder to override.
- Best practice: Use ENTRYPOINT for main, CMD for args.

COPY vs ADD
- COPY: Copies files/directories from build context.
- ADD: Same as COPY + can fetch from URLs & auto-extract archives.
- Best practice: Prefer COPY unless ADD is required.

Build Context vs .dockerignore
- Build Context: Directory sent to Docker daemon during build.
- .dockerignore: Excludes files from context to shrink image & speed up builds.

Base Image vs Scratch
- Base Image: Prebuilt OS/runtime layer (ubuntu, alpine, python).
- Scratch: Empty image, used for minimal builds (common in Go/C).

---

## Phase 3 – Volumes & Persistence
Bind Mount vs Named Volume
- Bind Mount: Maps host directory → container path, tied to host FS.
- Named Volume: Managed by Docker in /var/lib/docker/volumes, portable.

-v vs --mount
- -v: Shorthand, less clear.
- --mount: Explicit, modern, supports volume, bind, tmpfs.

Anonymous Volume vs Named Volume
- Anonymous: Auto-created, random name, hard to manage.
- Named: Explicitly created, reusable, manageable.

---

## Phase 4 – Networking
Bridge vs Host Network
- Bridge: Default mode, isolated network, requires port mapping.
- Host: Shares host’s network stack, no isolation, no port mapping needed.

Default Bridge vs Custom Bridge
- Default Bridge: Containers communicate only via IP.
- Custom Bridge: Containers resolve each other by name (Docker DNS).

EXPOSE vs -p (Publish)
- EXPOSE: Documentation in Dockerfile, no actual exposure.
- -p: Publishes container port → host port mapping.

depends_on vs Healthcheck (in Compose)
- depends_on: Controls startup order only.
- healthcheck: Verifies readiness, needed for reliable dependency mgmt.

---

## Phase 5 – Docker Compose
Docker Run vs Docker Compose
- Docker Run: CLI command, single container.
- Docker Compose: YAML-based, multi-container definition & orchestration.

Dockerfile ENV vs Compose Environment
- ENV (Dockerfile): Sets env vars at image build/runtime.
- environment: (Compose): Injects env vars at container runtime.

docker-compose down vs docker-compose down -v
- down: Stops containers, removes networks.
- down -v: Also removes volumes (data lost).

---

## Phase 6 – CI/CD & Production
latest Tag vs Versioned Tag
- latest: Ambiguous, may change unexpectedly.
- Versioned: Explicit (e.g., app:1.0), safe for prod.

Docker Scan vs Trivy
- Docker Scan: Built-in, limited scope, powered by Snyk.
- Trivy: External, broader scanning (OS packages + libs).

docker exec vs Immutable Redeploy
- docker exec: Temporary fix/debug inside running container.
- Immutable Redeploy: Best practice, rebuild image & redeploy instead of patching.

docker stats vs cAdvisor/Prometheus
- docker stats: Live metrics per container (CPU, Mem).
- cAdvisor/Prometheus: Centralized, historical metrics, alerting.

Public vs Private Registry
- Public (Docker Hub): Open, rate limits apply.
- Private (ECR, ACR, Harbor): Secure, controlled access, enterprise-ready.
