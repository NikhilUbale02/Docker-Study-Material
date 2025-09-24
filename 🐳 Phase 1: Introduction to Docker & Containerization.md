🐳 Phase 1: Introduction to Docker & Containerization

🎯 Objectives
- By the end of this phase, you should:
  - Understand what containers are and why Docker is used in DevOps.
  - Differentiate VMs vs Containers.
  - Learn Docker architecture (Client, Daemon, Registry, Images, Containers).
  - Install Docker and run your first container.
  - Explore container lifecycle basics.

📘 Concepts
- What is Docker?
  - Docker is a containerization platform that packages applications and their      dependencies into lightweight, portable units called containers.
  - Think of it as a shipping container for your app → it contains code, runtime, libraries, and configs in one box.
  - Ensures consistency across environments (dev, test, prod).

## Containers vs VMs

| Feature     | Virtual Machine (VM)        | Container                   |
| ----------- | --------------------------- | --------------------------- |
| Abstraction | Hardware-level (Hypervisor) | OS-level (Docker Engine)    |
| Size        | GBs (with full OS)          | MBs (shares host OS kernel) |
| Startup     | Minutes                     | Seconds                     |
| Portability | Limited                     | Highly portable             |
| Performance | Heavier                     | Lightweight                 |

👉 Key takeaway: Containers share the host OS kernel → much faster, lighter.

- Docker Architecture
  - Docker Client (docker CLI): You interact with it (docker run, docker ps).
  - Docker Daemon (dockerd): Background service managing containers.
  - Docker Registry: Where images live (e.g., Docker Hub, private registry).
  - Docker Image: Blueprint (immutable).
  - Docker Container: Running instance of an image (mutable).

🧪 Examples (3 Levels)
- ✅ Beginner
  - Check Docker version:
    ```bash
       docker --version
       docker info
    ```
  
  - Run hello-world:
    ```bash
       docker run hello-world
    ```

- ✅ Intermediate
  - Pull & run an image:
    ```bash
       docker pull nginx
       docker run -d -p 8080:80 nginx
    ```

  - View running containers:
    ```bash
       docker ps
       docker ps -a
    ```

- ✅ Advanced
  - Inspect container metadata:
    ```bash
       docker inspect <container_id>
    ```
  
  - View events in real-time:
    ```bash
       docker events
    ```
  
  - Check container logs:
    ```bash
       docker logs <container_id>
    ```

🌐 Types of Docker Networks

  - Docker provides multiple network drivers, each designed for different use cases.
  
  🔹 1. Bridge Network (default)
    - Default driver for containers if you don’t specify one.
    - Containers on the same bridge can talk to each other using container names.
    - Communication with external world requires port mapping (-p).

    👉 Example:

    ```bash
       docker run -d --name web1 nginx
       docker run -d --name web2 nginx
       docker network inspect bridge
    ```

  🔹 2. Host Network
    - Removes the network isolation between container and host.
    - Container shares the host’s network stack → same IP as host.
    - No port mapping needed.

    👉 Example:

    ```bash
       docker run -d --network host nginx
    ```

    (Your nginx is directly accessible on host’s IP at port 80.)

  🔹 3. None Network
    - Container has no networking.
    - Useful for security-restricted or testing cases.

    👉 Example:

    ```bash
       docker run -d --network none nginx
    ```
    (Container is isolated, no internet, no LAN access.)

  🔹 4. Overlay Network
    - Used in multi-host Docker Swarm or Kubernetes setups.
    - Allows containers running on different Docker hosts to communicate.
    - Requires a key-value store (like Consul or etcd) in older Docker, but now mostly used with Swarm.

    👉 Example:
    
    ```bash
       docker network create -d overlay my_overlay
    ```   
 
  🔹 5. Macvlan Network
    - Assigns containers a unique MAC address → they appear as physical devices on the LAN.

    - Useful when containers need to be treated like real machines in the same network.

    👉 Example:

    ```bash
       docker network create -d macvlan \
       --subnet=192.168.1.0/24 \
       --gateway=192.168.1.1 \
       -o parent=eth0 my_macvlan
    ```

  🔹 6. Custom Bridge Networks

    - Like default bridge, but user-defined.
    - Offers better DNS-based resolution (containers can reach each other by name).

    👉 Example:

    ```bash
       docker network create my_bridge
       docker run -d --network my_bridge --name app nginx
    ```




