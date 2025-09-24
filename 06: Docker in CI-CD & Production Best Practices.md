🚀 Phase 6: Docker in CI/CD & Production Best Practices

---

🎯 Objectives

- By the end of this phase, you should:
  - Integrate Docker into CI/CD pipelines (Jenkins, GitHub Actions, GitLab CI).
  - Use image tagging strategies (avoid latest in prod).
  - Scan Docker images for vulnerabilities.
  - Optimize images for performance & security.
  - Apply logging, monitoring, and debugging best practices.

---

📘 Concepts

🔹 CI/CD Integration

- Build → Test → Push → Deploy workflow using Docker.
- Common CI/CD tools: Jenkins, GitHub Actions, GitLab CI, Azure DevOps.
- Steps in pipeline:
  - Checkout code
  - Build Docker image
  - Run tests inside container
  - Tag and push image to registry
  - Deploy (to staging/prod)

---

🔹 Image Tagging Strategy

- Bad practice: using latest (unpredictable).
- Good practices:
  - Semantic versioning (app:1.2.3)
  - Git commit hash (app:abc123)
  - Build number (app:build-45)
- Use multi-tags for traceability:
```bash
docker build -t user/app:1.2.3 -t user/app:latest .
```

---

🔹 Security Scanning

- Use Docker Scan or external tools (Trivy, Snyk).
- Check for CVEs in base images & dependencies.
👉 Example:
```bash
docker scan myapp:1.0
trivy image myapp:1.0
```

---

🔹 Optimizing Images

- Use multi-stage builds to keep runtime image small.
- Use Alpine or Distroless base images.
- Clean up dependencies:
```bash
RUN apt-get update && apt-get install -y build-essential \
    && rm -rf /var/lib/apt/lists/*
```
- Order Dockerfile instructions to maximize layer caching.

---

🔹 Logging & Monitoring

- Containers log to stdout/stderr by default.
- Collect logs via:
  - docker logs
  - Logging drivers (json-file, syslog, fluentd, awslogs)
- Monitoring tools: Prometheus + cAdvisor, ELK stack, Grafana.

---

🔹 Debugging in Production

- Run ephemeral debugging containers with --rm.
- Use docker exec to enter running containers.
- Use netshoot for network debugging.

---

🧪 Examples (3 Levels)

✅ Beginner

- Simple GitHub Action pipeline:
```bash
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build Docker image
        run: docker build -t user/app:${{ github.sha }} .
```

---

✅ Intermediate

- Jenkins pipeline with Docker:
```bash
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t user/app:${BUILD_NUMBER} .'
      }
    }
    stage('Test') {
      steps {
        sh 'docker run user/app:${BUILD_NUMBER} pytest tests/'
      }
    }
    stage('Push') {
      steps {
        withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'PASS')]) {
          sh 'echo $PASS | docker login -u user --password-stdin'
          sh 'docker push user/app:${BUILD_NUMBER}'
        }
      }
    }
  }
}
```

---

✅ Advanced

Trivy scan in CI pipeline:
```bash
- name: Security Scan
  run: trivy image user/app:${{ github.sha }}
```
- Multi-stage build example:
```bash
# Build stage
FROM node:18 as builder
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Runtime stage
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
```
- 👉 Final image is tiny with only static assets.

---

💼 Mini Project: CI/CD Pipeline with Security Scan

- 1.Create a Node.js app.
- 2.Write multi-stage Dockerfile.
- 3.Create GitHub Actions workflow:
- 4.Build image
- 5.Run tests
- 6.Scan with Trivy
- 7.Push to Docker Hub
- 8.Deploy to staging (run container).

---

🧠 Interview Tips

> Q: Why not use latest tag in production?
- A: It’s ambiguous; versioned tags ensure consistency & rollback ability.

> Q: How to reduce Docker image size?
- A: Use multi-stage builds, Alpine base, .dockerignore, remove build tools.

> Q: How do you secure Docker images?
- A: Scan images, drop root privileges, use minimal base, pin versions.

