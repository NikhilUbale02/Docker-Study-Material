💾 Phase 3: Docker Volumes & Data Persistence

🎯 Objectives

- By the end of this phase, you should:
- Understand why containers need persistent storage.
- Differentiate bind mounts vs named volumes.
- Learn how to create, use, inspect, and share volumes.
- Backup & restore volumes.
- Manage volume lifecycle and disk usage.

---

📘 Concepts

🔹 Ephemeral Nature of Containers
- By default, container data exists only as long as the container runs.
- Once removed → data is lost (stateless).
- Solution: Docker Volumes.

---

🔹 Types of Storage in Docker

- 1.Bind Mounts
- Maps a host machine directory → container directory.
- Direct access to host filesystem.
- Useful for dev environments (hot reload).
- Example:
```bash
docker run -v /host/path:/container/path nginx
```

- 2.Named Volumes
- Managed by Docker under /var/lib/docker/volumes/.
- Independent of host path.
- Portable & safer (decoupled from host).
- Example:
```bash
docker volume create myvol
docker run -v myvol:/app/data nginx
```

- 3.tmpfs Mounts (Linux-only)
- Stores data in host memory, not on disk.
- Fast but non-persistent (data gone after restart).
- Example:
```bash
docker run --mount type=tmpfs,destination=/app/tmp tmp-container
```

---

🔹 Managing Volumes

- List: 
```bash
docker volume ls
```

- Inspect:
```bash
docker volume inspect myvol
```

- Remove unused:
```bash
docker volume prune
```

---

🧪 Examples (3 Levels)

✅ Beginner
- Run nginx with bind mount:
```bash
docker run -d -p 8080:80 -v $(pwd):/usr/share/nginx/html nginx
```

✅ Intermediate
- Named volume for MySQL:
```bash
docker volume create mysql_data
docker run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=pass \
  -v mysql_data:/var/lib/mysql \
  mysql:5.7
```
- Restart container → data persists.

✅ Advanced
- Share volume between two containers:
```bash
docker run -d --name app1 -v sharedvol:/data busybox sleep 1000
docker run -it --name app2 -v sharedvol:/data busybox sh
```
- Now both containers can see /data.

---

💼 Mini Project: Persistent MySQL Database

- 1.Create a volume:
```bash
docker volume create db_data
```

- 2.Run MySQL with volume:
```bash
docker run -d --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=pass \
  -v db_data:/var/lib/mysql \
  mysql:5.7
```

- 3.Insert some rows.
- 4.Stop & remove container.
- 5.Start new MySQL container with same volume → data persists.

---

🧠 Interview Tips

> Q: Difference between bind mount and volume?
- A: Bind mount = host directory mapping. Volume = managed by Docker in /var/lib/docker/volumes/. Volumes are safer and portable.

> Q: Can you share volumes between containers?
- A: Yes, multiple containers can mount the same volume.

> Q: How to backup a Docker volume?
- A: docker run --rm -v myvol:/data -v $(pwd):/backup busybox tar cvf /backup/backup.tar /data

> Q: What is tmpfs mount?
- A: A memory-only mount (fast, non-persistent).

> Q: What happens to volumes when you remove a container?
- A: By default, volumes are preserved unless explicitly removed (docker rm -v).

---

🪄 Hidden Gems

- Volumes can be mounted read-only:
```bash
docker run -v myvol:/data:ro nginx
```

- You can mount files instead of dirs (good for config files).
- Named volumes are useful in Docker Compose.

--- 

🏠 Homework (5 Tasks)

- 1.Run nginx with bind mount to serve custom HTML from your host.
- 2.Create a named volume and attach it to a container.
- 3.Run PostgreSQL with a named volume → stop & start again → confirm persistence.
- 4.Share a volume between two containers and write/read files.
- 5.Backup and restore a volume using tar.

---

💾 Phase 3 – Missing Pieces / Deep Dives

---

🔹 1. Volume Lifecycle & Orphaned Volumes.

- Volumes persist even if the container is deleted.
- If you run docker rm <container> without -v, the volume stays.
- Over time → orphaned volumes can eat disk space.
👉 Clean with:
```bash
docker volume prune
```

🔹 2. --mount vs -v

- Two ways to mount storage in containers.
- -v is shorthand, --mount is explicit & recommended.
👉 Example:
```bash
# Shorthand
docker run -v myvol:/data nginx

# Explicit mount
docker run --mount type=volume,src=myvol,dst=/data nginx
```
- --mount is clearer & more flexible (works with volume, bind, tmpfs).

🔹 3. Volume Drivers

- Docker supports external volume drivers.
- Allows integration with cloud storage or plugins (e.g., AWS EBS, Azure Disk, NFS).
👉 Example:
```bash 
docker volume create \
  --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.100,rw \
  --opt device=:/path/to/dir \
  nfsvolume
```
- Real-world use case: Persist DB data on shared NFS/EBS.

🔹 4. Anonymous Volumes

- If you mount without naming:
```bash
docker run -v /data busybox
```
- → Docker creates an anonymous volume with random hash.
- Can clutter system if not managed.

🔹 5. Volume Backups & Migration (Production Case)

- Backup:
```bash
docker run --rm -v myvol:/data -v $(pwd):/backup busybox tar cvf /backup/vol_backup.tar /data
```

- Restore:
```bash
docker run --rm -v myvol:/data -v $(pwd):/backup busybox tar xvf /backup/vol_backup.tar -C /data
```
- Interview Curveball: "How do you migrate Docker volume data between environments?" → This is the answer.

🔹 6. Data-Only Containers (Old but asked in interviews)

- Before named volumes, people used data-only containers.
```bash
docker create -v /data --name data-container busybox
docker run --volumes-from data-container nginx
```
- Rarely used now, but worth knowing for legacy interview Qs.

---

🧠 Extra Interview Q&A

> Q: What’s the difference between -v and --mount?
- A: -v is older shorthand, less clear. --mount is explicit and supports volume, bind, tmpfs.

> Q: What happens to a volume if you delete its container?
- A: Volume persists unless removed with -v or docker volume rm.

> Q: What are orphaned volumes?
- A: Volumes not attached to any container. They consume disk space until pruned.

> Q: What are volume drivers?
- A: Plugins that let Docker store volumes on external storage (NFS, AWS EBS, Azure Disk, etc.).

> Q: What is an anonymous volume?
- A: A volume created automatically without a name. Harder to manage, often leads to clutter.

> Q: How do you migrate Docker volume data?
- A: Use docker run with tar to backup/restore between environments.
