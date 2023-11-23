# Set up Helm
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh

# Traefik
    helm repo add traefik https://helm.traefik.io/traefik
    helm repo update
    helm install --create-namespace --namespace=traefik traefik traefik/traefik --values=testing/traefik/01-values.yaml --version v25.0.0
    kubectl apply -f testing/traefik/02-traefik.yaml

# Cert-manager
    helm repo add jetstack https://charts.jetstack.io
    helm repo update
    kubectl create namespace cert-manager
    kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.2/cert-manager.crds.yaml
    helm install cert-manager jetstack/cert-manager --namespace cert-manager --values=testing/cert-manager/01-values.yaml --version v1.13.2
    sops -d ./testing/cert-manager/02-cert-manager.yaml | kubectl apply -f -
