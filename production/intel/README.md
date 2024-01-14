
    helm repo add intel https://intel.github.io/helm-charts/
    helm repo update

    helm upgrade --install device-plugin-operator intel/intel-device-plugins-operator
    helm upgrade --install gpu-device-plugin intel/intel-device-plugins-gpu --values production/intel/values.yaml
