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
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.4.0/standard-install.yaml
```

---
## MetalLB

```bash
kubectl get configmap kube-proxy -n kube-system -o yaml | \
	sed -e "s/strictARP: false/strictARP: true/" | \
	kubectl apply -f - -n kube-system
```

```bash
helm repo add metallb https://metallb.github.io/metallb
helm repo update

helm install metallb metallb/metallb \
  --namespace metallb \
  --create-namespace
```

```bash
nano ip-pool.yaml
```

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: ip-pool
  namespace: metallb
spec:
  addresses:
  - 192.168.0.200-192.168.0.250 # 노드 대역과 같되 이미 있는 노드와 겹치지 않게 설정
```

```bash
nano advertisement.yaml
```

```yaml
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2-advertisement
  namespace: metallb
spec:
  ipAddressPools:
  - ip-pool # 위에서 만든 IPAddressPool 이름
```

```bash
kubectl apply -f ip-pool.yaml
kubectl apply -f advertisement.yaml
```

---