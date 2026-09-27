# Stage 1: Who changed prod?
**Status:** Done · **Started:** 2026-09-27 · **Finished:** 2026-09-27 · **Active hours:** ~2.5

## Business pain
Someone ran `kubectl edit` on the menu service at 1am. Prices were wrong for 2 hours; nobody knew who changed what.

## What we built
- `bootstrap/root-app.yaml`: tiffinbox-root, the only manifest applied by hand (app-of-apps)
- `apps/menu-api.yaml`: podinfo 6.15.0 → tb-dev, ingress at menu.localhost:8080
- `apps/argocd.yaml`: Argo CD manages itself (multi-source: chart + `$values` from this repo)
- Banner added to Argo's UI purely via `argocd/values.yaml`

## How it works
- Reconcile loop: poll Git (~3 min) → render → diff desired vs live → sync
- `selfHeal` reverts manual changes; `prune` deletes what's removed from Git
- Root has no finalizer (deleting it must not cascade); menu-api has one (clean teardown)
- Argo adopted its Helm-installed resources: the diff was only tracking-id annotations

## Demos
- Scale to 5 → back to 2 · `set env` → reverted · delete deployment → recreated
- v2 (purple) via commit → `git revert` → v1 (orange)

## Gotchas
- `git revert HEAD` reverts *whatever* was last; it deleted menu-api (then revert-the-revert)
- Changing argocd-cm restarts argocd-server → CLI 504 via Traefik mid-sync
- `/api/v1/settings` shows only public fields without a token
- Never `helm upgrade` Argo again; Helm's release record is stale

## Evidence
Tag: `stage-1`

## Interview line
Made Git the only way to change the cluster: self-heal removed config drift, rollback became a
`git revert` with an audit trail, and Argo CD now upgrades itself from the same repo.
