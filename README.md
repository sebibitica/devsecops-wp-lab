# DevSecOps WordPress Lab

Automated vulnerability discovery and remediation pipeline for a containerized WordPress deployment — **Docker** + **WPScan** + **GitHub Actions**.

## What the pipeline does

Every push to `main` triggers `.github/workflows/scan.yml`:

1. `docker compose up -d --build` — builds the image from `docker/Dockerfile` and starts MySQL + WordPress
2. Waits for WordPress to respond
3. Runs **WPScan**, saves output to `scans/scan-<short-sha>.txt`
4. Uploads the scan as a workflow artifact
5. **Only if all the above succeeded:** logs into Docker Hub + GHCR, pushes the image as `:latest` and `:sha-<short-sha>`
6. Commits the scan file back to `/scans/` and tears down the stack

To remediate: edit `docker/Dockerfile`, commit, push. Each commit produces a new image + scan. `git log` + the `/scans/` folder show the progression.

## Repository layout

```
devsecops-wp-lab/
├── README.md
├── .env.example                     # template for local DOCKERHUB_USERNAME + TAG
├── docker/
│   ├── Dockerfile                   # edit this to harden
│   └── docker-compose.yml           # hybrid build+image, used locally AND in CI
├── scans/
│   └── scan-<short-sha>.txt         # committed automatically by CI
└── .github/workflows/scan.yml
```

## One-time setup

### GitHub repo secrets (Settings → Secrets and variables → Actions)

| Secret                | Value |
|-----------------------|-------|
| `DOCKERHUB_USERNAME`  | Your Docker Hub user |
| `DOCKERHUB_TOKEN`     | Docker Hub access token |
| `WPSCAN_API_TOKEN`    | Optional, from wpscan.com — enables richer CVE data |

`GITHUB_TOKEN` is auto-provided for the GHCR push.

### Workflow permissions

Settings → Actions → General → **Workflow permissions** → "Read and write permissions" (lets the workflow commit scans back).

### Docker Hub

Create a public repo named `devsecops-wp-lab` on hub.docker.com.

## Running locally

```bash
cp .env.example .env
# edit DOCKERHUB_USERNAME=<yours>
docker compose --env-file .env -f docker/docker-compose.yml up -d --build
```

Then open http://localhost:8080 in a browser (or `curl.exe -I http://localhost:8080` on Windows PowerShell, `curl -I http://localhost:8080` on Linux/macOS).

## Comparing scans

```bash
diff scans/scan-<old-sha>.txt scans/scan-<new-sha>.txt | less
```