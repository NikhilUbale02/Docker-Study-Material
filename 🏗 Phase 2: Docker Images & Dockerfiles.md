🏗 Phase 2: Docker Images & Dockerfiles

🎯 Objectives
- By the end of this phase, you should:
- Understand what Docker images are and how they’re built.
- Learn the structure & lifecycle of Docker images.
- Write and optimize Dockerfiles.
- Push/pull images from Docker Hub.
- Understand best practices for image management.

📘 Concepts
🔹 Docker Images
- A Docker Image is a read-only blueprint for creating containers.
- Built in layers (each instruction in a Dockerfile adds a new layer).
- Images are immutable → if you need changes, you build a new one.
- Union File System is used to stack layers efficiently.

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
🔹 Build Context & .dockerignore
- Build context: directory sent to Docker daemon during build.
- .dockerignore: exclude unnecessary files (e.g., .git, node_modules).

🔹 Image Management
- docker build → build image from Dockerfile.
- docker tag → give image a friendly name & version.
- docker push / docker pull → upload/download images from registry.

🧪 Examples (3 Levels)

✅ Beginner
- 1.Create a simple Python script app.py:
```bash
print("Hello from Docker!")
```
- 2.Write Dockerfile:
```bash
FROM python:3.9-slim
COPY app.py /app/
WORKDIR /app
CMD ["python", "app.py"]
```
- 3.Build & run:
```bash
docker build -t hello-python .
docker run hello-python
```

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
- 2.Build & run with port mapping:
```bash
docker build -t my-node-app .
docker run -d -p 3000:3000 my-node-app
```

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

💼 Mini Project: Dockerize a Flask Web App

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