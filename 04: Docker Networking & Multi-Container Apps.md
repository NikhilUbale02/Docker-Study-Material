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

