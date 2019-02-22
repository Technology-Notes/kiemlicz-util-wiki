# kube-apiserver
Master only component. Accepts user request. Stores resources definitions in `etcd`.

# kube-scheduler
Master only component. Materializes user requests, `watch`es the kube-apiserver, decides where and when schedule PODs.
PODs definition may contain some data that affects `kube-scheduler`:
 - `affinity/anti-affinity`
 - `nodeSelector`
 - `taints/tolerations`
 - `reservations/limits`
 
It is possible to write custom scheduler 

# kube-controller-manager
Master only component. Monitors `*-controller`s, e.g., `deployment-controller`
 
# kubelet
Master/Worker component. Resides on every Node. Connects to the `kube-apiserver`. Starts the actual containers via the container runtime. 
Provides health-checks

# kube-proxy
Master/Worker component. Main network component, watches the `service`s and materializes their rule on the Nodes (e.g. handles `iptables`)
