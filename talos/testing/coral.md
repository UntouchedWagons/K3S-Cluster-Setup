
## Cleaning house

```
rm -Rf ~/.talos ~/.kube/talos-config talos/testing/rendered talos/testing/rendered+nvidia talos/testing/rendered+coral talos/testing/secrets.yaml
```

## Getting started

```
talosctl gen secrets --output-file talos/testing/secrets.yaml

talosctl gen config coral-test https://192.168.20.105:6443 \
  --with-secrets  talos/testing/secrets.yaml \
  --config-patch @talos/testing/patches/allow-controlplane-workloads.yaml \
  --config-patch @talos/testing/patches/dhcp.yaml \
  --config-patch @talos/testing/patches/install-disk+coral.yaml \
  --config-patch @talos/testing/patches/interface-names.yaml \
  --config-patch @talos/testing/patches/kubelet-certificates.yaml \
  --config-patch-control-plane @talos/testing/patches/admissionControl.yaml \
  --output talos/testing/rendered+coral/
```

## Install Talos

```
talosctl apply -f talos/testing/rendered+coral/controlplane.yaml -n 192.168.20.105 --insecure
```

## Copy talos config for talosctl

```
mkdir -p ~/.talos
cp talos/testing/rendered+coral/talosconfig ~/.talos/config
```

## Configure the context's endpoints

```
talosctl config contexts

talosctl config endpoint 192.168.20.105
talosctl config node 192.168.20.105
```

## Bootstrap the cluster

```
talosctl bootstrap -n 192.168.20.105
```

## Generate a kubeconfig for kubectl

```
talosctl kubeconfig ~/.kube/talos-config
```

Based off of https://mirceanton.com/posts/2023-11-28-the-best-os-for-kubernetes/