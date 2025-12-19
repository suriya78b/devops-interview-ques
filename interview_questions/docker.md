# 𝐃𝐨𝐜𝐤𝐞𝐫 & 𝐂𝐨𝐧𝐭𝐚𝐢𝐧𝐞𝐫𝐬
## 1. What is Docker?

Docker is an open-source platform for developing, shipping, and running applications inside lightweight, portable containers. Containers package an app with its dependencies (code, runtime, libraries) into a single executable unit, ensuring consistency across environments (dev, test, prod).
Key Components:

Docker Engine: Core runtime (daemon, CLI) that builds/runs containers.
Images: Read-only templates (e.g., ubuntu:24.04) from layers (union filesystem like AUFS/OverlayFS).
Containers: Runnable instances of images (isolated via namespaces/cgroups).
Docker Hub: Registry for sharing images.

Benefits:

Isolation: Apps run independently without VM overhead.
Portability: "Build once, run anywhere" (Linux/Windows/Mac via Docker Desktop).
Efficiency: Shares host kernel → Faster startup, lower resource use.

Install: curl -fsSL https://get.docker.com | sh. Basic command: docker run hello-world. Docker popularized containers but builds on Linux tech (LXC, libcontainer).
## 2. How do you reduce Docker image size and improve build time?

Smaller images mean faster pulls/deploys; quicker builds save CI time.
Reduce Size:

