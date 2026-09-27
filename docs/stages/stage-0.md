# Stage 0: Only Rohit can deploy
**Status:** Done · **Started:** 2026-09-25 · **Finished:** 2026-09-26 · **Active hours:** ~3

## Business pain
Deploys ran as `kubectl apply` from one engineer's laptop. He went on leave; a bug fix sat for 3 days.

## What we built
- k3d cluster `tiffinbox-dev` (1 server, 2 agents, ports 8080/8443 → load balancer)
- Argo CD via Helm, chart `argo/argo-cd` 10.9.2, config in `argocd/values.yaml`
- Traefik ingress at `argocd.localhost:8080`; TLS terminates at Traefik
- Admin password rotated; initial admin secret deleted

## Gotchas
- `enables` typo: Helm silently ignores unknown keys → no Ingress. Check with `helm get values`.
- Download binaries to `/tmp`, not into the repo (clashed with `argocd/`).
- CLI TLS warning = Traefik's default self-signed cert (fixed by cert-manager in Stage 5).

## Evidence
Tag: `stage-0`

## Interview line
Replaced laptop-driven deploys by installing Argo CD from a pinned Helm chart, with config
version-controlled in Git and exposed through ingress.
