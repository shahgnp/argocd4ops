# Session 6 — Broken Manifests and Bad Image Tags

## Objective

See what happens when the repo contains something invalid, and learn the difference between ArgoCD's **Sync status** (does live match git?) and **Health status** (is it actually working?).

## Part A — Invalid YAML

1. Introduce a syntax error in `apps/nginx-demo/deployment.yaml`, e.g. break the indentation under `spec:` or add a field that doesn't exist on the Deployment schema.
2. Commit and push:
   ```bash
   git add apps/nginx-demo/deployment.yaml
   git commit -m "Break the manifest (intentional)"
   git push
   ```
3. Click **Refresh** in the ArgoCD UI. And click on `Sync` to forcefully apply broken change.

🔍 **Observe:** Open the Diff tab. ArgoCD can't even complete its comparison — the app shows a manifest/comparison error (e.g. "error converting YAML to JSON" or an admission/schema error) rather than a normal Diff. Nothing gets applied to the cluster. ArgoCD caught this *before* touching the live state.

4. Fix it: revert the commit or correct the YAML, commit, push, and re-sync to get back to a clean state.
   ```bash
   git revert HEAD
   git push
   ```

## Part B — Bad Image Tag

1. Edit `apps/nginx-demo/deployment.yaml` and set an image tag that doesn't exist:
   ```yaml
   image: nginx:doesnotexist
   ```
2. Commit, push, and Sync in the ArgoCD UI as usual.

**Observe:** This time the sync *succeeds* — the YAML is structurally valid, so ArgoCD applies it and reports **Synced**. But Health goes to **Degraded** after a while (or stays **Progressing**). Check the pods:
   ```bash
   kubectl get pods -n workshop-nginx-<name>
   kubectl describe pod <pod-name> -n workshop-nginx-<name>
   ```
   You'll see `ImagePullBackOff` / `ErrImagePull` in the pod events.

**Observe — the key takeaway:** ArgoCD validates that your manifest is *structurally* correct and matches what's in the cluster (Sync status). It does **not** validate that the image actually exists or that the workload is functioning (that's Health status, derived from Kubernetes' own resource conditions). A green "Synced" does not mean the app is working — always check Health too.

3. Fix it by pushing the correct image tag (`nginx:1.25`), commit, push, re-sync.
   ```
   git revert HEAD
   git push
   ```

## Checkpoint

You can state, without looking it up: "Synced means live matches git. Healthy means the workload is actually running." And the app is back to **Synced** / **Healthy**.
