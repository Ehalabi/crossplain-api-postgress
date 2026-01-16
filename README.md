# Crossplain API Postgress
This project defines a custom Kubernetes API using Crossplane for provisioning PostgreSQL databases.

By applying a single Kubernetes resource XAppDatabase, Crossplane creates a Kubernetes Job that connects to a PostgreSQL instance and creates:

* A database
* A schema
* One table with predefined columns

At the moment, the Composition uses hard-coded values.

This means:

* The same database name is always created
* The same table structure is always created
* The values defined in the claim are validated by the API, but not yet dynamically used by the Composition

Every claim will result in creating the same database and tables.

## How It Works

1. A custom resource (`XAppDatabase`) is defined using a CompositeResourceDefinition (XRD)
2. A Composition maps that resource to a Kubernetes Job
3. The Job runs `psql` commands against an existing PostgreSQL instance
4. The database and tables are created if they do not already exist

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

    # Gives cluster admin role for crossplane k8s provider, should be changed with right rbac roles
    kubectl create clusterrolebinding provider-kubernetes-admin \
    --clusterrole=cluster-admin \
    --serviceaccount=crossplane-system:provider-kubernetes-83c72134f895
```
2. **The Platform**:
   `kubectl apply -f api/xappdatabase-xrd.yaml`
   `kubectl apply -f compositions/xappdatabase-composition.yaml`

3. **The User Request (The Claim)**:
   `kubectl apply -f claims/simple-xappdatabase.yaml`
