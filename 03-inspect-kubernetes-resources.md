# Session 3 — Check the Deployed Resources in Kubernetes

## Objective

Look at what ArgoCD actually created in the cluster, and understand how ArgoCD recognizes these resources as "belonging" to the Application.

## Steps

1. List everything in your namespace:
   ```bash
   kubectl get all -n workshop-nginx-<name>
   ```

2. Inspect the Deployment's labels and annotations:
   ```bash
   kubectl get deployment nginx-demo -n workshop-nginx-<name> -o yaml
   ```
