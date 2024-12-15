# Kargo Demo

Demo setting up a multicluster CI/CD pipeline with Kargo. This repo is intended to control multiple clusters

This repo uses [gitmoji](https://gitmoji.dev/) for commit messages.

## Environment

This demo will setup the following environment:

```mermaid
graph TD;
    clustera[Cluster A]
    clusterb[Cluster B]
    kargo[Kargo]
    constainerreg[Container Registry]
    gitopsrepo[GitOps Repo]

    %% Interactions
    kargo -->|listening for new images| constainerreg
    kargo -->|syncing with GitOps repo| gitopsrepo
    kargo -->|promoting images| clustera
    kargo -->|promoting images| clusterb
```

### Setup Kargo

Following the instructions in the [Kargo README](https://docs.kargo.io/how-to-guides/installing-kargo) to install Kargo.

Cert Manger is required for Kargo. Install it with the following command:

```bash
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.16.2 --set crds.enabled=true --set prometheus.enabled=false --set webhook.timeoutSeconds=4
```

ArgoCD is required for Kargo. Install it with the following command:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Access the ArgoCD UI with the following command:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Username is `admin` and password is the output from:

```bash
argocd admin initial-password -n argocd
```

## Questions

This repo wants to answer the following questions:
- How to setup a multicluster CI/CD pipeline with Kargo?
- Do I need to install ArgoCD on each cluster?
- How does Kargo communicate with ArgoCD in each cluster?
- How can I avoid a recursive loop when syncing the same repo in each cluster and making changes to the GitOps repo?
