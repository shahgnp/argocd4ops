# Session 4 — Update a Resource with kubectl → Out-of-Sync

## Objective

Manually change a live resource outside of Git and observe how ArgoCD detects and reports the drift.

## Steps

1. Scale the deployment directly with kubectl, bypassing Git entirely:
   ```bash
   kubectl scale deployment nginx-demo -n workshop-nginx-<name> --replicas=3
   ```

2. In the ArgoCD UI, click **Refresh** on the app (or wait up to ~3 minutes for ArgoCD's default polling interval).

**Observe:** The app's sync status flips from **Synced** to **OutOfSync**. Click on `OutOfSync` and Open the **Diff** tab — it shows `replicas: 2` (desired, from git) vs `replicas: 3` (live, from your kubectl command). ArgoCD detected the live cluster state no longer matches the Git-declared state.

3. Click **Sync → Synchronize** to bring the cluster back in line with Git.

**Observe:** Replicas drop back to 2 and the Deployment is reverted. Because sync policy is **manual**, this correction only happened because you clicked Sync. If `selfHeal` were enabled on an automated sync policy, ArgoCD would have reverted the kubectl change automatically within its poll interval, without you doing anything — mention this to your facilitator if you want to see it demonstrated.

## Checkpoint

The app returns to **Synced** / **Healthy** with 2 replicas, and you can explain the difference between "drift was detected" (automatic) and "drift was corrected" (required a manual Sync click here).
