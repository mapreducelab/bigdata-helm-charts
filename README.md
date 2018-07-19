# Bigdata Helm Charts

Collection of Kubernetes Big Data ecosystem products helm charts

# Install helm

Installs client and tiller(server side) helm component.

```
kubectl create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default
helm init --tiller-image=art-hq.intranet.qualys.com:5001/k8s.gcr.io/kubernetes-helm/tiller:v2.9.1
```

# Order to bootstrap big data cluster

1. `rook-operator` - Creates a custom Kubernetes resource with Rook operator.
2. `rook-cluster` - Installs CEPH on your k8s cluster and creates an object store, external dashboard, toolbox, and external gateway.
3. `postgresql` - Creates PostgreSQL database for Hive metastore.
4. `alluxio` - Creates Alluxio cluster with 1 master and workers.
5. `spark-master` - Creates spark master.
6. `spark-worker` - Creates spark worker.
7. `spark-proxy` - Creates spark proxy.
8. `zeppelin` - Creates Zeppelin notebook.
9. `spark-hadoop-master` - Creates spark master with hadoop and hive 1.2.1.
10. `spark-hadoop-worker` - Creates spark worker with hadoop and hive 1.2.1.
11. `spark-hadoop-proxy` - Creates spark proxy with hadoop and hive 1.2.1.

# Configure s3cmd to work with CEPH directly

Create config file and replace following variables.

Configuration template - [s3cmd.cfg.template](s3cmd.cfg.template)

```
kubectl -n rook-ceph exec -it rook-ceph-tools bash
radosgw-admin user create --uid=<username> --display-name=<username>
mv s3cmd.cfg.template s3cmd.cfg
vim s3cmd.cfg                               # substitute variables
s3cmd -c s3cmd.cfg ls
```

# FAQ

### How to delete rook-ceph namespace if it is stuck in `terminating` stage?

Removal of ThirdPartyResources solves the problem.

```
kubectl patch crd clusters.ceph.rook.io -p '{"metadata":{"finalizers": []}}' --type=merge
```

### How to verify that Rook completely removed?

Run the following commands and check the output.

```
kubectl get sa --all-namespaces
kubectl get cm --all-namespaces
kubectl get crd
kubectl get ns
```
