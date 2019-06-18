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

# Init containers
Run certain scripts/procedures etc during POD startup

# Tricks

Throw-away debug container:  
`kubectl run -it --rm name --image=the_image --restart=Never -- sh `

busybox: `kubectl run -it --rm busybox --image=busybox --restart=Never -- sh`    
debian: `kubectl run -it --rm debian --image=debian --restart=Never -- bash`
