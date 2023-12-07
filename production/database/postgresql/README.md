
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo update
    helm upgrade --install postgresql bitnami/postgresql --create-namespace --namespace database --version 13.2.24 --values ./production/database/postgresql/values.yaml