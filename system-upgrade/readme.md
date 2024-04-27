
Upgrading your k3s cluster taken from [here](https://docs.k3s.io/upgrades/automated).

```
kubectl apply -f https://github.com/rancher/system-upgrade-controller/releases/latest/download/system-upgrade-controller.yaml
kubectl apply -f https://github.com/rancher/system-upgrade-controller/releases/latest/download/crd.yaml
kubectl apply -f system-upgrade/plan.yaml
```
