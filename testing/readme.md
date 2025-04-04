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
helm upgrade --install cert-manager jetstack/cert-manager --create-namespace --namespace cert-manager --values testing/cert-manager/values.yaml --version v1.17.1
sops -d ./testing/cert-manager/02-cert-manager.yaml | kubectl apply -f -
```

# Secret mirroring

```
helm upgrade --install mirrors kts/mirrors
kubectl apply -f testing/default/mirrors/mirror-tls-secret.yaml
```

# Nginx Ingress

```
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx --create-namespace --namespace nginx --version 4.12.1 --values testing/nginx/ingres-nginx/values.yaml
```

# Nginx

```
helm upgrade --install nginx bjw-s/app-template -f testing/default/nginx/values.yaml
```

# Longhorn

```
helm upgrade --install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace --version 1.8.1 --values testing/longhorn-system/values.yaml
```

# Volsync System

```
helm upgrade --install --create-namespace -n volsync-system volsync backube/volsync
kubectl apply -f testing/default/namespace.yaml
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
