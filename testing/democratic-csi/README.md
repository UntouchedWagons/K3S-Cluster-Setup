# Democratic-CSI installation

    helm repo add democratic-csi https://democratic-csi.github.io/charts/
    helm repo update
    helm upgrade --install --values testing/democratic-csi/values.yaml --create-namespace --namespace democratic-csi zfs-iscsi democratic-csi/democratic-csi
  