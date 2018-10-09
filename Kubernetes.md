Kubernetes is the **container** orchestration tool.  
Its primary job it to ensure that given container is running with regards to given constraints (e.g container X requires N mb of memory or container X must be replicated N times on different physical nodes).

Kubernetes covers more use-cases: it is a platform for automatic deployment, scaling, HA and orchestration of containerized applications

## Vocabulary
| Term | Meaning |
| - | - |
| Namespace | Virtual cluster, scope for names |
| Label | Key-value pair used to group set of objects |
| Node | Worker machine, runs _Services_, capable of running _Pods_. Either VM or physical machine |
| Pod | Representation of one or more application containers (Docker or [rkt](https://github.com/rkt/rkt)) or shared resources for those containers |
| Deployment | Configuration how to create/update instances of the application |
| Service | Set of _Pods_ with policy how to access them (e.g. load balancing or service discovery for _pods_) |
| Controller | _Pods_ manager (handles, e.g., pod replication) |

Containers within one _Pod_ share IP address. Tightly coupled containers should run within one Pod. Pod provides two kinds of shared (by pod's containers) resources: _networking_ and _storage_. Services match a set of pods using labels and selectors. Services are published or discovered either via DNS or environmental variables

# Setup
Setup depends on the number of nodes used for cluster

## Single-node
Use [minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/).  
It uses either KVM or VirtualBox as docker host.  
In order to increase default limits of the VM, it must be destroyed first: `minikube delete; minikube start --memory 12288`

## Multi-node


# API
`Kubectl` is used to interact with the cluster.  
If you have multiple clusters, list them with: `kubectl config get-contexts`, switch between them with: `kubectl config use-context CONTEXT_NAME`

### Overview
In order to get detailed information about any part of your deployment use `kubectl describe <kind>`.  
The term `kind` is defined in this [manual](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) (it can simply be a `pod`, `service` or `deployment`)

# References
1. https://kubernetes.io/docs/user-journeys/users/application-developer/foundational/
2. https://coreos.com/rkt/docs/latest/rkt-vs-other-projects.html