> Q: How do you monitor Docker containers in production?
- A: Logging drivers + tools like ELK/EFK stack, Prometheus, Grafana.

> Q: How do you debug a running container?
- A: docker exec -it <id> sh or use netshoot for advanced debugging.

---

🪄 Hidden Gems

- docker stats → real-time CPU/memory usage.
- docker events → monitor container lifecycle in prod.
- docker system prune -a → free up unused images/containers.
- Use immutable images → rebuild instead of patching in prod.

---

🏠 Homework (5 Tasks)

- 1.Create a GitHub Action that builds & pushes Docker image with commit hash.
- 2.Add Trivy scan to your CI pipeline.
- 3.Write a multi-stage Dockerfile for a Node/React app.
- 4.Configure a service to use json-file logging driver with rotation.
- 5.Run docker stats on multiple containers and analyze resource usage.

---

## 🚀 Phase 6 – Missing Pieces / Deep Dives

🔹 1. Docker Registry Options
- Besides Docker Hub, companies use private registries:
  - AWS ECR (Elastic Container Registry)
  - GCP Artifact Registry
  - Azure ACR (Container Registry)
  - Self-hosted Harbor/Quay
  - 👉 Interview Q: “How would you secure a private registry?” → Use TLS + authentication.

  ---

  🔹 2. Secrets Management
- Never hardcode secrets in images or Compose.
- Options:
  - Docker Swarm secrets
  - Vault, AWS/GCP Secrets Manager
  - .env files (only for dev, never commit to Git!)
👉 Example Swarm secret:
```bash
echo "mypassword" | docker secret create db_pass -
```

---

🔹 3. Resource Limits & Isolation
- Use --cpus, --memory to control resource usage in production.
```bash
docker run --cpus=1.0 --memory=512m myapp
```
- Prevents “noisy neighbor” problems on shared hosts.

---

🔹 4. Docker Content Trust (DCT)
- Ensures only signed, verified images are used.
```bash
export DOCKER_CONTENT_TRUST=1
```
- 👉 Blocks unsigned images → supply chain protection.

---

🔹 5. Immutable Infrastructure Mindset
- Don’t docker exec into prod containers to fix issues → instead, rebuild & redeploy.
- Interviewers love asking: “Would you patch a running container?” → Correct answer: No. Containers should be immutable.

---

🔹 6. Multi-Stage Testing
- Run unit tests inside CI containers before pushing image.
👉 Example in pipeline:
```bash
docker run --rm myapp:build pytest tests/
```
- Ensures images are validated before shipping.

---

🔹 7. Observability in Production
- Logs: docker logs (basic), but forward to ELK/EFK for centralized logging.
- Metrics: docker stats, cAdvisor, Prometheus exporters.
- Tracing: Jaeger/Zipkin for distributed microservices.

---

🔹 8. Vulnerability Management
- Base images frequently get CVEs → use automation to rebuild when patched.
- Tools: Dependabot, Renovate, Watchtower (auto-update running containers).

---

🧠 Extra Interview Q&A

> Q: Why is using the latest tag risky in production?
- A: It’s ambiguous. New builds may introduce breaking changes silently.

> Q: How would you implement Docker image security in a CI/CD pipeline?
- A: Use Trivy/Snyk scans, sign images, use minimal base images, and enforce content trust.

> Q: How do you prevent containers from consuming all system resources?
- A: Apply CPU and memory limits via --cpus and --memory flags or Compose deploy resources.

> Q: What is Docker Content Trust?
- A: A feature to sign and verify images, ensuring only trusted images are pulled.

> Q: What’s the difference between monitoring with docker stats and cAdvisor?
- A: docker stats shows live metrics per container. cAdvisor provides detailed metrics for Prometheus/Grafana integration.

> Q: How would you handle secrets in Docker-based apps?
- A: Use Swarm/Kubernetes secrets, Vault, or cloud secret managers. Never bake secrets into images.

> Q: If a container crashes in prod, what’s your strategy?
- A: Use restart policies, logging to understand cause, but redeploy via CI/CD rather than patching manually.
