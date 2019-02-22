# Setup
Setup depends on the number of nodes used for cluster

## Single-node
Use [minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/).  
It uses either KVM or VirtualBox as docker host.  
In order to increase default limits of the VM, it must be destroyed first: `minikube delete; minikube start --memory 12288`

## Multi-node
