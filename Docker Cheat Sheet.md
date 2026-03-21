# 🐳 Docker Cheat Sheet

**Docker** packages applications as **images** and runs them in isolated **containers**. This sheet covers daily CLI commands, **Dockerfile** instructions, **Compose** keys, and practical tips for dev and CI/CD.

---

## 📥 Installation

| Option | Use case | Link |
| :--- | :--- | :--- |
| **Docker Engine** | Linux servers / VMs (daemon + CLI) | [Install Docker Engine](https://docs.docker.com/engine/install/) |
| **Docker Desktop** | Linux, macOS, Windows (GUI + Engine + Compose + Kubernetes toggle) | [Docker Desktop](https://docs.docker.com/desktop/) |

> [!NOTE]
> On Linux servers you typically install **Engine** only; **Desktop** bundles extra tooling for local development.

---

## 📦 Container Commands

| Command | Description |
| :--- | :--- |
| `docker run <image>` | Create and start a new container from an image |
| `docker run -p 8080:80 <image>` | Publish container port **80** to host **8080** (`host:container`) |
| `docker run -d <image>` | Run in **detached** (background) mode |
| `docker run --name myapp <image>` | Assign a friendly container name |
| `docker run -v /host/path:/container/path <image>` | Bind-mount a host directory into the container |
| `docker run -e KEY=value <image>` | Set an environment variable |
| `docker run --rm <image>` | Remove container automatically when it exits |
| `docker ps` | List **running** containers |
| `docker ps -a` | List **all** containers (running + stopped) |
| `docker logs <container>` | Show container logs (stdout/stderr) |
| `docker logs -f <container>` | **Follow** logs (like `tail -f`) |
| `docker logs --tail 100 <container>` | Show last N lines |
| `docker stop <container>` | Stop a running container (SIGTERM, then SIGKILL) |
| `docker kill <container>` | Force stop (SIGKILL) |
| `docker start <container>` | Start a **stopped** container |
| `docker restart <container>` | Restart a container |
| `docker rm <container>` | Remove a **stopped** container |
| `docker rm -f <container>` | Force remove a **running** container |

### Handy extras

| Command | Description |
| :--- | :--- |
| `docker inspect <container\|image>` | JSON metadata (IPs, mounts, env, labels) |
| `docker cp <container>:/path /host` | Copy files between container and host (both directions) |
| `docker stats` | Live CPU/memory/network for running containers |
| `docker top <container>` | List processes inside a container |

---

## 🖥️ Run Commands Inside a Container

| Command | Description |
| :--- | :--- |
| `docker exec <container> <command>` | Run a command in a **running** container |
| `docker exec -it <container> bash` | Interactive shell (`bash` or `sh` if minimal image) |
| `docker exec -u 0 -it <container> sh` | Exec as root (debug only) |

> [!TIP]
> Prefer `docker exec` over SSH into the host to debug—keeps changes inside the container boundary.

---

## 🖼️ Image Commands

| Command | Description |
| :--- | :--- |
| `docker build -t myimg:1.0 .` | Build an image from a `Dockerfile` in the current directory |
| `docker build -t myimg:1.0 -f Dockerfile.prod .` | Use a non-default Dockerfile path |
| `docker images` | List local images |
| `docker image ls` | Same as `docker images` |
| `docker rmi <image>` | Remove an image (no containers using it) |
| `docker image rm <image>` | Same as `docker rmi` |
| `docker image prune` | Remove **dangling** images |
| `docker tag <source> <target>` | Tag an image for a registry (`registry/repo:tag`) |

---

## 🌐 Registry Commands

| Command | Description |
| :--- | :--- |
| `docker login` | Log in to Docker Hub (default registry) |
| `docker login <registry>` | Log in to a specific registry (e.g. `ghcr.io`) |
| `docker logout` | Log out from default registry |
| `docker logout <registry>` | Log out from a specific registry |
| `docker push <image>` | Push an image to a registry |
| `docker pull <image>` | Pull an image from a registry |
| `docker search <term>` | Search Docker Hub (limited; many teams use registry UI or catalog APIs) |

---

## ⚙️ System / Cleanup Commands

| Command | Description |
| :--- | :--- |
| `docker system df` | Show disk usage (images, containers, volumes, build cache) |
| `docker system prune` | Remove unused **containers**, **networks**, and dangling images |
| `docker system prune -a` | Also remove **all** unused images (not used by any container) |
| `docker system prune --volumes` | **Danger:** may delete unused **named volumes** (data loss) |
| `docker builder prune` | Clear **build cache** |

> [!WARNING]
> **`docker system prune -a`** and **`--volumes`** can delete data you still need. Review with `docker system df` first; avoid on shared CI runners without policies.

---

## 🧩 Docker Compose (v2 plugin: `docker compose`)

Modern Docker includes the **Compose V2** plugin (`docker compose`). The legacy standalone binary was `docker-compose`.

| Command | Description |
| :--- | :--- |
| `docker compose up` | Create and start containers (foreground logs) |
| `docker compose up -d` | Start in **detached** mode |
| `docker compose up --build` | Build images then start |
| `docker compose stop` | Stop containers (keep them) |
| `docker compose down` | Stop and **remove** containers + default network |
| `docker compose down -v` | Also remove **volumes** declared in the file (data loss risk) |
| `docker compose ps` | List compose project containers |
| `docker compose logs` | Logs for all services |
| `docker compose logs <service>` | Logs for one service |
| `docker compose logs -f` | Follow all service logs |
| `docker compose pull` | Pull images for services |
| `docker compose build` | Build images for services with `build:` |
| `docker compose build --pull` | Pull newer base images before build |
| `docker compose exec <service> sh` | Exec into a running service container |
| `docker compose run <service> <cmd>` | Run a **one-off** command in a new container |

### Scaling (CLI)

| Command | Description |
| :--- | :--- |
| `docker compose up -d --scale web=3` | Run **3** instances of service `web` (ports need handling) |

> [!NOTE]
> **File name:** `compose.yaml` (or `compose.yml`) is preferred today; `docker-compose.yaml` still works for compatibility.

---

## 📄 Dockerfile Instructions

| Instruction | Description |
| :--- | :--- |
| `FROM <image>` | Base image for the build |
| `FROM <image> AS <name>` | Name a **build stage** (multi-stage builds) |
| `RUN <command>` | Execute a shell command during build |
| `RUN ["exec", "param1", "param2"]` | **Exec form** (JSON array, no shell) |
| `CMD ["exec", "param1"]` | Default command for the container (**replaceable** by `docker run ...`) |
| `ENTRYPOINT ["exec", "param"]` | Main executable; `CMD` supplies default args |
| `ENV KEY=value` | Set environment variable (also available at build time) |
| `EXPOSE <port>` | **Documentation** only; publish with `-p` / Compose `ports` |
| `COPY <src> <dest>` | Copy files from build context into image |
| `COPY --from=<stage> <src> <dest>` | Copy from another build stage |
| `ADD <src> <dest>` | Like `COPY` + URLs and auto-extract tar (prefer `COPY` when possible) |
| `WORKDIR <path>` | Set working directory for subsequent instructions |
| `VOLUME <path>` | Declare a mount point (anonymous volume by default) |
| `USER <user[:group]>` | Run as non-root user |
| `ARG NAME[=default]` | Build-time variable (`docker build --build-arg`) |
| `LABEL key=value` | Image metadata (OCI labels) |
| `HEALTHCHECK CMD ...` | Container health check for orchestrators |

**Full reference:** [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)

### Minimal multi-stage example

```dockerfile
# Build stage
FROM golang:1.22-alpine AS build
WORKDIR /src
COPY . .
RUN go build -o /out/app .

# Runtime stage
FROM gcr.io/distroless/static-debian12:nonroot
COPY --from=build /out/app /app
USER nonroot:nonroot
ENTRYPOINT ["/app"]
```

> [!TIP]
> Add a **`.dockerignore`** (like `.gitignore`) to keep build context small and avoid leaking secrets into images.

---

## 🔑 Docker Compose File Keys (`compose.yaml`)

Top-level and common `services.<name>.*` fields:

| Key | Description |
| :--- | :--- |
| `name` | **Project name** (overrides directory name) |
| `services` | Map of service definitions |
| `services.<name>.image` | Image to run (if not building) |
| `services.<name>.build` | Build configuration (object or context path string) |
| `services.<name>.build.context` | Build context directory (often `.`) |
| `services.<name>.build.dockerfile` | Dockerfile path (default `Dockerfile`) |
| `services.<name>.build.target` | Multi-stage **target** stage name |
| `services.<name>.build.args` | Build-args (`ARG` values) |
| `services.<name>.command` | Override image `CMD` |
| `services.<name>.entrypoint` | Override image `ENTRYPOINT` |
| `services.<name>.volumes` | Bind mounts and named volumes |
| `services.<name>.ports` | Publish ports (`"8080:80"`, `127.0.0.1:8080:80`) |
| `services.<name>.environment` | Environment variables (map or list `KEY=value`) |
| `services.<name>.env_file` | Load variables from a file |
| `services.<name>.restart` | `no`, `always`, `on-failure`, `unless-stopped` |
| `services.<name>.depends_on` | Startup ordering (**does not wait for “healthy”** unless conditions used) |
| `services.<name>.healthcheck` | Same idea as Dockerfile `HEALTHCHECK` |
| `services.<name>.networks` | Attach service to networks |
| `services.<name>.labels` | Container labels |
| `networks` | User-defined networks |
| `networks.<name>.driver` | e.g. `bridge`, `overlay` (Swarm) |
| `networks.<name>.external` | Use an existing network (`true` or `name: other`) |
| `volumes` | Named volume definitions |
| `volumes.<name>.name` | Explicit volume name |
| `volumes.<name>.driver` | Volume driver |
| `configs` | Swarm-style configs (or Compose configs feature) |
| `secrets` | Swarm secrets / file-based secrets mapping |

### `depends_on` + health (Compose spec)

```yaml
services:
  web:
    depends_on:
      db:
        condition: service_healthy
  db:
    image: postgres:16
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $$POSTGRES_USER"]
      interval: 5s
      timeout: 5s
      retries: 5
```

> [!NOTE]
> **`depends_on` alone** only controls **create order**, not “database is ready.” Use **healthchecks** + `condition` for reliable startups.

---

## 📋 Example `compose.yaml`

> [!NOTE]
> Prefer **`compose.yaml`** today; **`docker-compose.yaml`** remains valid for older workflows.

```yaml
name: demo

services:
  service1:
    image: myapp:latest
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - ./:/code:ro
    ports:
      - "8000:80"
    environment:
      KEY: value
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
```

---

## 📌 Quick Reference

| Task | Command |
| :--- | :--- |
| Run interactively | `docker run -it --rm ubuntu bash` |
| Background + ports | `docker run -d -p 8080:80 --name web nginx` |
| Shell into running box | `docker exec -it <name> sh` |
| Compose up | `docker compose up -d` |
| Rebuild + up | `docker compose up -d --build` |
| Clean slate (careful) | `docker compose down -v` |

---

## 🔗 Official Docs

| Topic | URL |
| :--- | :--- |
| Dockerfile | [reference/builder](https://docs.docker.com/engine/reference/builder/) |
| Compose file | [Compose specification](https://docs.docker.com/compose/compose-file/) |
| CLI | [docker CLI](https://docs.docker.com/engine/reference/commandline/docker/) |

---

*Prefer **pinned image digests** in production, scan images in CI, and run as **non-root** whenever possible.*
