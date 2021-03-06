# 🚧 Step 6: Deploy a Composition

Read the official documentation about `Configuration`on the [Crossplane documentation website](https://crossplane.github.io/docs/v1.2/getting-started/create-configuration.html).

## Create a Composed Database

```bash
# Create a composed PostgreSQLInstance database.
# Composition available in https://github.com/crossplane/crossplane/tree/d04a059fe341a941760a3a94ef07085ae7158365/docs/snippets/package/gcp
# Docs on https://crossplane.io/docs/v1.3/getting-started/create-configuration.html
kubectl apply -f ./etc/composition/getting-started-gcp/definition.yaml && \
  kubectl apply -f ./etc/composition/getting-started-gcp/composition.yaml
echo """
apiVersion: database.example.org/v1alpha1
kind: PostgreSQLInstance
metadata:
  name: my-db
  namespace: default
spec:
  parameters:
    storageGB: 20
  compositionSelector:
    matchLabels:
      provider: gcp
  writeConnectionSecretToRef:
    name: db-conn
""" | kubectl create -f -
kubectl get managed
kubectl get postgresqlinstance my-db
kubectl describe postgresqlinstance my-db
kubectl describe cloudsqlinstance my-db
# kubectl describe cloudsqlinstance.database.gcp.crossplane.io/my-db-d8z4g-6rf6v
kubectl describe secrets db-conn
open https://console.cloud.google.com/sql/instances?project=$PROJECT_ID
```

```bash
# Destroy the PostgreSQLInstance database.
kubectl delete postgresqlinstance my-db
```

## Build your Configuration

```bash
# Taken from https://github.com/crossplane/crossplane/tree/master/docs/snippets/package/gcp
kubectl crossplane build configuration -f ./etc/composition/getting-started-gcp
ls ./etc/composition/getting-started/*.xpkg
```

```bash
# 🚧 Failing with cannot unpack package: failed to fetch package digest from remote: Get "https://./v2/": dial tcp: lookup .: no such host
kubectl crossplane install configuration ./etc/composition/getting-started-gcp/getting-started-with-gcp-....xpgk
kubectl get configuration
kubectl describe configuration
```

## Publish your Configuration

```bash
REGISTRY=gcr.io/datalayer-dev-1
kubectl crossplane push configuration ${REGISTRY}/getting-started-with-gcp:master
```

## Use a Configuration

```bash
# https://cloud.upbound.io/registry/upbound/platform-ref-gcp
# GKE + Network
kubectl crossplane install configuration registry.upbound.io/upbound/platform-ref-gcp:v0.0.2
# https://github.com/crossplane/crossplane/blob/master/.github/workflows/configurations.yml
# https://github.com/crossplane/crossplane/tree/master/docs/snippets/package/gcp
# crossplane/provider-gcp + PostgreSQL database
kubectl crossplane install configuration registry.upbound.io/xp/getting-started-with-gcp
watch kubectl get configuration
watch kubectl get pkg
watch kubectl get providers
kubectl get crd | grep database
kubectl get crd | grep postgresql
## Install the getting started with gcp configuration.
kubectl crossplane install configuration gcr.io/datalayer-dev-1/getting-started-with-gcp:master
```
