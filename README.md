# ArgoCD Hands-On Workshop

A guided, hands-on introduction to ArgoCD: connecting a Git repo, deploying apps, observing GitOps reconciliation, and working with Helm charts.

## Prerequisites

Before starting, confirm you have:

- [ ] Access to the ArgoCD Web UI (URL and login provided by the facilitator)
- [ ] `kubectl` configured against the training cluster (`kubectl get nodes` works)
- [ ] `git` installed locally
- [ ] An Azure DevOps organization/project where you can create a repo and a Personal Access Token (PAT)
- [ ] A unique suffix for naming your resources (e.g. your name or initials) so participants don't collide — referred to as `<name>` throughout

## How to use these instructions

Work through the sessions in order — each one builds on the state left by the previous one. Every session file follows the same structure:

- **Objective** — what you'll accomplish and why it matters
- **Steps** — exact commands and UI actions, in order
- **🔍 Observe** — stop and look at this before moving on; this is the actual learning point, not just busywork
- **Checkpoint** — a one-line way to confirm you're ready for the next session

## Sessions

| # | Session | What you'll learn |
|---|---------|--------------------|
| 1 | [Init the repo and connect](01-init-and-connect-repo.md) | Connecting ArgoCD to a Git repo via HTTPS + PAT |
| 2 | [Deploy an application using ArgoCD](02-deploy-application.md) | Creating an Application, manual sync, resource tree |
| 3 | [Check deployed resources in Kubernetes](03-inspect-kubernetes-resources.md) | How ArgoCD tracks ownership of live resources |
| 4 | [Manual kubectl edit → out-of-sync](04-manual-drift-out-of-sync.md) | Drift detection and reverting via sync |
| 5 | [Update the repo → ArgoCD syncs](05-git-update-and-sync.md) | Git as the source of truth, revision history |
| 6 | [Broken manifests and bad images](06-broken-manifest-and-image.md) | Sync status vs. Health status |
| 7 | [Clean up the manifests](07-cleanup.md) | Pruning is opt-in, not automatic |
| 8 | [Deploy a public Helm chart](08-helm-chart-app.md) | Helm sources and parameter overrides |

Start with **[Session 1](01-init-and-connect-repo.md)**.
