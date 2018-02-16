# Kubernetes & Docker examples at Nucleus

[We](https://www.nucleus.be) run a fairly default Kubernetes installation in our VMware environment. Most of the [Kubernetes documentation](https://kubernetes.io/docs/home/) also applies to our setup.

It is highly recommended to read about the [concepts](https://kubernetes.io/docs/concepts/) and [tasks](https://kubernetes.io/docs/tasks/) section of the Kubernetes docs before continueing.

# Your Kubernetes configuration

Our support department will give you your Kubernetes configuration file, tailored to your setup.

To see an example, have a look in [kubetl.config-example](tree/master/kubectl.config-example).

Once you have your configuration, save it on your local machine as `kubectl.config`.

# Talk to your Kubernetes cluster

With a working config, you can now talk to your Kubernetes cluster via the CLI by referencing your configuration file.

```
$ kubectl --kubeconfig=kubectl.config get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.248.0.1   <none>        443/TCP   1d
```

You can run multiple clusters by referencing a different config file with `---kubeconfig` for your `kubectl` command.

# Persistent storage in Kubernetes

The persistence layer is handled by VMware. Creating a persistent volume consists of 2 steps.

First, create a persistent volume claim.

```
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mariadb-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

The above example will create a persistent volume named `mariadb-pv-storage` that is `3GB` in size.

You'll want to consider a long-term naming scheme for your persistent volumes before you start creating new volumes.

To use this storage claim, your config will look like this.

```
---
apiVersion: v1
kind: ReplicationController
metadata:
  name: mariadb-rc
spec:
  replicas: 1
  selector:
    app: mariadb
  template:
    metadata:
      name: mariadb-rc
      labels:
        app: mariadb
    spec:
      volumes:
      - name: mariadb-pv-storage
        persistentVolumeClaim:
          claimName: mariadb-pv-claim
      containers:
      - name: mariadb
        image: mariadb:latest
        ports:
        - containerPort: 3306
        volumeMounts:
        - mountPath: "/var/lib/mysql"
          name: mariadb-pv-storage
```

In this example, the following lines are the most important:

```
    spec:
      volumes:
      - name: mariadb-pv-storage
        persistentVolumeClaim:
          claimName: mariadb-pv-claim
      containers:
        volumeMounts:
        - mountPath: "/var/lib/mysql"
          name: mariadb-pv-storage
```

This uses the previously claimed space and mounts it on `/var/lib/mysql` inside your `mariadb` container.