# Basics
Kubernetes is a platform for automatic deployment, scaling, HA and orchestration of containerized applications

## Vocabulary
| Term | Meaning |
| - | - |
| Namespace | Virtual cluster, scope for names |
| Node | Worker machine, runs _Services_, capable of running _Pods_. Either VM or physical machine |
| Pod | Representation of one or more application containers (Docker or [rkt](https://github.com/rkt/rkt)) or shared resources for those containers |
| Deployment | Configuration how to create/update instances of the application |
| Service | Set of _Pods_ with policy how to access them (e.g. load balancing or service discovery for _pods_) |
| Controller | _Pods_ manager (handles, e.g., pod replication) |

Containers within one _Pod_ share IP address. Tightly coupled containers should run within one Pod. Pod provides two kinds of shared (by pod's containers) resources: _networking_ and _storage_. Services match a set of pods using labels and selectors.

# Features

## API
`Kubectl` is used to interact with the cluster.  
If you have multiple clusters, list them with: `kubectl config get-contexts`, switch between them with: `kubectl config use-context CONTEXT_NAME`

# References
1. https://kubernetes.io/docs/user-journeys/users/application-developer/foundational/
2. https://coreos.com/rkt/docs/latest/rkt-vs-other-projects.html