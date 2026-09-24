# Deploying Nginx : ArgoCD UI approach
- Create applicatio on ArgoCD dashboard
- This App definition resides on cluster
- ArgoCD created CRD inside the cluster (In GitOps best aproach is to create Application resource CRD in GIT which makes GIT single source of truth, But for quick Demo we will follow this approch)
## 1. Prequisites
1. Kind Cluster
2. Kubectl Installed
3. ArgoCD Setup and CLI configured\

**Guide for ArgoCD setup:** [ArgoCD Setup](https://github.com/Ashhwin-t/GitOps/blob/main/ArgoCD/00_Setup_Installation_On_KindCluster/README.md)
## 2. Steps to follow

### Step-1 : Acess ArgoCD Dashboard
Port-forward ArgoCD server:
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address=0.0.0.0 &
```
Open the UI: http://<instance_public_ip>:8080

**Username:** admin  
**Password:** `(fetched from secret: kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)`
## Step-2 : Add cluster to ArgoCD 
```diff
- Make sure you have installed Argo CLI
```
1. know your cluster details
```bash
kubectl config get-contexts
```
2. Add cluster to ArgoCd
```bash
argocd cluster add kind-argocd-cluster --name argocd-cluster --insecure
```
3. Verify
```bash
argocd cluster list
```
`ArgoCD dashboard: Settings &rarr; Clusters.`\
4. Connect Git Repo with ArgoCd
- In ArgoCD UI, go to Settings → Repositories.  
- Click Connect Repo.
- Choose your connection method:
 HTTPS (for public/private repos)
- Fill in your Git repo details  
    Project: default  
    Repository URL: <add_url_of_forked_repo_of_argocd_demos>  
    Username/Password: (if private repo)
- Click Connect.