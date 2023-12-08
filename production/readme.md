# Set up Helm
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh
    helm repo add traefik https://helm.traefik.io/traefik
    helm repo add jetstack https://charts.jetstack.io
    helm repo add democratic-csi https://democratic-csi.github.io/charts/
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo add runix https://helm.runix.net/
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo add grafana https://grafana.github.io/helm-charts
    helm repo update

# Traefik
    helm upgrade --install traefik traefik/traefik --create-namespace --namespace traefik --values production/traefik/01-values.yaml
    kubectl apply -f production/traefik/02-traefik.yaml

# Cert-manager
    kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.crds.yaml
    helm upgrade --install cert-manager jetstack/cert-manager --create-namespace --namespace cert-manager --version v1.11.0 --values production/cert-manager/01-values.yaml
    sops -d ./production/cert-manager/02-cert-manager.yaml | kubectl apply -f -

# Democratic-CSI installation

    helm upgrade --install  zfs-iscsi democratic-csi/democratic-csi --create-namespace --namespace democratic-csi --values production/democratic-csi/values.yaml

# PostgreSQL

    kubectl apply -f production/database/
    helm upgrade --install postgresql bitnami/postgresql --namespace database --version 13.2.24 --values production/database/postgresql/values.yaml
    helm upgrade --install pgadmin4 runix/pgadmin4 --namespace database --version 1.18.5 --values production/database/pgadmin4/values.yaml
    kubectl apply -f production/database/docker-db-backup/service.yaml

# Restore PostgreSQL databases

To be discovered

# Services
    kubectl apply -f production/default/homepage/
    kubectl apply -f production/default/it-tools/
    kubectl apply -f production/default/jellyfin/
    sops -d production/default/qbittorrent/service.yaml | kubectl apply -f -
    kubectl apply -f production/default/sabnzbd/
    kubectl apply -f production/default/vaultwarden/
    kubectl apply -f production/servarr/
    kubectl apply -f production/servarr/bazarr/
    kubectl apply -f production/servarr/lidarr/
    kubectl apply -f production/servarr/prowlarr/
    kubectl apply -f production/servarr/radarr/
    kubectl apply -f production/servarr/sonarr/
    kubectl apply -f production/ai/
    kubectl apply -f production/ai/deepstack/
    kubectl apply -f production/network/
    kubectl apply -f production/network/ispyagentdvr/
    sops -d production/networking/ddclient/service.yaml | kubectl apply -f -

# Monitoring

    sops -d ./production/monitoring/prometheus/service.yaml | helm upgrade --install kube-prometheus-stack prometheus-community/kube-prometheus-stack --create-namespace --namespace monitoring --version 52.1.0 --values -
    helm upgrade --install grafana grafana/grafana --namespace monitoring --version 7.0.3 --values ./production/monitoring/grafana/service.yaml
    kubectl apply -f production/monitoring/exporter-idrac/
    kubectl apply -f production/monitoring/exporter-linux/
    kubectl apply -f production/monitoring/exporter-opnsense/
    kubectl apply -f production/monitoring/exporter-proxmox/
    kubectl apply -f production/monitoring/exporter-sonarr/
    kubectl apply -f production/monitoring/exporter-radarr/
    kubectl apply -f production/monitoring/exporter-lidarr/
    kubectl apply -f production/monitoring/exporter-prowlarr/
    kubectl apply -f production/monitoring/exporter-bazarr/
    kubectl apply -f production/monitoring/exporter-sabnzbd/
    kubectl apply -f production/monitoring/exporter-qbittorrent/
