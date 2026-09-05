# Docker Security

Docker containers provide process and filesystem isolation, but they are **not automatically secure simply because they are containers**.

Security should be considered at the image, container, host, network, registry, and secret-management levels.

---

## Run as a Non-root User

Avoid running applications as root where possible.

Dockerfile:

```dockerfile
RUN useradd --create-home appuser

USER appuser
```

---

## Use Trusted Base Images

Prefer:

* Official images
* Verified publishers
* Minimal images
* Pinned versions
* Regularly updated images

Avoid blindly using arbitrary images from public registries.

---

## Don't Store Secrets in Images

Never do:

```dockerfile
ENV API_KEY=secret-value
```

or:

```dockerfile
COPY .env /app/.env
```

Secrets can remain in image layers even if later deleted.

---

## Environment Variables

Environment variables are useful for configuration:

```bash
docker run \
  -e APP_ENV=production \
  my-app
```

But environment variables are not always an appropriate secret-management mechanism.

For sensitive production secrets, use a proper secret-management solution.

---

## Read-only Filesystem

Where possible:

```bash
docker run \
  --read-only \
  my-app
```

If temporary storage is required, provide an explicit writable location.

---

## Drop Capabilities

Linux capabilities can be reduced:

```bash
docker run \
  --cap-drop=ALL \
  my-app
```

Only add required capabilities.

---

## Avoid Privileged Containers

Avoid:

```bash
docker run --privileged ...
```

unless there is a well-understood requirement.

Privileged containers significantly increase the container's access to the host.

---

## Resource Limits

Limit resources to reduce denial-of-service risk:

```bash
docker run \
  --memory 512m \
  --cpus 1 \
  my-app
```

---

## Network Exposure

Don't expose ports unnecessarily.

Instead of:

```bash
-p 8080:80
```

consider:

```bash
-p 127.0.0.1:8080:80
```

when the service should only be accessible locally.

---

## Image Scanning

Images should be scanned for known vulnerabilities.

The exact scanning tool depends on your environment and CI/CD platform.

A typical workflow is:

```text
Build
  │
  ▼
Scan
  │
  ├── PASS ──► Push
  │
  └── FAIL ──► Fix
```

---

## `.dockerignore`

Prevent secrets and unnecessary files from entering the build context:

```text
.git
.env
.venv
__pycache__
*.pem
*.key
```

---

## Docker Socket

Be extremely careful with:

```text
/var/run/docker.sock
```

Giving a container access to the Docker socket can effectively give it control over the Docker host.

Avoid mounting it unless absolutely necessary.

---

## Security Checklist

* Use trusted base images
* Keep images updated
* Run as non-root
* Avoid `--privileged`
* Drop unnecessary capabilities
* Minimize exposed ports
* Don't bake secrets into images
* Use `.dockerignore`
* Scan images
* Apply resource limits
* Protect the Docker socket
* Use immutable image references where appropriate
* Keep Docker Engine and Docker Desktop updated