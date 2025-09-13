# ArgoCD Helm Repo

This repository manages ArgoCD deployment via Helm.

## Structure
- `charts/argocd/Chart.yaml` → declares dependency on official `argo-cd` chart
- `values.yaml` → environment-specific config (RBAC, URL, service type)

## Usage
This repo is synced by ArgoCD itself (Application CR in infra repo):

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: argocd
  namespace: argocd
spec:
  project: argocd
  source:
    repoURL: https://github.com/<YOUR_ORG>/argocd-helm.git
    targetRevision: main
    path: charts/argocd
    helm:
      releaseName: argocd
      valueFiles:
        - ../../values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: argocd
  syncPolicy:
    automated:
      selfHeal: true
      prune: true
    syncOptions:
      - CreateNamespace=true
