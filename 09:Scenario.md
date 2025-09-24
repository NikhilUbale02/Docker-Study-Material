# 🐳 Docker Scenario-Based & Curveball Qs (Phase 1 → 6)

---

## 🔰 Phase 1 – Basics (Containerization Fundamentals)

> Q. Your container exits immediately after starting. What could be the reason?  
- A. The container’s main process finished (e.g., `docker run ubuntu` exits because no long-running command). Fix with `-it bash` or ENTRYPOINT.

> Q. Can a container run without an image?  
- A. No, every container is based on an image (even `scratch`).

> Q. How do namespaces and cgroups contribute to containerization?  
- A. Namespaces = isolation (PID, NET, MNT, IPC, UTS). Cgroups = resource limits (CPU, memory, I/O).

> Q. How would you explain Docker to a non-technical manager?  
- A. "It’s like shipping containers: same package runs everywhere, no environment drift."

---

## 🏗️ Phase 2 – Images & Dockerfile (Builds)

> Q. Your Docker build is taking 20 minutes. How do you optimize it?  
- A. Use .dockerignore, order instructions to leverage cache, multi-stage builds, Alpine base, avoid unnecessary packages.

> Q. You see your image is 2GB. How to shrink it?  
- A. Switch base to Alpine/Distroless, multi-stage builds, remove caches/temp files, merge RUN layers.

> Q. Why is `apt-get update && apt-get install` in separate layers a bad idea?  
- A. Caching issues + stale repos. Should combine into one RUN.

> Q. Can you override ENTRYPOINT at runtime?  
- A. Yes, with `--entrypoint` flag.

> Q. How do you view how an image was built?  
- A. `docker history <image>` shows layers.

> Q. If you had to build a Go app with smallest possible image, what would you do?  
- A. Multi-stage build → compile in golang base → copy binary into scratch or distroless.

---

## 💾 Phase 3 – Volumes & Persistence

> Q. A customer complains their DB data is lost after container restart. Why?  
- A. They didn’t use a persistent volume. By default, container FS is ephemeral.

> Q. When would you use bind mount vs named volume?  
- A. Bind mount = dev (real-time sync). Named volume = prod (portable, managed).

> Q. How do you move Docker volumes from one server to another?  
- A. Backup with `tar` → transfer → restore on new server with `docker run`.

> Q. You see dozens of orphaned volumes consuming GBs. How do you clean them safely?  
- A. `docker volume ls` → verify → `docker volume prune`.

> Q. Your app requires temporary high-speed storage. Which volume type?  
- A. tmpfs (RAM-based, non-persistent).

---

## 🌐 Phase 4 – Networking

> Q. Two containers on the default bridge network can’t talk by name. Why?  
- A. Default bridge doesn’t support DNS resolution. Use custom bridge.

> Q. How do you debug container networking?  
- A. `docker exec -it <container> ping/curl`, inspect network, use netshoot.

> Q. You expose a service with EXPOSE 80, but it’s not reachable from host. Why?  
- A. EXPOSE is only documentation. Need `-p` to map ports.

> Q. When would you use macvlan?  
- A. When containers need their own IP/MAC like real devices (e.g., IoT, legacy networks).

> Q. When would you use overlay networks?  
- A. Multi-host communication in Docker Swarm or K8s.

> Q. Can you attach a container to multiple networks? Why would you?  
- A. Yes. Example: web service joins both frontend and backend networks for isolation.

---

## ⚙️ Phase 5 – Docker Compose

> Q. You deploy with Compose, but app fails because DB wasn’t ready. Why?  
- A. `depends_on` only controls startup order, not readiness. Need healthchecks/wait-for scripts.

> Q. How do you scale backend services to 5 replicas with Compose?  
- A. `docker-compose up --scale backend=5`.

> Q. After `docker-compose down`, your DB data vanished. Why?  
- A. You ran with `-v` flag, which removes volumes.

> Q. Difference between overriding configs in Compose vs rebuilding image?  
- A. Config/env changes can be overridden in YAML. Binary/dependency changes need image rebuild.

> Q. How do you debug a failing service in Compose?  
- A. `docker-compose logs -f <service>` and `docker-compose ps`.

> Q. When would you use multiple docker-compose files?  
- A. To handle dev/staging/prod overrides.

---

## 🚀 Phase 6 – CI/CD & Production

> Q. Why avoid `docker exec` in production?  
- A. Breaks immutability. Containers should be redeployed, not patched live.

> Q. Your CI pipeline builds but fails on Trivy scan. What do you do?  
- A. Fix vulnerable dependencies, rebuild with patched base, retest.

> Q. What’s your Docker tagging strategy for prod?  
- A. Semantic versions, Git SHA, latest for dev only. Multi-tag images for traceability.

> Q. You see Docker images filling disk space on a CI runner. Fix?  
- A. `docker system prune -af --volumes` periodically.

> Q. Container in prod is hogging memory. How do you stop it?  
- A. Apply `--memory` limits or adjust cgroups in Compose/K8s.

> Q. How do you ensure only trusted images run in prod?  
- A. Docker Content Trust, signed images, private registry with RBAC.

> Q. Logs from multiple containers are overwhelming. How do you centralize?  
- A. Use logging drivers (fluentd, syslog, awslogs) → forward to ELK/EFK stack.

> Q. How do you monitor container CPU & memory usage across cluster?  
- A. Prometheus + cAdvisor, Grafana dashboards.

> Q. Your app works locally but fails in CI container. Why?  
- A. Environment mismatch, missing dependencies, wrong base image. Debug with same image in local.

> Q. How do you patch CVEs in your Docker base image?  
- A. Rebuild image with latest base, run scans again. Automate with Renovate/Dependabot.
