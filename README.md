# Docker Assessment

### Differences between image vs container vs volume vs network in Docker.
- Docker image is a read-only template used to create containers. It contains application code, dependencies, libraries, and configurations. Images are built using a Dockerfile.
- Container is a running instance of an image.
- Volume is used to persist data. Volumes retain data even if the container is removed. 
- Docker network is used to enable communication between containers.

---

### How to clean up unused Docker resources.
- This removes all unused containers, networks and image.
```bash
docker system prune -a
```

---

- This removes all unused volumes.
```bash
docker volumes prune -a
```

---

### Best practices for writing secure Dockerfiles.
1. Use Trusted & Minimal Base Images
2. Avoid Running as Root to limit damage if container gets compromised
3. Use multi-stage builds to keep images small
4. Use .dockerignore to prevent sensitive or unnecessary files from being copied inside image
5. Pin Image Versions to avoid compatibility issues
6. Scan Images for Vulnerabilities
7. Use Read-Only Filesystem

---

### .dockerignore
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

---

### Dockerfile
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

---

#### Build & Push image to Docker Hub
```bash
docker build -t amandhal/flask-lms:1.0.0 -t amandhal/flask-lms:latest .
docker push amandhal/flask-lms --all-tags
```
<img width="915" height="688" alt="image" src="https://github.com/user-attachments/assets/deabffc3-60fb-46b9-b5b0-86db859cd13e" />

---

#### Image Scanning using Trivy
```bash
trivy image amandhal/flask-lms:1.0.0
```
<img width="1568" height="910" alt="image" src="https://github.com/user-attachments/assets/1c01140f-748d-4f37-8996-2552596d0247" />

---

#### Configure container with resource limits & read-only filesystem
```bash
docker run -d --name test-limit-ro --memory="128m" --cpus="0.5" --read-only busybox sleep infinity
docker inspect test-limit-ro | grep -i cpu
docker inspect test-limit-ro | grep -i memory
```
<img width="1918" height="589" alt="image" src="https://github.com/user-attachments/assets/59d1fc63-0b65-4ceb-b02b-4d614aa3754f" />
