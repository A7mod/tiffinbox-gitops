# Post brief: Stage 2
- **Hook:** "Prod had 1 replica at lunch rush because someone copy-pasted staging's YAML."
- **Pain:** Copy-pasted env configs drift; nobody can see what differs between staging and prod.
- **What I built:** Kustomize base + overlays; one ApplicationSet → dev/staging/prod; prod behind a manual sync gate.
- **3 learnings:**
  1. `diff` of rendered overlays shows exactly what differs per env: the whole point.
  2. Kubernetes tools fail silently on typos (wrong kind, unknown field). Server dry-run catches them.
  3. A new environment is a folder, not a ticket.
- **Visual idea:** Three browser tabs: blue DEV, amber STAGING, orange PROD. Then the terminal `check` output during promotion.
- **Link:** repo / docs/stages/stage-2.md
