# Set up Helm

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm repo add traefik https://helm.traefik.io/traefik
helm repo add jetstack https://charts.jetstack.io
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add rook-release https://charts.rook.io/release
helm repo add intel https://intel.github.io/helm-charts
helm repo add node-feature-discovery https://kubernetes-sigs.github.io/node-feature-discovery/charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add bjw-s https://bjw-s.github.io/helm-charts
helm repo add jameswynn https://jameswynn.github.io/helm-charts
helm repo add piraeus-charts https://piraeus.io/helm-charts
helm repo add backube https://backube.github.io/helm-charts/
helm repo add kts https://charts.kts.studio
helm repo update
```

# Cert-manager

```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.5/cert-manager.crds.yaml
helm upgrade --install cert-manager jetstack/cert-manager --create-namespace --namespace cert-manager --version v1.14.5 --values production/cert-manager/values.yaml
sops -d ./production/cert-manager/02-cert-manager.yaml | kubectl apply -f -
```

# Secret mirroring

```
helm upgrade --install mirrors kts/mirrors
kubectl apply -f production/default/mirrors/mirror-com-untouchedwagons-services.yaml
```

# Traefik

```
helm upgrade --install traefik traefik/traefik --create-namespace --namespace traefik --values production/traefik/values.yaml --version 28.0.0
```

# Volume snapshots

```
helm upgrade --install snapshot-controller piraeus-charts/snapshot-controller
```

# Ceph

```
helm install --create-namespace --namespace rook-ceph rook-ceph rook-release/rook-ceph
helm upgrade --install --create-namespace --namespace rook-ceph rook-ceph-cluster rook-release/rook-ceph-cluster -f rook-ceph/rook-ceph-cluster/values.yaml -f production/rook-ceph/rook-ceph-cluster/values.yaml
```

# Volsync

```
helm upgrade --install --create-namespace -n volsync-system volsync backube/volsync --values production/volsync-system/values.yaml
sops -d production/volsync-system/secrets.yaml | kubectl apply -f -
kubectl apply -f production/volsync-system/replicationdestination.yaml
kubectl apply -f production/volsync-system/replicationsource.yaml
```

# Version Checker

```
sops -d production/version-checker/version-checker/values.yaml | helm upgrade --install version-checker --create-namespace --namespace version-checker jetstack/version-checker --version 0.5.5 --values -
```

# Node Feature Discovery

```
helm upgrade --install --create-namespace -n node-feature-discovery node-feature-discovery node-feature-discovery/node-feature-discovery --values production/node-feature-discovery/node-feature-discovery/values.yaml
```

# Intel GPU Stuff

```
helm upgrade --install device-plugin-operator intel/intel-device-plugins-operator
helm upgrade --install gpu-device-plugin intel/intel-device-plugins-gpu --values k3s-intel/values.yaml
```

# Services

## Default namespace

```
kubectl apply -f production/default
helm upgrade --install file-browser bjw-s/app-template -f production/default/file-browser/values.yaml
helm upgrade --install homepage jameswynn/homepage -f production/default/homepage/values.yaml
helm upgrade --install it-tools bjw-s/app-template -f production/default/it-tools/values.yaml
helm upgrade --install jellyfin bjw-s/app-template -f production/default/jellyfin/values.yaml
sops -d production/default/qbittorrent/values.yaml | helm upgrade --install qbittorrent bjw-s/app-template -f -
helm upgrade --install sabnzbd bjw-s/app-template -f production/default/sabnzbd/values.yaml
helm upgrade --install vaultwarden bjw-s/app-template -f production/default/vaultwarden/values.yaml
```

## servarr namespace

```
kubectl apply -f production/servarr/
helm upgrade --install bazarr bjw-s/app-template --namespace servarr -f production/servarr/bazarr/values.yaml
helm upgrade --install flaresolverr bjw-s/app-template --namespace servarr -f production/servarr/flaresolverr/values.yaml
helm upgrade --install lidarr bjw-s/app-template --namespace servarr -f production/servarr/lidarr/values.yaml
helm upgrade --install prowlarr bjw-s/app-template --namespace servarr -f production/servarr/prowlarr/values.yaml
helm upgrade --install radarr bjw-s/app-template --namespace servarr -f production/servarr/radarr/values.yaml
helm upgrade --install sonarr bjw-s/app-template --namespace servarr -f production/servarr/sonarr/values.yaml
helm upgrade --install tdarr-server bjw-s/app-template --namespace servarr -f production/servarr/tdarr-server/values.yaml
helm upgrade --install tdarr-worker bjw-s/app-template --namespace servarr -f production/servarr/tdarr-worker/values.yaml
```

## AI namespace

```
kubectl apply -f production/ai/
helm upgrade --install cpas bjw-s/app-template --namespace ai -f production/ai/codeproject/values.yaml
```

## Networking namespace

```
kubectl apply -f production/networking/
kubectl apply -f production/networking/ispyagentdvr/
sops -d production/networking/cloudflared/values.yaml | helm upgrade --install cloudflared kubitodev/cloudflared --namespace networking --version 1.1.0 --values -
sops -d production/networking/ddclient/values.yaml | helm upgrade --install ddclient bjw-s/app-template --namespace networking -f -
sops -d production/networking/rclone/service.yaml | kubectl apply -f -
sops -d production/networking/msmtpd/service.yaml | kubectl apply -f -
```

# Home Assistant

```
kubectl apply -f production/home-assistant/
helm upgrade --install zwave-js-ui bjw-s/app-template --namespace home-assistant -f production/home-assistant/zwave-js-ui/values.yaml
helm upgrade --install home-assistant bjw-s/app-template --namespace home-assistant -f production/home-assistant/home-assistant/values.yaml
```

# Monitoring

```
sops -d ./production/monitoring/prometheus/values.yaml | helm upgrade --install kube-prometheus-stack prometheus-community/kube-prometheus-stack --create-namespace --namespace monitoring --version 55.11.0 --values -
helm upgrade --install grafana grafana/grafana --namespace monitoring --version 7.3.11 --values ./production/monitoring/grafana/values.yaml
kubectl apply -f production/monitoring/ceph/rule.yaml
kubectl apply -f production/monitoring/exporter-idrac/
kubectl apply -f production/monitoring/exporter-flaresolverr/
kubectl apply -f production/monitoring/exporter-linux/
kubectl apply -f production/monitoring/exporter-opnsense/
kubectl apply -f production/monitoring/exporter-proxmox/
helm upgrade --install exporter-bazarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-bazarr/values.yaml
helm upgrade --install exporter-lidarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-lidarr/values.yaml
helm upgrade --install exporter-prowlarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-prowlarr/values.yaml
helm upgrade --install exporter-qbittorrent bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-qbittorrent/values.yaml
helm upgrade --install exporter-radarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-radarr/values.yaml
helm upgrade --install exporter-sabnzbd bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-sabnzbd/values.yaml
helm upgrade --install exporter-sonarr bjw-s/app-template --namespace monitoring -f production/monitoring/exporter-sonarr/values.yaml
kubectl apply -f production/monitoring/exporter-nut/
kubectl apply -f production/monitoring/exporter-zfs/
kubectl apply -f production/monitoring/exporter-technitium/
kubectl apply -f production/monitoring/exporter-blackbox/
```
