# Expose one of your containers to the outside world

This example uses:

- A service running on port 9376
- It will expose it on port 80

To create:

```
$ kubectl --kubeconfig=kubectl.config create -f expose-service.yaml
```

Alternatively, you can do this via `kubetctl` directly.

```
$ kubectl --kubeconfig=kubectl.config -n monitoring expose deployment prometheus --port=80 --target-port=9090 --type=LoadBalancer
```

An IP address will be automatically assigned by us.
