# Session 2 — Deploy an Application Using ArgoCD

## Objective

Add Kubernetes manifests to the repo and deploy them through ArgoCD, observing how an Application moves from "not deployed" to "synced and healthy."

## Steps

1. Clone the repo locally:
   ```bash
   git clone <URL>
   cd <project-directory>
   ```
   When prompted for credentials, use your Azure DevOps username and the Password from Session 1 as the password.

2. Create the manifest directory and files:
   ```bash
   mkdir -p apps/nginx-demo
   ```

   `apps/nginx-demo/namespace.yaml`:
   ```yaml
   apiVersion: v1
   kind: Namespace
   metadata:
     name: workshop-nginx-<name>
   ```

   `apps/nginx-demo/deployment.yaml`:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-demo
     namespace: workshop-nginx-<name>
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: nginx-demo
     template:
       metadata:
         labels:
           app: nginx-demo
       spec:
         containers:
           - name: nginx
             image: nginx:1.25
             ports:
               - containerPort: 80
   ```

   `apps/nginx-demo/service.yaml`:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-demo
     namespace: workshop-nginx-<name>
   spec:
     selector:
       app: nginx-demo
     ports:
       - port: 80
         targetPort: 80
   ```

   Replace `<name>` with your unique suffix in all three files.

3. Commit and push:
   ```bash
   git add apps/nginx-demo
   git commit -m "Add nginx demo manifests"
   git push
   ```

4. In the ArgoCD Web UI, click **New App** and fill in:
   - **Application Name:** `nginx-demo-<name>`
   - **Project:** `default`
   - **Sync Policy:** **Manual** (deliberately — this keeps drift visible in later sessions instead of ArgoCD auto-healing it away)
   - **Repository URL:** Your repo you connected in Session 1
   - **Path:** `apps/nginx-demo`
   - **Cluster URL:** `https://kubernetes.default.svc`
   - **Namespace:** `workshop-nginx-<name>`
   - Click **Create**

**Observe:** Right after creation, the app shows **Missing**/**OutOfSync** — ArgoCD has read the desired state from git but nothing exists in the cluster yet. This is expected with manual sync policy.

5. Click into the app, then click **Sync → Synchronize**.

**Observe:** The status moves to **Synced** and **Healthy**. Open the resource tree — you'll see the hierarchy ArgoCD created and now tracks: Namespace → Deployment → ReplicaSet → Pods, and Service alongside. This tree view is how you'll spot drift and errors in later sessions.

## Checkpoint

`nginx-demo-<name>` shows **Synced** / **Healthy** in the ArgoCD UI with a visible resource tree.
