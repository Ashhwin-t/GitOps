# Setting Up ArgoCd On Kind Cluster
This is the complete guide to setup Kind cluster on EC2 instance and install Argo CD.
This Guide is prepared consiering you have basic understanding of Linux, Git, Kubernetes, Docker
## Steps

- Launch an EC2 Instance. Select atleast t3.medium with atleast 20GB Storage instance as we are going to create Kind cluster and install some tools

- Connect/SSH to the instance

# ArgoCd Installation
Read carefully and follow below steps
## Prerequisites
Make sure you have following tools installed

**1. Docker** :arrow_right: Container will be run as Kind cluster nodes

```bash
  sudo apt-get update
  sudo apt install docker.io -y
```
By just installing docker you can not run docker command as current user doesnt have permission, so we run below command to append $USER to the supplementary group "docker". $$ neegrp docker will refresh the group so that you dont have to logout and login again.
```bash
  sudo usermod -aG docker $USER && newgrp docker
  docker --version
```

2. **Kind** (Kibernetes in Docker) :arrow_right: To create Cluster\
Follow Below Guide

[Installation Guide](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)

3. **Kubectl** :arrow_right: To interact with cluster\
Folow Below Guide

[kubectl Installation Guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

4. **Helm**\
Follow Below Guide (Folow instructions for Install from script)

[Helm Installation Guide](https://helm.sh/docs/intro/install/)

## Step 1:  Create Kind cluster  
1. Save config file as kind-config.yaml

```yml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  apiServerAddress: "172.31.19.150"   # Change this to your EC2 private IP (run "hostname -I" to check or from your EC2 dashboard)
  apiServerPort: 33893
nodes:
  - role: control-plane
    image: kindest/node:v1.33.1
  - role: worker
    image: kindest/node:v1.33.1
  - role: worker
    image: kindest/node:v1.33.1
```

2. Create Cluster
```bash
kind create cluster --name argocd-cluster --config kind-config.yaml
```

3. Verify

```bash
kubectl cluster-info
kubectl get nodes
```

## Step 2 : Install ArgoCd
### Method 1: Using Helm

1. Add Argo Helm Repo
```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```
2. Create "argocd" Namespace
```bash
kubectl create namespace argocd
```
3. Install ArgoCD
```bash
helm install argocd argo/argo-cd -n argocd
```
4. Installation Verification
```bash
kubectl get pods -n argocd
kubectl get svc -n argocd
```
5. Accessing ArgoCD Dashboard
To access the dashboard you need to expose the argocd server. We will port forward to the argocd service/
/
Go to the security group of EC2 instance and add inbound rule to allow custom tcp trafic to 8080 port 
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address=0.0.0.0 &
```
6. Get initial Password
```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d && echo
  ```
**Username:** admin\
**Password:** `above output`