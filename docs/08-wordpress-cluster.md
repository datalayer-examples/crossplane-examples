# Step 8: Deploy a Wordpress Cluster

## Define

Define your Wordpress cluster configuration composed of a GKE cluster, a database and a Wordpress Helm chart.

```bash
# Taken from https://github.com/crossplane-contrib/provider-helm/tree/master/examples/in-composition
kubectl apply -f ./etc/composition/wordpress-cluster/compose
kubectl get xrd | grep wordpressclusters
```

## Claim

Claim a Wordpress cluster.

```bash
kubectl apply -f ./etc/composition/wordpress-cluster/claim
```

Get the status of the Wordpress cluster.

```bash
watch kubectl get managed
kubectl get networks
kubectl get gkecluster
kubectl get cloudsqlinstances
kubectl get helm
kubectl get wordpressclusters
kubectl describe wordpressclusters
```

## Terminate

```bash
kubectl delete -f ./etc/composition/wordpress-cluster/claim
```

## Build

Once you are confident with your configuration, build, publish and use it as a package.

```bash
cd ./etc/wordpress-cluster/compoose && \
  kubectl crossplane build configuration
ls *.xpkg
REG=<YOUR_REGISTRY>
kubectl crossplane push configuration ${REG}/wordpresscluster:master
kubectl crossplane install configuration ${REG}/wordpresscluster:master
watch kubectl get configuration
watch kubectl get pkg
watch kubectl get providers
```
