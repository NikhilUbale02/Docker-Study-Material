🌐 Phase 4: Docker Networking & Multi-Container Apps

🎯 Objectives

- By the end of this phase, you should:
- Understand how Docker networking works by default.
- Explore different network modes (bridge, host, none, custom, overlay).
- Connect multiple containers in a custom network.
- Use DNS-based service discovery between containers.
- Inspect and troubleshoot container networking.

---

📘 Concepts

🔹 Default Bridge Network
- Every Docker installation creates a default bridge network.
- Containers launched without --network join it.
- Containers get IPs, but DNS resolution only works in user-defined bridges.

---

🔹 Custom Bridge Networks
- Containers on the same custom bridge can communicate using container names.
- Preferred over default bridge in multi-container setups.
👉 Example:
```bash
docker network create mynet
docker run -d --name web --network mynet nginx
docker run -it --name alpine --network mynet alpine ping web
```
---

🔹 Host Network
- Removes container isolation → shares host’s network stack.
- No port mapping needed (container port = host port).
- Fast, but less isolation.
👉 Example:
```bash
docker run -d --network host nginx
```

🔹 None Network
- Container has no network access (loopback only).
- Good for security-sensitive tasks.
👉 Example:
```bash
docker run -d --network none nginx
```
---

🔹 Overlay Network
- Used in Docker Swarm / multi-host setups.
- Allows containers on different hosts to communicate securely.
- Requires Swarm mode:
```bash
docker swarm init
docker network create -d overlay myoverlay
```
---

🔹 Macvlan Network
- Assigns a container a MAC address so it looks like a real device on LAN.
- Useful when containers need to appear as independent machines.

---

🔹 Networking Commands
- List networks:
```bash 
docker network ls
```

- Inspect network:
```bash
docker network inspect mynet
```

- Connect/Disconnect container:
```bash
docker network connect mynet web
docker network disconnect mynet web
```

---

🧪 Examples (3 Levels)

✅ Beginner

- Run two containers in default bridge → test connectivity.
```bash
docker run -d --name web nginx
docker run -it --name test busybox ping web
```
👉 Fails by name in default bridge, but works by IP.

✅ Intermediate

- Create a custom bridge and run two containers.
```bash
docker network create appnet
docker run -d --name redis --network appnet redis
docker run -it --name alpine --network appnet alpine ping redis
```
👉 Works by container name thanks to embedded DNS.

✅ Advanced

- Run multi-container app with network alias.
```bash
docker network create backend
docker run -d --network backend --name db --network-alias mysql mysql:5.7
docker run -it --network backend alpine ping mysql
```
👉 Multiple aliases = flexible service naming.

---

💼 Mini Project: Flask + Redis App

Goal → Build a 2-container app that communicates over a custom bridge.

- 1.Run Redis:
```bash 
docker network create appnet
docker run -d --name redis --network appnet redis
```

- 2.Create simple app.py:
```bash
import redis
from flask import Flask
app = Flask(__name__)
r = redis.Redis(host='redis', port=6379)

@app.route('/')
def hello():
    r.incr('hits')
    return f"Hello! I have been seen {r.get('hits').decode()} times."
```

- 3.Dockerfile for Flask:
```bash
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

- 4.Run Flask on same network:
```bash
docker run -d -p 5000:5000 --name flask --network appnet flask-app
```
👉 Visit http://localhost:5000 → Flask talks to Redis by name.

---

🧠 Interview Tips

> Q: What is the default network type for Docker containers?
- A: Bridge.

> Q: Can containers talk to each other by name?
- A: Only in custom bridge or overlay networks. Default bridge requires IP.

> Q: Difference between host and bridge network?
- A: Bridge isolates; host shares host’s stack (better perf, less isolation).

> Q: When to use overlay network?
- A: Multi-host communication in Swarm.

> Q: What is embedded DNS in Docker?
- A: Docker provides built-in DNS so containers resolve each other by name in user-defined networks.

---

🪄 Hidden Gems

- Run docker network inspect bridge → see container IPs.
- Use --link (deprecated) for backward compatibility.
- You can connect one container to multiple networks.
- Logs may show DNS issues if service names don’t resolve → always prefer user-defined networks.

---

🏠 Homework (5 Tasks)

- 1.Run two containers in default bridge → test name vs IP resolution.
- 2.Create a custom bridge and connect two containers → ping by name.
- 3.Run a Redis + Alpine test client communicating via custom network.
- 4.Run an nginx container in host network mode and access it directly.
- 5.Create a network alias for MySQL and connect a test client by alias.

---

## 🌐 Phase 4 – Missing Pieces / Deep Dives

🔹 1. Ports vs Expose
- EXPOSE in Dockerfile = documentation only, does not publish ports.
- -p host:container (or --publish) → actually exposes port to host.
👉 Interview trick Q: “Does EXPOSE make the container accessible externally?” → No.

---

🔹 2. Publishing Ports (Differences)
- -p 8080:80 → maps host 8080 → container 80.
- -P (uppercase) → publishes all EXPOSEd ports to random host ports.
👉 Check mappings:
```bash
docker port <container>
```
---

🔹 3. Multi-Network Containers
- A single container can be attached to multiple networks.
👉 Example:
```bash
docker network create frontend
docker network create backend
docker run -d --name web --network frontend nginx
docker network connect backend web
```
- Now web can talk to both frontend and backend.

---

🔹 4. Network Isolation & Security
- By default, containers in the same custom network can talk freely.
- For production:
  - Use separate networks for frontend/backend.
  - Only connect services that need communication.
  - Consider firewalls/iptables for extra isolation.

---

🔹 5. Docker DNS Internals
- Each custom bridge/overlay comes with a built-in DNS server.
- Containers resolve names via /etc/resolv.conf.
- Docker maintains a DNS entry mapping container name → IP.
👉 Trick: Stop/start container → new IP, but DNS name stays same.

---

🔹 6. Inspecting Network Traffic
- You can run tcpdump inside a container or on the host bridge to debug.
👉 Example:
```bash
docker run -it --net=container:<id> nicolaka/netshoot
```
(netshoot image is a Swiss Army knife for container networking issues).

---

🔹 7. Network Drivers Beyond Defaults
- Plugins exist for:
  - Calico, Flannel, Weave (common in Kubernetes).
  - Software-defined networks (SDN).
👉 Not daily Docker usage, but interviewers may drop names.

---

🧠 Extra Interview Q&A

> Q: What’s the difference between EXPOSE and -p?
- A: EXPOSE only documents ports. -p actually publishes to the host.

> Q: Can a container be attached to multiple networks?
- A: Yes, using docker network connect.

> Q: How does Docker provide service discovery?
- A: Via embedded DNS, available in custom bridge and overlay networks.

> Q: If a container restarts, does its IP change?
- A: Yes, but DNS name remains the same, so service resolution still works.

> Q: How do you debug networking issues between containers?
- A: Use docker logs, docker inspect, ping, curl, or netshoot for tcpdump/nslookup.


