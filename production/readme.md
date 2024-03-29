# Set up Helm

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm repo add traefik https://helm.traefik.io/traefik
helm repo add jetstack https://charts.jetstack.io
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add rook-release https://charts.rook.io/release
helm repo add cloudnative-pg https://cloudnative-pg.io/charts/
helm repo add intel https://intel.github.io/helm-charts/
helm repo add node-feature-discovery https://kubernetes-sigs.github.io/node-feature-discovery/charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add bjw-s https://bjw-s.github.io/helm-charts/
helm repo update
```

# Cert-manager

```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.3/cert-manager.crds.yaml
helm upgrade --install cert-manager jetstack/cert-manager --create-namespace --namespace cert-manager --version v1.14.3 --values production/cert-manager/values.yaml
sops -d ./production/cert-manager/02-cert-manager.yaml | kubectl apply -f -
```

# Traefik

```
helm upgrade --install traefik traefik/traefik --create-namespace --namespace traefik --values production/traefik/values.yaml
```

# Ceph

```
helm install --create-namespace --namespace rook-ceph rook-ceph rook-release/rook-ceph
helm install --create-namespace --namespace rook-ceph rook-ceph-cluster rook-release/rook-ceph-cluster -f rook-ceph/rook-ceph-cluster/values.yaml -f production/rook-ceph/rook-ceph-cluster/values.yaml
```

# Node Feature Discovery

```
helm upgrade --install node-feature-discovery node-feature-discovery/node-feature-discovery
```

# Intel GPU Stuff

```
helm upgrade --install device-plugin-operator intel/intel-device-plugins-operator
helm upgrade --install gpu-device-plugin intel/intel-device-plugins-gpu --values k3s-intel/values.yaml
```

# PostgreSQL

```
helm upgrade --install cnpg --create-namespace --namespace cnpg-system cloudnative-pg/cloudnative-pg
kubectl create namespace database
kubectl apply -f production/database/postgresql
kubectl apply -f production/database/pgadmin4/
kubectl apply -f production/database/docker-db-backup/
```

# Restore PostgreSQL databases

```
kubectl apply -f production/database/postgresql-restore/
```

# Services

```
helm upgrade --install file-browser bjw-s/app-template -f production/default/file-browser/values.yaml
kubectl apply -f production/default/homepage/
helm upgrade --install it-tools bjw-s/app-template -f production/default/it-tools/values.yaml
helm upgrade --install jellyfin bjw-s/app-template -f production/default/jellyfin/values.yaml
kubectl apply -f production/default/qbittorrent/volume.yaml
sops -d production/default/qbittorrent/secrets.yaml | kubectl apply -f -
kubectl apply -f production/default/qbittorrent/service.yaml
helm upgrade --install sabnzbd bjw-s/app-template -f production/default/sabnzbd/values.yaml
helm upgrade --install vaultwarden bjw-s/app-template -f production/default/vaultwarden/values.yaml
kubectl apply -f production/servarr/
kubectl apply -f production/servarr/bazarr/
helm upgrade --install flaresolverr bjw-s/app-template --namespace servarr -f production/servarr/flaresolverr/values.yaml
kubectl apply -f production/servarr/lidarr/
kubectl apply -f production/servarr/prowlarr/
kubectl apply -f production/servarr/radarr/
kubectl apply -f production/servarr/sonarr/
kubectl apply -f production/ai/
helm upgrade --install cpas bjw-s/app-template --namespace ai -f production/ai/codeproject/values.yaml
kubectl apply -f production/networking/
kubectl apply -f production/networking/ispyagentdvr/
sops -d production/networking/cloudflared/values.yaml | helm upgrade --install cloudflared kubitodev/cloudflared --namespace networking --version 1.1.0 --values -
sops -d production/networking/ddclient/service.yaml | kubectl apply -f -
sops -d production/networking/rclone/service.yaml | kubectl apply -f -
sops -d production/networking/msmtpd/service.yaml | kubectl apply -f -
```

# Home Assistant

```
kubectl apply -f production/home-assistant/
kubectl apply -f production/home-assistant/zwave-js-ui
kubectl apply -f production/home-assistant/home-assistant
```

# Monitoring

```
sops -d ./production/monitoring/prometheus/values.yaml | helm upgrade --install kube-prometheus-stack prometheus-community/kube-prometheus-stack --create-namespace --namespace monitoring --version 55.11.0 --values -
helm upgrade --install grafana grafana/grafana --namespace monitoring --version 7.3.3 --values ./production/monitoring/grafana/values.yaml
kubectl apply -f production/monitoring/exporter-idrac/
kubectl apply -f production/monitoring/exporter-flaresolverr/
kubectl apply -f production/monitoring/exporter-linux/
kubectl apply -f production/monitoring/exporter-opnsense/
kubectl apply -f production/monitoring/exporter-proxmox/
helm upgrade --install exporter-sonarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-sonarr/values.yaml
helm upgrade --install exporter-radarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-radarr/values.yaml
helm upgrade --install exporter-lidarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-lidarr/values.yaml
helm upgrade --install exporter-prowlarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-prowlarr/values.yaml
helm upgrade --install exporter-bazarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-bazarr/values.yaml
helm upgrade --install exporter-sabnzbd bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-sabnzbd/values.yaml
kubectl apply -f production/monitoring/exporter-qbittorrent/
kubectl apply -f production/monitoring/exporter-nut/
kubectl apply -f production/monitoring/exporter-zfs/
kubectl apply -f production/monitoring/exporter-technitium/
kubectl apply -f production/monitoring/exporter-blackbox/
```
