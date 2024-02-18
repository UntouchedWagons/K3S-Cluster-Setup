
```
helm repo add rook-release https://charts.rook.io/release
helm install --create-namespace --namespace rook-ceph rook-ceph rook-release/rook-ceph
helm install --create-namespace --namespace rook-ceph rook-ceph-cluster rook-release/rook-ceph-cluster -f rook-ceph/rook-ceph-cluster/values.yaml -f production/rook-ceph/rook-ceph-cluster/values.yaml
```