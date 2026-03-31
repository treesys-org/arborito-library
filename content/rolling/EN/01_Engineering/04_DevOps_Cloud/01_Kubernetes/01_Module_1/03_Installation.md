
@title: Local install and kubectl
@icon: 🛠️
@description: kind, Minikube, kubeconfig, and first safe commands.
@order: 3

# Practice clusters

You want a **disposable** cluster for learning. Common options: **kind** (Kubernetes in Docker) and **Minikube** (VM or container driver). Managed clusters (EKS, GKE, AKS…) share the same API and `kubectl`.

@section: kind

Requires Docker (or compatible Podman). Nodes run as containers.

```bash
kind create cluster --name course
kubectl cluster-info --context kind-course
kubectl get nodes
```

Delete: `kind delete cluster --name course`.

@section: Minikube

```bash
minikube start
minikube status
kubectl get nodes
```

Handy: `minikube service …` for NodePort tunnels locally.

@section: kubectl and kubeconfig

`kubectl` reads **`~/.kube/config`**: contexts, clusters, credentials, default **namespace**.

```bash
kubectl config current-context
kubectl config get-contexts
kubectl get pods -A
kubectl api-resources
kubectl explain pod
```

@section: Good habits

*   Do not practice on production without permission.
*   Align **kubectl client** minor with the **server** when practical.
*   Enable shell completion for `kubectl`.

@quiz: Which file usually defines the cluster/context kubectl uses?
@option: /etc/kubernetes/admin.conf on your laptop only
@correct: ~/.kube/config (kubeconfig)
@option: Only the DOCKER_HOST variable
