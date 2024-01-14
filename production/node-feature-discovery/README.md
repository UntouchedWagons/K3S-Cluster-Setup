
    helm repo add node-feature-discovery https://kubernetes-sigs.github.io/node-feature-discovery/charts
    helm repo update

    helm upgrade --install node-feature-discovery node-feature-discovery/node-feature-discovery --version 0.14.3
    kubectl apply -f testing/node-feature-discovery/rules.yaml
