# Set up Helm

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

# Cert-manager

```
helm repo add jetstack https://charts.jetstack.io
helm repo update
kubectl create namespace cert-manager
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.5/cert-manager.crds.yaml
helm install cert-manager jetstack/cert-manager --namespace cert-manager --values testing/cert-manager/01-values.yaml --version v1.14.5
sops -d ./testing/cert-manager/02-cert-manager.yaml | kubectl apply -f -
```

# Nginx Ingress

```
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx --create-namespace --namespace nginx --version 4.10.1 --values testing/nginx/ingres-nginx/values.yaml
```

# Nginx

```
helm upgrade --install nginx bjw-s/app-template -f testing/default/nginx/values.yaml
```