# To-do (Prerequisite)

---
## Helm

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```

```bash
chmod 700 get_helm.sh
```

```bash
./get_helm.sh
```

```bash
helm version
```

---
## ArgoCD

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

helm install argo-cd argo/argo-cd \
  --namespace argocd \
  --create-namespace
```

---
## Gateway API

```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.0/standard-install.yaml
```

---
