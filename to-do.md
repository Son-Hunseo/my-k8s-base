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

```bash
kubectl -n argocd get cm argocd-cm -o yaml > argo-config.yaml
```

```yaml
resource.exclusions: |
    ### Network resources created by the Kubernetes control plane and excluded to reduce the number of watched events and UI clutter
    - apiGroups:
      - ''
      - discovery.k8s.io
      kinds:
      # - Endpoints
      - EndpointSlice
```

- Endpoints 주석처리 (제외 리소스에서 제거)

```bash
kubectl apply -f argo-config.yaml
```

- 이후에 `kubectl -n argocd rollout restart ~` 명령어로 argocd-server deploy와 argocd-repo-server를 재시작

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