💼 Mini Project: Your First Web Server

Goal → Run an Nginx container serving a custom HTML page.

  - Steps:
    - Create a folder and add an index.html file:
    ```bash
       <h1>Hello from Docker!</h1>
    ```
  
    - Run nginx container with volume mount:
    ```bash
       docker run -d -p 8080:80 -v $(pwd):/usr/share/nginx/html nginx
    ```

    - Open http://localhost:8080 → You’ll see your custom page.

    - Stop and remove the container:
    ```bash
       docker stop <container_id>
       docker rm <container_id>
    ```

🧠 Interview Tips

  - Q: What is the difference between Docker Image and Container?
    A: Image = blueprint (read-only), Container = running instance (read-write).

  - Q: Why are containers faster than VMs?
    A: They share the host kernel instead of booting a full OS.

  - Q: How do you check running Docker containers?
    A: docker ps
  
🔹 General Concepts

  - Q: What is Docker in simple terms?
    A:Docker is a containerization platform that packages applications and their dependencies into portable containers.
  
  - Q: Why do we need Docker if we already have VMs?
    A: VMs emulate hardware and are heavy (GBs, minutes to boot).
     - Containers virtualize at the OS level, are lightweight (MBs, seconds).
     - Containers allow faster scaling, portability, and efficient resource usage.

  - Q: How is a Docker container different from an image?
    A: Image = blueprint (read-only, immutable).
     - Container = running instance of that image (read-write).

🔹 Architecture / Components

  - Q: Explain Docker architecture.
    A: Client (CLI) → sends commands.
     - Daemon (dockerd) → runs containers, manages images.
     - Registry (e.g., Docker Hub) → stores images.
     - Objects: images, containers, volumes, networks.

  - Q: What happens when you run docker run hello-world?
    A: CLI sends command to daemon.
     - Daemon checks if image exists locally.
     - If not, it pulls from Docker Hub.
     - It creates and runs a container.
     - Container executes hello-world program and exits.
  
  - Q: Where are Docker images and containers stored on Linux by default?
    A: /var/lib/docker/

🔹 Lifecycle & CLI
  
  - Q: What is the lifecycle of a container?
    A: Created → Running → Paused → Stopped → Removed

  - Q: How do you check logs of a container?
    A: docker logs <container_id>

  - Q: What command shows all containers (running + stopped)?
    A: docker ps -a

  - Q: What happens if you stop a container? Will it be removed?
    A: No, it will be in "stopped" state (Exited). Removal is separate.

🔹 Real-World Usage

  - Q: Why do developers prefer Docker over installing dependencies manually?
    A: Consistency across environments
     - Avoids “works on my machine” problem
     - Faster CI/CD pipelines

  - Q: Can multiple containers run on the same host?
    A: Yes, they share the host kernel and resources. Each container is isolated.

  - Q: How do you expose a container to the outside world?
    A: Use -p <host_port>:<container_port> (e.g., -p 8080:80).

🔹 Troubleshooting / Practical

  - Q: How do you enter a running container’s shell?
    A: docker exec -it <container_id> /bin/bash

  - Q: If a container exits immediately after starting, what could be the reason?
    A: The container’s main process finished (e.g., docker run ubuntu exits because there’s no command to keep it running).

  - Q: What’s the difference between docker attach and docker exec?
    A: docker attach connects to the container’s main process.
     - docker exec starts a new process inside the container.

🔹 Extra “Curveball” Questions (Hidden but Asked)

  - Q: How do you check disk usage of Docker objects?
    A: docker system df

  - Q: Can a container run without an image?
    A: No. Every container is based on an image (even if it’s scratch, an empty image).

  - Q: How do you see how an image was built?
    A: docker history <image>

  - Q: What is the default network mode of a container?
    A: bridge

  - Q: Difference between bridge and host network?
    A: Bridge isolates containers; host shares host’s network stack (faster, less isolated).

  - Q: When would you use macvlan?
    A: When containers need their own MAC/IP on the LAN (treated as physical devices).

  - Q: Which network type is used in Docker Swarm?
    A: Overlay.

👉 Pro tip: If you run docker network ls, you’ll see at least:

  - bridge
  - host
  - none

🪄 Hidden Gems

  - docker system df → shows disk usage of images, containers, and volumes.
  - docker events → monitor real-time container lifecycle events.
  - docker history <image> → shows how an image was built layer by layer.   
