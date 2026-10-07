# Session 8 — App of Apps: Deploy Multiple Public Helm Charts

## Objective

Demonstrate the **app-of-apps pattern**: one parent Application fans out to multiple independently-managed child Applications. You'll repoint the existing `nginx-demo-<name>` Application (from Session 2) to watch the whole `apps/` directory, then add **two** child Application manifests — a Bitnami nginx chart and a podinfo chart — and watch a single sync of the parent create both.

## Steps

1. Repoint the existing `nginx-demo-<name>` Application so it can discover new Application manifests under `apps/`:
   - In the ArgoCD UI, open `nginx-demo-<name>` → **App Details → Edit**
   - Change **Path** from `apps/nginx-demo` to `apps`
   - Enable **Directory → Recurse**
   - Save

**Observe:** `nginx-demo-<name>`'s own destination namespace (`workshop-nginx-<name>`) is unchanged — this edit only widens *what git path it watches*, from one app's folder to the whole `apps/` tree. After Session 7's cleanup, that folder is empty, so nothing changes in the cluster yet. This Application is now your app-of-apps **root**.

2. In your local clone, create `apps/nginx-helm/application.yaml`:
   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: nginx-helm-<name>
     namespace: argocd
   spec:
     project: default
     source:
       repoURL: https://charts.bitnami.com/bitnami
       chart: nginx
       targetRevision: 25.2.1   # check `helm search repo bitnami/nginx --versions` for the latest
       helm:
         parameters:
           - name: replicaCount
             value: "1"
           - name: image.tag
             value: "latest"
     destination:
       server: https://kubernetes.default.svc
       namespace: workshop-helm-<name>
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
       syncOptions:
         - CreateNamespace=true
   ```

3. Create a second child, `apps/podinfo-helm/application.yaml` — a different chart from a different Helm repo entirely:
   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: podinfo-helm-<name>
     namespace: argocd
   spec:
     project: default
     source:
       repoURL: https://stefanprodan.github.io/podinfo
       chart: podinfo
       targetRevision: 6.15.0   # check `helm search repo podinfo/podinfo --versions` for the latest
       helm:
         parameters:
           - name: replicaCount
             value: "1"
           - name: ui.message
             value: "hello from <name>"
     destination:
       server: https://kubernetes.default.svc
       namespace: workshop-podinfo-<name>
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
       syncOptions:
         - CreateNamespace=true
   ```

   Replace `<name>` with your unique suffix in both files. Both use `syncPolicy.automated: {prune: true, selfHeal: true}`

4. Commit and push both manifests together:
   ```bash
   git add apps/nginx-helm/application.yaml apps/podinfo-helm/application.yaml
   git commit -m "Add nginx-helm and podinfo-helm Application manifests"
   git push
   ```

5. Go back to `nginx-demo-<name>` in the ArgoCD UI, click **Refresh**, then **Sync → Synchronize**.

🔍 **Observe:** This *one* Sync creates **both** `nginx-helm-<name>` and `podinfo-helm-<name>` in the `argocd` namespace

🔍 **Observe:** Within moments, both child apps appear in the Applications list on their own and go **Synced** / **Healthy** independently.

## Checkpoint

Both `nginx-helm-<name>` and `podinfo-helm-<name>` show **Synced** / **Healthy**, created by a single sync of `nginx-demo-<name>`.
---
