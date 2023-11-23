# Helm

    helm repo add damoun https://charts.damoun.dev
    helm install proxmox-exporter --values ./production/monitoring-proxmox/01-proxmox-exporter.yaml --create-namespace --namespace monitoring-proxmox damoun/proxmox-exporter --version 1.5.0
    sops -d ./production/monitoring-proxmox/02-scrape-job.yaml | kubectl apply -f -
