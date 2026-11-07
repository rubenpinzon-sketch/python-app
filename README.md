# python-app

A simple Flask microservice that displays time and hostname, registered in a Backstage Software Catalog with a full GitOps CI/CD pipeline.

## Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/api/v1/info` | GET | Returns current time, hostname, message, and deployment platform |
| `/api/v1/healthz` | GET | Health check — returns `{"status": "up"}` |

**GET /api/v1/info** example:
```json
{
  "time": "10:30:00AM on November 07, 2026",
  "hostname": "python-app-7d6f9b8c5-xkpqr",
  "message": "You are doing great, <3 human!",
  "deployed_on": "kubernetes!"
}
```

## Technology Stack

- **Runtime:** Python 3.14 (Alpine) + Flask
- **Container:** Docker (`python:3.14-alpine`)
- **Orchestration:** Kubernetes — `k8s/` (Deployment, Service, Ingress on `python-app.local`)
- **Helm:** `charts/python-app/` with `values-dev.yaml` and `values-prd.yaml`
- **GitOps:** ArgoCD (`charts/argocd/`)
- **TechDocs:** Backstage (`docs/`, `src/mkdocs.yaml`)

## CI/CD Pipeline

Triggers on pushes to `src/**` on `main`:

1. **ci** — Builds Docker image and pushes `rubenpinzon/python-app:<sha>` to Docker Hub
2. **cd** — Updates `charts/python-app/values.yaml` with new tag, commits, and runs `argocd app sync python-app`

**Required:** `DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`, `ARGOCD_PASSWORD`

## Repository Layout

```
.
├── src/
│   ├── app.py
│   └── mkdocs.yaml
├── k8s/            # deploy.yaml, services.yaml, ingress.yaml
├── charts/
│   ├── python-app/
│   └── argocd/
├── docs/index.md
├── catalog-info.yaml
├── Dockerfile
└── requirements.txt
```

## Running Locally

```bash
pip install flask
python src/app.py
# http://localhost:5000
```

## Backstage Integration

`catalog-info.yaml` registers this as a Backstage Component (type: service, lifecycle: experimental, TechDocs enabled).

## License

MIT
