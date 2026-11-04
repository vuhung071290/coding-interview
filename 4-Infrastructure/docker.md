# Docker for Backend Engineers

> Container fundamentals cho production-ready backend systems

## Tại sao Docker quan trọng
- **Consistency:** "Works on my machine" → build once, run anywhere
- **Isolation:** mỗi service có dependencies riêng, không conflict
- **Deployment:** CI/CD pipeline chuẩn — build image → push registry → deploy
- **Interview:** 90%+ senior JD yêu cầu Docker; system design hay hỏi containerization strategy

---

# 1. Core Concepts

## Container vs VM
- **VM:** full OS, nặng (~GBs), startup minutes
- **Container:** share host kernel, nhẹ (~MBs), startup seconds
- **Key insight:** Container = isolated process, không phải mini-VM

## Docker Architecture
- **Docker Engine:** daemon (server) + CLI (client) + REST API
- **Image:** read-only template (layers) — immutable
- **Container:** running instance of image — mutable layer on top
- **Registry:** lưu trữ images (Docker Hub, ECR, GCR, Harbor)

## Layer System
- Mỗi `RUN/COPY/ADD` instruction → 1 layer mới
- Layer được **cache** nếu instruction không thay đổi
- **Tối ưu:** instructions ít thay đổi đặt trước (base image, dependencies), code đặt cuối

---

# 2. Dockerfile Best Practices

## Key Instructions
- `FROM` — base image (use `eclipse-temurin:21-jre-alpine` for Java runtime)
- `WORKDIR` — set working directory
- `COPY` — copy files (prefer over `ADD`)
- `RUN` — execute commands during build
- `EXPOSE` — document port (informational only)
- `USER` — switch to non-root user (security!)
- `ENTRYPOINT` — main command (not overridable)
- `CMD` — default arguments (overridable)

## Best Practices
- **Minimal base image:** `alpine` or `distroless` → smaller attack surface, fewer CVEs
- **Non-root user:** `USER nobody` or dedicated `appuser` — security best practice
- **Layer cache optimization:** copy `pom.xml` + download deps first, copy source code last
- **COPY not ADD:** `ADD` has hidden features (URL download, tar extract) — use explicitly
- **ENV vs ARG:** `ENV` persists in runtime; `ARG` build-time only
- **.dockerignore:** exclude `.git`, `target/`, `*.md` to reduce build context size
- **Single process per container:** one concern per container

## Multi-stage Build (critical for Java)
Stage 1 (builder): uses JDK + Maven/Gradle → compiles application
Stage 2 (runtime): uses JRE-only base image → copies built artifact only

Result: final image has no JDK, no Maven, no source code → 60–80% size reduction

Pattern:
- Stage 1: copy `pom.xml`, download deps (cached layer), copy source, build
- Stage 2: `COPY --from=builder /app/target/app.jar app.jar` → lean runtime image

---

# 3. Docker Compose (Local Development)

## Purpose
- Chạy multi-container apps locally (app + DB + Redis + Kafka)
- Không dùng Kubernetes cho dev environment

## Key Concepts
- `services` — each service gets a name, image/build, ports, env
- `depends_on` + `healthcheck` — đảm bảo thứ tự khởi động (wait for DB healthy, not just started)
- `networks` — containers cùng compose file tự thấy nhau qua service name (DNS)
- `volumes` — named volumes: data persist qua container restart

## Important Config Points
- `condition: service_healthy` in `depends_on` → wait for actual readiness, not just container start
- Named volumes for DB data: `pgdata:/var/lib/postgresql/data`
- Environment variables: `SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/mydb` (service name as host)

---

# 4. Networking & Volumes

## Network Drivers
- **bridge (default):** isolated network, containers communicate by name
- **host:** dùng host network trực tiếp — không khuyến khích (security)
- **overlay:** multi-host networking cho Swarm/K8s
- **none:** hoàn toàn isolated

## Volume Types
- **Named volume:** `pgdata:/var/lib/postgresql/data` → Docker quản lý, persist across restarts
- **Bind mount:** `./src:/app/src` → map thư mục host vào container (dev hot reload)
- **tmpfs:** lưu trong RAM, không persist — secrets, temp data

---

# 5. Container Registry & Image Management

## Common Registries
- **Docker Hub:** public, free tier
- **AWS ECR:** tích hợp tốt với EKS/ECS, IAM auth
- **GCR / Artifact Registry:** Google Cloud
- **Harbor:** self-hosted, enterprise features (RBAC, vulnerability scanning)

## Image Tagging Strategy
- `myapp:latest` — KHÔNG dùng trong production! (mutable, unpredictable)
- `myapp:1.2.3` — semantic version — stable
- `myapp:1.2.3-abc1234` — version + git commit — fully traceable
- `myapp:main-20261104` — branch + date

**Production rule:** dùng immutable tags (commit SHA hoặc semantic version), không dùng `latest`

---

# 6. Security

## Common Issues
- Running as root (default) → privilege escalation risk
- Storing secrets in image layers (visible in `docker history`)
- Using outdated base images với CVEs
- Too-large images với unnecessary tools

## Security Best Practices
- **Non-root user:** `USER nobody` hoặc tạo dedicated user
- **Secrets:** Docker secrets hoặc env vars từ secret manager, không bake vào image
- **Image scanning:** Trivy, Snyk, AWS ECR scanning — detect CVEs in dependencies + base image
- **Read-only filesystem:** `--read-only` flag khi có thể
- **Minimal base:** `distroless` hoặc `alpine` — ít packages = ít vulnerabilities
- **Regular rebuilds:** rebuild images khi base image có security patch

---

# 7. Performance & Production Tips

## JVM in Container
- Java 11+: auto-detects container memory limits via cgroups
- Recommended: `-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0`
- Set `-Xms = -Xmx` to avoid heap resizing overhead
- Never set heap larger than container memory limit

## Health Check in Dockerfile
- `HEALTHCHECK --interval=30s --timeout=3s --retries=3 CMD wget -q --spider http://localhost:8080/actuator/health || exit 1`
- Integrates with Docker Compose `condition: service_healthy`

## Useful Commands (reference)
- `docker build -t myapp:latest .` — build image
- `docker run -p 8080:8080 --env-file .env myapp:latest` — run container
- `docker exec -it <container> sh` — debug shell into container
- `docker logs -f <container>` — follow logs
- `docker stats` — resource usage (CPU, memory)
- `docker system prune -a` — clean up unused images/containers
- `docker inspect <container>` — view full config

---

# 8. Interview Topics

## Câu hỏi thường gặp
- "ENTRYPOINT vs CMD?" → ENTRYPOINT: main command (not overridable at run); CMD: default args (overridable)
- "Làm sao giảm Docker image size?" → Multi-stage build, alpine base, .dockerignore, merge RUN commands
- "Container stateless hay stateful?" → Container nên stateless; state → external volumes hoặc external DB
- "Làm sao debug container không start được?" → `docker logs`, `docker inspect`, `docker run -it --entrypoint sh`
- "Docker vs VM trong microservices?" → Container: nhẹ hơn, start nhanh, share kernel → suitable cho microservices

## Cho System Design
- Mỗi microservice → 1 container → 1 image
- Images stored in private registry (ECR)
- Container = immutable unit of deployment
- Config injected qua environment variables hoặc secret manager
