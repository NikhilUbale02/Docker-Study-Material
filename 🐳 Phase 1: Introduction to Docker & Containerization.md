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

- Containers vs VMs
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
    - ```docker --version
         docker info
      ```
  
  - Run hello-world:
    - ```docker run hello-world
      ```

- ✅ Intermediate
  - Pull & run an image:
    - ```docker pull nginx
         docker run -d -p 8080:80 nginx
      ```

  - View running containers:
    - ```docker ps
         docker ps -a
      ```


 