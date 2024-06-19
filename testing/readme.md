# Set up Helm

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

# MetalLB

```
kubectl apply -f testing/metallb-system/ip-address-pool.yaml
```

# Cert-manager

```
helm repo add jetstack https://charts.jetstack.io
helm repo update
kubectl create namespace cert-manager
helm upgrade --install cert-manager jetstack/cert-manager --namespace cert-manager --values testing/cert-manager/values.yaml --version v1.14.5
sops -d ./testing/cert-manager/02-cert-manager.yaml | kubectl apply -f -
```

# Secret mirroring

```
helm upgrade --install mirrors kts/mirrors
kubectl apply -f testing/default/mirrors/mirror-tls-secret.yaml
```

# Nginx Ingress

```
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx --create-namespace --namespace nginx --version 4.10.1 --values testing/nginx/ingres-nginx/values.yaml
```

# Nginx

```
helm upgrade --install nginx bjw-s/app-template -f testing/default/nginx/values.yaml
```

# Longhorn

```
helm upgrade --install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace --version 1.6.2 --values testing/longhorn-system/values.yaml
```

# Volsync System

```
helm upgrade --install --create-namespace -n volsync-system volsync backube/volsync
kubectl apply -f testing/namespace.yaml
kubectl apply -f testing/default/volsync-system/volumesnapshotclass.yaml
```

# Node Feature Discovery
```
helm upgrade --install --create-namespace -n node-feature-discovery node-feature-discovery node-feature-discovery/node-feature-discovery
```

# NVidia stuff

```
kubectl apply -f testing/nvidia/nvidia-runtime.yaml
helm install nvidia-device-plugin nvdp/nvidia-device-plugin --version=0.13.0 --create-namespace --namespace nvidia --values testing/nvidia/nvidia-device-plugin/values.yaml
kubectl apply -f testing/nvidia/nvidia-smi.yaml
```
