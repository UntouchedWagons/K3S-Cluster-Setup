
## Cleaning house

```
rm -Rf ~/.talos ~/.kube/talos-config talos/testing/rendered talos/testing/rendered+nvidia talos/testing/secrets.yaml
```

## Getting started

```
talosctl gen secrets --output-file talos/testing/secrets.yaml

talosctl gen config testing-cluster https://192.168.20.90:6443 \
  --with-secrets  talos/testing/secrets.yaml \
  --config-patch @talos/testing/patches/allow-controlplane-workloads.yaml \
  --config-patch @talos/testing/patches/dhcp.yaml \
  --config-patch @talos/testing/patches/install-disk.yaml \
  --config-patch @talos/testing/patches/interface-names.yaml \
  --config-patch @talos/testing/patches/kubelet-certificates.yaml \
  --config-patch @talos/testing/patches/metallb.yaml \
  --config-patch-control-plane @talos/testing/patches/admissionControl.yaml \
  --config-patch-control-plane @talos/testing/patches/vip.yaml \
  --output talos/testing/rendered/

talosctl gen config testing-cluster https://192.168.20.90:6443 \
  --with-secrets  talos/testing/secrets.yaml \
  --config-patch @talos/testing/patches/allow-controlplane-workloads.yaml \
  --config-patch @talos/testing/patches/dhcp.yaml \
  --config-patch @talos/testing/patches/gpu-worker-patch.yaml \
  --config-patch @talos/testing/patches/install-disk+nvidia.yaml \
  --config-patch @talos/testing/patches/interface-names.yaml \
  --config-patch @talos/testing/patches/kubelet-certificates.yaml \
  --config-patch @talos/testing/patches/metallb.yaml \
  --config-patch-control-plane @talos/testing/patches/admissionControl.yaml \
  --config-patch-control-plane @talos/testing/patches/vip.yaml \
  --output talos/testing/rendered+nvidia/
```

## Install Talos

```
talosctl apply -f talos/testing/rendered/controlplane.yaml -n 192.168.20.100 --insecure
talosctl apply -f talos/testing/rendered/controlplane.yaml -n 192.168.20.101 --insecure
talosctl apply -f talos/testing/rendered+nvidia/controlplane.yaml -n 192.168.20.102 --insecure
```

## Copy talos config for talosctl

```
mkdir -p ~/.talos
cp talos/testing/rendered/talosconfig ~/.talos/config
```

## Configure the context's endpoints

```
talosctl config contexts

talosctl config endpoint 192.168.20.100 192.168.20.101 192.168.20.102
talosctl config node 192.168.20.100
```

## Bootstrap the cluster

```
talosctl bootstrap -n 192.168.20.100
```

## Generate a kubeconfig for kubectl

```
talosctl kubeconfig ~/.kube/talos-config
```

Based off of https://mirceanton.com/posts/2023-11-28-the-best-os-for-kubernetes/