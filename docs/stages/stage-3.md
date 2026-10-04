# Stage 3: Release day is scary
**Status:** In progress (~80%) · **Started:** 2026-09-30 · **Active hours so far:** ~4.5

## Business pain
Releases meant editing image tags by hand; a typo took orders down for 20 minutes.
Nobody knew which commit was running, or whether the image had known CVEs.

## What we built
- **order-api v1 (Go)** in `tiffinbox-order-api`: REST API, kitchen worker pool
  (buffered channel + goroutines), backpressure (429 when full), graceful drain on SIGTERM
- Tests: table-driven handler tests, kitchen drain/backpressure tests, 50 concurrent orders, run with `-race`
- Multi-stage Dockerfile → distroless, numeric non-root user, git SHA stamped into the binary
- CI (GitHub Actions): golangci-lint → race tests → build → Trivy (fail on HIGH/CRITICAL) → push to GHCR
- `workloads/order-api`: hardened Deployment (read-only FS, dropped capabilities, preStop sleep,
  45s grace period), Service, Ingress; dev/staging/prod overlays; ApplicationSet (prod gated)
- **Auto-bump:** CI commits the new image tag to the dev overlay; Argo deploys it

## How it works
push → lint/test → build → scan → push image `:<sha>` → CI commits `newTag: <sha>` to the dev overlay
→ Argo syncs dev → rolling update with drain. Staging and prod are promoted by PR.

## Gotchas
- Docker's 10s stop timeout killed a 15s drain: the same lesson as `terminationGracePeriodSeconds`
- `runAsNonRoot` needs a numeric UID; `USER nonroot` fails the kubelet check → use 65532
- ApplicationSet label `{{.path.path}}` contains `/` → rejected at render time (dry-run can't catch it)
- In-memory store + 2 replicas → 404s and colliding IDs. Stateful data needs a shared store.
- After CI pushes to gitops, `git pull` before editing locally

## Remaining
- SonarCloud quality gate
- Wrap-up: post brief, `stage-3` tags

## Interview line
A merge to main builds, tests and scans an immutable image; CI commits the new tag to the GitOps repo
and Argo rolls it out to dev with zero-downtime draining. Staging and prod are promoted by PR,
with a manual gate on prod.
