🏗 Phase 2: Docker Images & Dockerfiles

---

🎯 Objectives
- By the end of this phase, you should:
- Understand what Docker images are and how they’re built.
- Learn the structure & lifecycle of Docker images.
- Write and optimize Dockerfiles.
- Push/pull images from Docker Hub.
- Understand best practices for image management.

---

📘 Concepts
🔹 Docker Images
- A Docker Image is a read-only blueprint for creating containers.
- Built in layers (each instruction in a Dockerfile adds a new layer).
- Images are immutable → if you need changes, you build a new one.
- Union File System is used to stack layers efficiently.

---

🔹 Dockerfile
- A Dockerfile is a text file with instructions to build an image.
- Common instructions:
```bash
FROM → base image (e.g., alpine, ubuntu, python)
RUN → executes commands during build
COPY / ADD → copy files from host into image
WORKDIR → set working directory inside container
CMD → default command executed at runtime
ENTRYPOINT → main command (not overridden easily)
EXPOSE → document ports used (not mandatory for mapping)
ENV → set environment variables
```

---

🔹 Build Context & .dockerignore
- Build context: directory sent to Docker daemon during build.
- .dockerignore: exclude unnecessary files (e.g., .git, node_modules).

---

🔹 Image Management
- docker build → build image from Dockerfile.
- docker tag → give image a friendly name & version.
- docker push / docker pull → upload/download images from registry.

---

🧪 Examples (3 Levels)

---

✅ Beginner
- 1.Create a simple Python script app.py:
```bash
print("Hello from Docker!")
```
---
- 2.Write Dockerfile:
```bash
FROM python:3.9-slim
COPY app.py /app/
WORKDIR /app
CMD ["python", "app.py"]
```
---
- 3.Build & run:
```bash
docker build -t hello-python .
docker run hello-python
```
---

✅ Intermediate
- 1.Add environment variables and install dependencies:
```bash
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
ENV PORT=3000
EXPOSE 3000
CMD ["npm", "start"]
```
---
- 2.Build & run with port mapping:
```bash
docker build -t my-node-app .
docker run -d -p 3000:3000 my-node-app
```
---

✅ Advanced
Multi-stage build for Go app (saves image size):
```bash
# Build stage
FROM golang:1.20 as builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Run stage
FROM alpine:latest
WORKDIR /app
COPY --from=builder /app/myapp .
CMD ["./myapp"]
```

👉 Produces a very small final image.

---

💼 Mini Project: Dockerize a Flask Web App

---

- 1.Create app.py:
```bash  
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Flask in Docker!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```
---

- 2.Dockerfile
```bash
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```
---

- 3.requirements.txt
```bash
flask
```
---

- 4.Build & Run:
```bash
docker build -t flask-docker .
docker run -d -p 5000:5000 flask-docker
```

👉 Visit http://localhost:5000

---

🧠 Interview Tips:

- Q: Difference between CMD and ENTRYPOINT?
- A: CMD provides defaults (overridable at runtime). ENTRYPOINT defines the main command (harder to override).

---

- Q: How do you reduce Docker image size?
- A: Use smaller base images (e.g., alpine), multi-stage builds, .dockerignore, combine RUN commands.
---

- Q: What is the difference between COPY and ADD?
- A: COPY copies files; ADD can also fetch from URLs and auto-extract archives.

---

- Q: Why are images layered?
- A: Layers allow caching, reuse, and efficient builds.

---

- Q: How to view layers of an image?
- A: docker history <image>

---

🪄 Hidden Gems:

- Use --squash during build to merge all layers into one.
- Use docker build --no-cache to force rebuild.
- hadolint → lint Dockerfiles for best practices.
- Always pin base images (python:3.9-slim instead of python:latest).
- Use .dockerignore to avoid bloated builds.

---

🏠 Homework (5 Tasks):

- Write a Dockerfile for a simple Node.js app and run it.
- Add .dockerignore to exclude unnecessary files.
- Create a multi-stage Dockerfile for a Go or Java app.
- Push your custom image to Docker Hub.
- Experiment with CMD vs ENTRYPOINT (e.g., overriding runtime args).

---

## 🏗 Phase 2 – Missing Pieces / Deep Dives

---

🔹 1. Image Caching & Layer Reuse

