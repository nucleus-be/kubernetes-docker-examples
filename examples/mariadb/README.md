# A MariaDB container on Kubernetes

This example uses:

- The latest publicly available MariaDB instance
- A persistent volume of 3GB, mounted on `/var/lib/mysql`

To create:

```
$ kubectl --kubeconfig=kubectl.config create -f mariadb.yaml
replicationcontroller "mariadb-rc" created
```

To see the state:

```
$ kubectl --kubeconfig=kubectl.config get pods
NAME               READY     STATUS    RESTARTS   AGE
mariadb-rc-mm7k8   0/1       Pending   0          59s
```

All details of the pod:

```
$ kubectl --kubeconfig=kubectl.config describe pod mariadb-rc-mm7k8
Name:           mariadb-rc-mm7k8
Namespace:      default
Node:           <none>
...
```