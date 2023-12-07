
    helm repo add runix https://helm.runix.net/
    helm repo update
    helm upgrade --install --namespace database pgadmin4 runix/pgadmin4 --version 1.18.5 --values ./production/database/pgadmin4/values.yaml