Minimal Base Images: Use alpine (5MB) over ubuntu (100MB+): FROM node:20-alpine.
Multi-Stage Builds: Compile in one stage, copy artifacts to slim runtime (see Q3).
Layer Optimization: Combine commands (e.g., RUN apt-get update && apt-get install -y pkg && rm -rf /var/lib/apt/lists/*) to minimize layers.
.dockerignore: Exclude junk (e.g., .git, node_modules).
Compress/Remove: Use docker-squash tool or docker build --squash.

Improve Build Time:

Cache Layers: Order Dockerfile: Static first (e.g., deps before code). Use --cache-from.
BuildKit: Enable DOCKER_BUILDKIT=1 docker build for parallel stages, better caching.
Remote Cache: Docker Buildx with registry cache: docker buildx build --push --cache-to=type=registry.
Minimize Copies: COPY package*.json . && npm ci before full source.

Tools: Dive/Trivy for layer analysis. Aim <100MB for microservices.
## 3. What are multi-stage builds and when do you use them?

Multi-stage builds allow multiple FROM statements in a Dockerfile, where each stage builds artifacts, and later stages copy only needed files from prior ones. This produces slim final images by discarding build tools.
When to Use:

Apps needing compilation (e.g., Go/Java) – Build in heavy stage, run in light.
Security: Exclude dev tools/secrets from runtime.
Efficiency: Smaller images for prod (e.g., 1GB build → 100MB runtime).

Example: See Q10. Use when build deps bloat the image (e.g., Node.js with npm build).
4. How do you troubleshoot container crashes (OOM, CPU spike, segfault)?

Crashes disrupt apps; debug systematically.
General Steps:

Logs: docker logs <container-id> (or --tail=100). For segfault: Check app errors.
Events: docker events --since=1h.

OOM (Out of Memory):

Symptoms: Exit code 137.
Debug: docker stats (memory usage); docker inspect --format '{{.State.OOMKilled}}' <id>.
Fix: Set --memory=1g in docker run; profile app (e.g., Java -Xmx).

CPU Spike:

Symptoms: High host CPU; slow responses.
Debug: docker top <id> (processes); htop inside (docker exec). Use strace for segfault.
Fix: Limit --cpus=2; profile (e.g., perf inside container).

Segfault:

Debug: docker run --cap-add=SYS_PTRACE + gdb/strace. Core dumps: Mount /proc/sys/kernel/core_pattern.
Tools: Sysdig/Falco for runtime monitoring. Reproduce locally with docker compose up.

## 5. What are Docker namespaces and cgroups?

Docker uses Linux kernel features for isolation.

Namespaces: Isolate processes (virtualize resources). Types:
PID: Separate process trees.
NET: Per-container networks.
MNT: Filesystem mounts.
UTS: Hostname/domain.
IPC: Inter-process comms.
USER: UID/GID mapping.
CGROUP: v2 unified.
Benefit: Containers see isolated views (e.g., ps shows only container procs).

Cgroups (Control Groups): Limit/monitor resource usage (CPU, memory, I/O).
Paths: /sys/fs/cgroup/docker/<id>/.
Example: Limit memory via cgroup files or Docker --memory.


Together: Namespaces for isolation, cgroups for quotas. Docker's libcontainer manages them.
## 6. Explain multi-stage Docker builds and why they matter.

(See Q3 for basics.) Multi-stage separates build/compile from runtime, reducing size/security risks.
Why Matter:

Size: Drop build tools (e.g., gcc/maven) → Faster pulls.
Security: No dev secrets in prod image.
Efficiency: Parallel builds with BuildKit.

Matters for CI/CD: Smaller registries, quicker deploys. Avoid for simple scripts.
## 7. How do you reduce Docker image size?

(See Q2 for full.) Key tactics: Alpine bases, multi-stage, cleanups (rm -rf /tmp/*), --no-cache sparingly, squash layers. Scan with docker scout or Dive. Goal: <500MB for apps.
## 8. What happens internally when a Docker container starts?

docker run triggers:

Image Pull: If not local, fetch from registry (layers via manifest).
Container Creation: Allocate ID, setup namespaces/cgroups (via libcontainer/runc).
Filesystem: Mount layers (OverlayFS) + volumes.
Network: Create veth pair, attach to bridge (or host/custom).
Process Start: Exec entrypoint/cmd as PID 1 (replaces init).
Signals/Health: Setup traps for SIGTERM; run healthchecks.

Internals: Docker daemon (dockerd) calls containerd/runc. View: docker inspect <id>.
## 9. Explain difference between ENTRYPOINT and CMD.

Both set container's default executable, but differ in overrides.

ENTRYPOINT: Defines the main command (e.g., ENTRYPOINT ["nginx"]). Args from docker run append. Immutable unless --entrypoint. Use for wrappers.
CMD: Provides default args (e.g., CMD ["-g", "daemon off;"]). Overridable by docker run args.

Combo:

ENTRYPOINT + CMD: CMD as default args to ENTRYPOINT.
JSON vs Shell: Prefer JSON for no shell.

Example: ENTRYPOINT for binaries, CMD for params.
## 10. Explain Docker multi-stage builds with an example.

(See Q3/6.) Example Go app:
dockerfile# Build stage
FROM golang:1.23 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Runtime stage
FROM alpine:3.20
WORKDIR /app
COPY --from=builder /app/myapp .
CMD ["./myapp"]
Build: docker build -t myapp .. Size: ~10MB vs. 800MB single-stage.
## 11. How do you troubleshoot high CPU usage in a container?

(See Q4 for similar.) Steps:

docker stats/top: Identify spikes.
docker exec -it <id> /bin/sh: Run top/htop inside.
Profile: App-specific (e.g., Node --inspect).
Limits: Set --cpus=1.5; monitor with Prometheus.
Logs: docker logs --tail=100. Fix: Optimize code, scale horizontally.

## 12. Best practices for production-ready Dockerfiles?


Minimal Bases: Alpine/slim.
Non-Root User: USER appuser.
Healthchecks: HEALTHCHECK CMD curl -f http://localhost/health.
Env Vars: Use --env-file; no hardcodes.
Multi-Stage: For builds.
Scan: docker scout cve.
Labels: LABEL maintainer="team@company.com".
Immutable Tags: Avoid latest; use digests.

Lint with Hadolint; sign with Cosign.
## 13. How to handle environment variables securely?

Avoid baking in images (visible in layers).

docker run --env: docker run -e DB_PASS=secret.
Env Files: --env-file=.env (gitignore).
Secrets: docker secret create (Swarm) or mounts (--secret id=pass,source=pass.txt). In Dockerfile: RUN --mount=type=secret,id=pass.
Vault/SSM: Fetch at runtime (e.g., entrypoint script).

Best: No plaintext; rotate via orchestrators (Kubernetes Secrets).
## 14. What happens internally when you run docker run?

(See Q8 for start.) Full: CLI → Daemon API → Containerd → Runc exec. Pulls image, creates container (namespaces/cgroups), mounts, networks, starts PID 1. Errors: Check daemon logs (journalctl -u docker).
