# Multi-stage builds
Multi-stage builds use multiple FROM statements. Each stage can have a different base image. You copy only what you need from earlier stages — keeping the FINAL image tiny and secure.

- Builder stage contains all tools required to build the source code
- Production stage: as tiny as possible (only code runtime)
- Less disk space & more secure
```dockerfile
# Stage 1: BUILD
FROM golang:1.21 AS builder

WORKDIR /app

COPY go.* .

RUN go mod download

COPY . .

RUN CGO_ENABLED=0 go build -o app

# Stage 2: PRODUCTION
FROM scratch # empty base!

COPY --from=builder /app/app /usr/local/bin/app

CMD ["app"]
```

# Common pipeline
1. Code committed to Github
2. Build a new version of docker image 
3. Run tests inside a test container
4. Scan image for vulnerabilities with (e.g., Trivy)
5. Push new version to registry (e.g., dockerhub)
6. Pull & deploy in staging/production


# Container Orchestration
When you have hundreds or thousands of containers across many servers, you need an orchestration platform to manage deployment, scaling, networking, health checks, and updates automatically.
e.g., Docker Swarm, Kubernetes, AWS ECS / Fargate

e.g.,
1. Restart when crash
2. Run new containers when traffic is more
3. Rolling updates without downtime
4. Load balancing
5. Service dicovery (App A finds App B)
6. Secrets & config management at scale


# Best Practices

Dockerfile
1. Use small base images
2. Use multi-stage builds
3. **Layer Ordering:** Always put the most "stable" instructions at the top.
	- Copy package files before source code (to be cached in future builds)
4. use COPY --link (independent of previous layers)
5. Pin exact versions in FROM (don't use latest)
6. use .dockerignore (.git/, .env, node_modules/, bin/, obj/ )

 Security
7. Don't run as root
8. Scan images
9. No hardcode secrets in images
10. Use Read-only filesystems when possible
11. Run with `--read-only`. This prevents attackers from downloading and running malware inside your container.
12. Regularly update base images
Images
13. Tag images with git SHA or SemVer (Semantic Versioning) is a 3-part versioning scheme (MAJOR.MINOR.PATCH)
14. Use private registries if needed
15. Clean up unused images
16. Use health checks in production
Runtime
17. Set CPU, memory limits
18. use restart: unless-stopped (to restart container if terminates unexpectedly)
19. Use environment variables for config
20. Log to stdout/stderr not files (files are deleted with container but logs persists)
21. Ensure your application handles `SIGTERM` (in order to do a graceful shutdown)