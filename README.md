# Crossplain API Postgress

## Deployment Order
To set up this platform, apply the manifests in the following order:

1. **Prerequisites**:
```bash
    helm repo add crossplane-stable https://charts.crossplane.io/stable
    helm repo update

    helm install crossplane crossplane-stable/crossplane \
    --namespace crossplane-system \
    --create-namespace \
    --version 1.18.0

    kubectl apply -f storageclaim/gp3_storageclass.yaml 
    kubectl apply -f postgres/postgres.yaml

    kubectl apply -f kubernetes-provider.yaml
    kubectl apply -f provider-config.yaml
```
2. **The Platform**:
   `kubectl apply -f api/xappdatabase-xrd.yaml`
   `kubectl apply -f compositions/xappdatabase-composition.yaml`

3. **The User Request (The Claim)**:
   `kubectl apply -f claims/example-xappdatabase.yaml`
