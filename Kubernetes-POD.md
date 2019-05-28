Main execution unit of Kubernetes cluster
  
`kubectl get pods -o wide` will inform about status, number of restarts, POD readiness or assigned node.  

# Status
The status roughly traverses:
Pending -> Running -> Succeeded/Failed -> Completed -> CrashLoopBackOff (if policy says so)

The POD default `restartPolicy` is `Always`, which means that if POD gets to Completed state, it will restart automatically.

To specify container command and/or args: `command`, `args`. 
If the Docker runtime is used then the `command` will override `ENTRYPOINT` and `args` will override `CMD`

Minimalistic POD yaml example:
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: pod
  name: example
spec:
#  restartPolicy: OnFailure
  containers:
    - image: httpd
      name: example
 #     command:
 #      - "echo"
 #      - "A"
```

# Probe
Such POD once reaches the Running state, is immediately marked as Ready (`kubectl get pod example`).
This is due to the fact that example POD didn't provide `readinessProbe` - the mechanism which informs the Kubernetes that
the POD is ready to accept the traffic.  
Example enriched with `readinessProbe`
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: pod
  name: example
spec:
  containers:
    - image: httpd
      name: example
      readinessProbe:
        exec:
            command:
              - cat
              - /tmp/healthy
        initialDelaySeconds: 5
        periodSeconds: 5
```

There are two types of probes:
1. `livenessProbe` - inform that the POD needs to be restarted
2. `readinessProbe` - inform that the POD won't accept traffic

Both of them use three different types of materializing checks: 
1. `exec` execute arbitrary command
2. `httpGET` perform HTTP GET request and succeed if 2XX code received
3. `tcpSocket` open TCP socket and try to establish a connection (three-way handshake only)

Both probes accept different thresholds, periods and inital delays.  
Using `port-forward` for accessing non-ready PODs bypasses the non-routing restriction.

# Volume
Essentialy - a directory that will be accessible by POD, preserving state independently of POD lifecycle.  
Example (simplest) usage of volume
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: pod
  name: example
spec:
  containers:
    - image: httpd
      name: example
      volumeMounts:
      - mountPath: /var/tmp
        name: vartmp
  volumes:
  - name: vartmp
    hostPath:
      path: /mnt/testing 
```
There are [numerous volume types](https://kubernetes.io/docs/concepts/storage/volumes/) that can be used, `hostPath` is actually the most test-purpose only.  
It is possible to mount `ConfigMap` as a volume.  

Typically you have `PersistentVolume` (PV) _resource_ (or have some Cloud provider prepare that for you) and you just mount it to the PODs.
The `persistentVolumeClaim` (PVC) is used as that glue. The PVC represents the request for storage.

There are two main types of Persistent Volumes provisioning:
1. Static
It is the administrator that prepares volumes. Use `selector` in PVC, without providing `storageClassName` to use static provisioning

2. Dynamic 
Based on `StorageClass` the `PersistentVolume` will be automatically created for given PVC. Use `storageClassName`.

Example POD with `PersistentVolume` Statically provisioned (`StorageClass` is not used at all)
```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
  labels:
    name: example-pv
spec:
  capacity:
    storage: 1Mi  
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  ##storageClassName: local-storage
  #hostPath:
  #  path: /mnt/testing
  local:
    path: /mnt/testing
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - k8s1
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  ##storageClassName: local-storage
  selector:
    matchLabels:
        name: example-pv
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Mi
--- 
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: pod
  name: example
spec:
  containers:
    - image: httpd
      name: example
      volumeMounts:
      - mountPath: /var/tmp
        name: vartmp
  volumes:
  - name: vartmp
    persistentVolumeClaim:
      claimName: example-pvc
```

# Init containers
Run certain scripts/procedures etc during POD startup

# Tricks

Throw-away debug container:
`kubectl run -it --rm busybox --image=busybox --restart=Never -- sh `
