# Session 1 — Init the Repo and Connect to ArgoCD

## Objective

Register an empty Azure DevOps (azdo) repo with ArgoCD, so ArgoCD can read manifests from it.

## Steps

1. In Azure DevOps, there a new empty Git repository for each participants, e.g. `pageniaaditya1-repo`.
2. Generate Git Credentials:
   - The empty repo has `Generate Git Credentials` button. Click and copy the credentials that is generated.
3. Copy the repo's HTTPS clone URL (Repos → Files → Clone), e.g.:
   ```
   https://dev.azure.com/<org>/<project>/_git/argocd-workshop-<name>
   ```
4. In the ArgoCD Web UI: **Settings → Repositories → Connect Repo using HTTPS**
   - `Choose your connection method`: `VIA HTTP/HTTPS`
   - Type: `git`
   - Name: `anything of your choice should be unique`
   - Project: `Default`
   - Repository URL: the clone URL from step 3
   - Username: your Azure DevOps username
   - Password: the Password from step 2
   - Click **Connect**

**Observe:** The repository's connection status should flip to **Failed** (Red). Why? Because the AzDO repo is empty and has nothing in it.

5. Go Back to AZDO repo and click `Initalize`. It should add a README file.

6. Go back to ArgoCD repository adding page.
   - Click in your repo
   - Click `Edit`
   - Re-add `Username` and `Password`

**Observe:** The repository's connection status should flip to **Successful** (green). This confirms ArgoCD can authenticate and reach the repo — nothing has been deployed yet, this step only establishes trust between ArgoCD and the repo.

## Checkpoint

The repo shows **Successful** under Settings → Repositories, and you have the clone URL and Password saved for Session 2.
