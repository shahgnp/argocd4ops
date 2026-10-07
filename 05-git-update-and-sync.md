# Session 5 — Update the Repo and Watch ArgoCD Sync

## Objective

Make a valid change — through Git — and watch ArgoCD pick it up and apply it.

## Steps

1. In your local clone, edit `apps/nginx-demo/deployment.yaml` and bump replicas:
   ```yaml
   spec:
     replicas: 4
   ```

2. Commit and push:
   ```bash
   git add apps/nginx-demo/deployment.yaml
   git commit -m "Scale nginx-demo to 4 replicas"
   git push
   ```

3. In the ArgoCD UI, click **Refresh** on the app (or wait for the poll interval).

**Observe:** The app shows **OutOfSync** again — but this time the Diff tab shows the *new* git revision (4 replicas) vs the *currently live* state (2 replicas). Compare this to Session 4: there, drift came from kubectl; here, it comes from a new commit. ArgoCD treats both the same way — it only cares whether live state matches the latest desired state in Git.

4. Click **Sync → Synchronize**.

**Observe:** Open **App Details → History and Rollback**. You'll see a new entry for the commit you just pushed. This history is what lets you roll back to a prior Git revision from the UI without touching the repo — a practical safety net worth remembering.

## Checkpoint

The Deployment now runs 4 replicas, and the History panel lists at least two revisions.
