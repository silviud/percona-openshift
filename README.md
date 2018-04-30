# percona-openshift
Set of scripts to run Percona software in OpenShift / Kubernetes / Google Cloud Kubernetes Engine

## MySQL Passwords
Before deployments you need to create passwords (secrets) which will be used to access Percona Server / Percona XtraDB Cluster.
We provide file https://github.com/Percona-Lab/percona-openshift/blob/master/deploy/secret.yaml as an example. **Please use your own secure passwords!**

Use `base64` to encode a password for `secret.yaml` : `echo -n 'securepassword' | base64`.

Used `base64 -d` to decode a password from `secret.yaml` : `echo YmFja3VwX3Bhc3N3b3Jk | base64 -d`.


## Considerations
The proposed depoyments were tested on Kubernetes 1.9 / OpenShift Origin 3.9. The earlier versions may not work.

The deployments assume you have a default `StorageClass` which will provide Persistent Volumes. If not, you need to create `PersistentVolume` manually.

## Deployments

### Master with N Slaves

We use `replica-set.yaml` to create a master with multiple slaves. The total amount of nodes is defined in `replicas: 2`.

To scale an existing depoyment you can use `kubectl scale --replicas=5 statefulsets/rsnode` - this will scale the total amount of nodes to 5 (That is 1 master and 4 slaves)

TODO:
- [ ] Create ProxySQL service to handle master-slaves deployments

### Percona XtraDB Cluster N nodes

Deployment `pxc.yaml` will create a StatefulSet with N nodes (defined in `replicas: 3`)
Pay attention to the service name, defined in `name: pxccluster1`

### ProxySQL service over Percona XtraDB Cluster

Deployment `proxysql.yaml` will create ProxySQL service and automatically configure to handle a traffic to Percona XtraDB Cluster service.
The service to handled is defined in line: `- -service=pxccluster1`

