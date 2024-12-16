```markdown
# Kubernetes Deployment with ArgoCD GITOPS

This project demonstrates a Kubernetes deployment using ArgoCD for GitOps-style continuous delivery. It automates the process of synchronizing application state with a Git repository.

## Prerequisites

Before getting started, ensure you have the following installed:

- **Kubernetes**: Ensure your cluster is set up and `kubectl` is configured.
- **ArgoCD**: Install ArgoCD and its CLI in your Kubernetes cluster.
- **Git**: To clone this repository and push changes.
- **Base64 Decoder**: To decode the ArgoCD admin password if needed.

## Project Structure

This repository contains Kubernetes manifests such as:

- `deployment.yaml`: Defines the deployment of the application.
- `service.yaml`: Exposes the application via a Kubernetes service.
- `argocd-app.yaml`: The ArgoCD Application definition to manage your Kubernetes resources.

## Setup and Installation

### 1. Clone this repository

```bash
git clone https://github.com/yourusername/your-repo.git
cd your-repo
```

### 2. Install ArgoCD in your Kubernetes Cluster

If you haven't installed ArgoCD yet, follow these steps:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 3. Access the ArgoCD UI

After installation, expose the ArgoCD API server as a LoadBalancer or port-forward it:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Now, open your browser and navigate to `https://localhost:8080`.

### 4. Get the ArgoCD admin password

Run the following command to retrieve the initial ArgoCD admin password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
```

Login with `admin` as the username and the decoded password.

### 5. Create the ArgoCD Application

Apply the `argocd-app.yaml` file to define your application in ArgoCD:

```bash
kubectl apply -f argocd-app.yaml
```

This will configure ArgoCD to watch for changes in this repository and synchronize them with your Kubernetes cluster.

### 6. Sync the Application

After creating the application, ArgoCD will attempt to synchronize the state defined in the Git repository with the cluster.

You can sync manually via the ArgoCD CLI:

```bash
argocd app sync <app-name>
```

Or you can set auto-sync to ensure any updates pushed to this repository automatically deploy to the cluster.

### 7. Verify the Deployment

To check if the application is running and healthy:

```bash
kubectl get pods -n <namespace>
```

You should see your pods in `Running` status.

### 8. Access the Application

If you have set up a service with a `LoadBalancer` type, retrieve the external IP:

```bash
kubectl get svc -n <namespace>
```

For a `NodePort`, use the cluster's IP and the exposed port.

Alternatively, you can port-forward a service to access it locally:

```bash
kubectl port-forward svc/<your-service-name> 8080:80 -n <namespace>
```

Now access the application at `http://localhost:8080`.

## Auto Sync

This repository is set up with auto-sync enabled in ArgoCD. Any push to the Git repository will automatically trigger an update to the Kubernetes resources.

## Troubleshooting

If the application is out of sync or you encounter issues, use the following commands to debug:

- **Check pod status**:
  ```bash
  kubectl get pods -n <namespace>
  ```

- **Check ArgoCD application status**:
  ```bash
  argocd app get <app-name>
  ```

- **View pod logs**:
  ```bash
  kubectl logs <pod-name> -n <namespace>
  ```
Shots:
![image](https://github.com/user-attachments/assets/376a716a-e66f-4f7e-aebd-168aba0060c1)