- Docker builds images in layers.
- If nothing changes in an earlier instruction, Docker reuses cached layers → faster builds.
- Order matters: put COPY package.json before copying all code in Node.js apps.

👉 Example:

```bash
COPY package.json .
RUN npm install
COPY . .
```

If code changes but package.json doesn’t → dependencies stay cached.

---

🔹 2. Difference Between Base Image & Scratch

- Base Image = like ubuntu, alpine, python:3.9 (already has OS/tools).
- scratch = truly empty image (used to build minimal images).

👉 Example minimal C program inside scratch:

```bash
FROM scratch
COPY hello /
CMD ["/hello"]
```

---

🔹 3. Ephemeral Containers & Data

- Containers are stateless → data disappears if container dies.
- Reinforces why volumes (covered in Phase 3) are necessary.

---

🔹 4. Multi-Arch Images

- Docker images can be built for different architectures (amd64, arm64).
- Useful when running on Raspberry Pi vs cloud VMs.

```bash
docker buildx build --platform linux/arm64,linux/amd64 -t user/app .
```

---

🔹 5. Entrypoint Scripts

- Real-world apps often need entrypoint.sh to prep environment (migrations, configs).
- Combine with ENTRYPOINT to add flexibility.

👉 Example:

```bash
COPY entrypoint.sh /usr/local/bin/
ENTRYPOINT ["entrypoint.sh"]
CMD ["python", "app.py"]
```

---

🔹 6. Docker Image Security

- Don’t run apps as root inside containers.
- Use USER directive in Dockerfile.

```bash
RUN useradd -m appuser
USER appuser
```
- Scan images with docker scan or Trivy.

---

🔹 7. Inspecting & Troubleshooting Images

- docker history <image> → see all layers.
- docker inspect <image> → metadata, env vars, architecture.
- docker save & docker load → export/import images as tarballs (air-gapped environments).

---

🔹 8. BuildKit (Modern Docker Builds)

- Faster builds, better caching.
- Enables secrets, mounts, and advanced caching.

```bash
DOCKER_BUILDKIT=1 docker build .
```

---

🧠 Extra Interview Q&A

---

Q: What is the difference between COPY and ADD?
A: 
- COPY → strictly copies files/directories from host into the image.
- ADD → does everything COPY does, but also:
- Can extract compressed archives (e.g., .tar.gz).
- Can fetch files from remote URLs.
👉 Best practice: use COPY unless you specifically need ADD.

---

Q: Why is order of Dockerfile instructions important?
A:
- Each Dockerfile instruction creates a new image layer.
- Docker caches layers → if earlier instructions don’t change, they’re reused.
- Reordering can invalidate cache and cause longer builds.
👉 Always put less frequently changing instructions (e.g., dependencies) before frequently changing ones (e.g., source code).

---

Q: How does Docker build caching work?
A:
- When building an image, Docker checks if a layer already exists in cache.
- If the instruction + context (files) haven’t changed, that layer is reused.
- If one layer changes, all subsequent layers must be rebuilt.
👉 Optimized order = faster builds.

---

Q: What is scratch?
A:
- `scratch` is an empty image provided by Docker.
- Used as a starting point for minimal images (no OS, no libraries).
- Often used with statically compiled binaries (Go, C).
👉 Example: FROM scratch

---

Q: Why is Alpine often used in Docker images?
A:
- Tiny Linux distribution (~5 MB).
- Secure and minimal → fewer vulnerabilities.
- Faster downloads, reduced attack surface.
- Common base for production images.

---

Q: How do you reduce attack surface in Docker images?
A:
- Use minimal base images (e.g., alpine, distroless).
- Avoid running as root → use USER directive.
- Remove unnecessary tools/packages.
- Regularly scan with Docker Scan, Trivy, or Snyk.
- Pin image versions instead of `latest`.

---

Q: How can you build Docker images for multiple architectures?
A:
- Use `docker buildx` for cross-platform builds.
- Example:
docker buildx build --platform linux/amd64,linux/arm64 -t user/app .
- Pushes a manifest list so the correct image is pulled per architecture.

---

Q: How to export/import Docker images without Docker Hub?
A:
- Export image:
docker save -o myimage.tar myimage:tag
- Transfer file to another machine.
- Import image:
docker load -i myimage.tar
👉 Useful in air-gapped or private environments.
