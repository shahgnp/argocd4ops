# Session 7 — Clean Up the Manifests from the Repo

## Objective

Remove the app's manifests from Git and learn that ArgoCD does **not** delete cluster resources by default when they disappear from git — pruning is opt-in.

## Steps

1. Delete the manifests from your local clone:
   ```bash
   rm apps/nginx-demo/*
   mkdir -p apps/nginx-demo
   touch apps/nginx-demo/.gitkeep
   git add .
   git commit -m "Remove nginx-demo manifests"
   git push
   ```

2. In the ArgoCD UI, click **Refresh** on the app.

🔍 **Observe:** The app shows **OutOfSync**, and the resource tree marks the Namespace/Deployment/Service as resources that exist live but are no longer defined in git. A plain **Sync** at this point will **not** delete them — try it and confirm with:
   ```bash
   kubectl get all -n workshop-nginx-<name>
   ```
   The resources are still there.

3. Sync again, but this time enable the **Prune** option in the Sync dialog (checkbox, or "Synchronize with Prune").

🔍 **Observe:** Now the Namespace, Deployment, and Service are actually deleted from the cluster. Confirm:
   ```bash
   kubectl get namespace workshop-nginx-<name>
   ```
   (should return "not found")

   Removing files from git is not enough on its own — pruning must be explicitly requested per-sync, or configured as part of an automated sync policy (`prune: true`), or orphaned resources will silently linger in the cluster.

## Checkpoint

`kubectl get namespace workshop-nginx-<name>` returns "not found", and you can explain why the first Sync attempt didn't delete anything.
