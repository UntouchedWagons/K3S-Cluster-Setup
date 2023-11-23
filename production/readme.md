# Set up Helm
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh

# Traefik
    helm repo add traefik https://helm.traefik.io/traefik
    helm repo update
    kubectl create namespace traefik
    helm install --namespace=traefik traefik traefik/traefik --values=production/traefik/01-values.yaml

    kubectl apply -f production/traefik/02-traefik.yaml

# Cert-manager
    helm repo add jetstack https://charts.jetstack.io
    helm repo update
    kubectl create namespace cert-manager
    kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.crds.yaml
    helm install cert-manager jetstack/cert-manager --namespace cert-manager --values=production/cert-manager/01-values.yaml --version v1.11.0
    sops -d ./production/cert-manager/02-cert-manager.yaml | kubectl apply -f -

# Longhorn

    helm repo add longhorn https://charts.longhorn.io
    helm repo update
    helm install longhorn longhorn/longhorn --create-namespace --namespace longhorn-system --values=production/longhorn/values.yaml

# Volumes

    kubectl apply -f production/volumes/

# Services
    sops -d ./production/secrets/secrets.yaml | kubectl apply -f -
    kubectl apply -f production/services/

# Monitoring

    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo add grafana https://grafana.github.io/helm-charts
    helm repo add damoun https://charts.damoun.dev
    helm repo update
    sops -d ./production/monitoring/01-prometheus.yaml | helm upgrade --install kube-prometheus-stack prometheus-community/kube-prometheus-stack --create-namespace --namespace monitoring --version 52.1.0 --values -
    helm upgrade --install grafana grafana/grafana --version 7.0.3 --namespace monitoring --values ./production/monitoring/02-grafana.yaml
    sops -d ./production/monitoring-idrac/idrac-exporter.yaml | kubectl apply -f -
    kubectl apply -f production/monitoring-exportarr
    helm install proxmox-exporter --values ./production/monitoring-proxmox/01-proxmox-exporter.yaml --create-namespace --namespace monitoring-proxmox damoun/proxmox-exporter --version 1.5.0
