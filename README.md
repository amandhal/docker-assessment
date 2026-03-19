# Docker Assessment

#### Differences between image vs container vs volume vs network in Docker.
- Docker image is a read-only template used to create containers. It contains application code, dependencies, libraries, and configurations. Images are built using a Dockerfile.
- Container is a running instance of an image.
- Volume is used to persist data. Volumes retain data even if the container is removed. 
- Docker network is used to enable communication between containers.

#### How to clean up unused Docker resources.
- This removes all unused containers, networks and image.
```bash
docker system prune -a
```

- This removes all unused volumes.
```bash
docker volumes prune -a
```

#### Best practices for writing secure Dockerfiles.
1. Use Trusted & Minimal Base Images
2. Avoid Running as Root to limit damage if container gets compromised
3. Use multi-stage builds to keep images small
4. Use .dockerignore to prevent sensitive or unnecessary files from being copied inside image
5. Pin Image Versions to avoid compatibility issues
6. Scan Images for Vulnerabilities
7. Use Read-Only Filesystem

#### Dockerfile
```dockerfile
# -------- STAGE 1: Builder --------
FROM python:3.12.3-slim AS builder

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy only requirements first (for caching)
COPY requirements.txt .

# Install dependencies into a separate folder
RUN pip install --no-cache-dir --prefix=/install -r requirements.txt

# -------- STAGE 2: Final Runtime --------
FROM python:3.12.3-slim

WORKDIR /app

# Create non-root user
RUN useradd -m appuser

# Copy dependencies from builder
COPY --from=builder /install /usr/local

# Copy app code
COPY . .

# Set ownership
RUN chown -R appuser:appuser /app

# Switch to non-root
USER appuser

EXPOSE 5000

CMD ["python", "app.py"]
```

#### .dockerignore
```
__pycache__/
*.pyc
*.pyo
*.pyd
.env
.git
.gitignore
README.md
```

