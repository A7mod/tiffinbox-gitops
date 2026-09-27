# Post brief: Stage 1
- **Hook:** "Someone kubectl-edited prod at 1am. Here's how I made that impossible to stick."
- **Pain:** Manual cluster changes, no audit trail, wrong prices for 2 hours.
- **What I built:** App-of-apps with Argo CD; self-heal + prune; Argo managing its own install from Git.
- **3 learnings:**
  1. Sync status and health status are different questions.
  2. Adopting a Helm install: the diff should be only tracking labels. Anything more, stop.
  3. `git revert` is a rollback and an audit trail at once (I learned this by reverting the wrong commit).
- **Visual idea:** Before/after: kubectl scale to 5 → Argo snaps back to 2. Orange → purple → orange page.
- **Link:** repo / docs/stages/stage-1.md
