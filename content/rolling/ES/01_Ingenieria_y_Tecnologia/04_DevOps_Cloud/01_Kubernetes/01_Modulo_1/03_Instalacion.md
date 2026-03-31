
@title: Instalación local y kubectl
@icon: 🛠️
@description: kind, Minikube, kubeconfig y primeros comandos seguros.
@order: 3

# Instalar un clúster de práctica

Para aprender necesitas un clúster **desechable**. Las dos vías más habituales son **kind** (Kubernetes in Docker) y **Minikube** (VM o contenedor según driver). En entornos corporativos usarás clústeres gestionados (EKS, GKE, AKS, etc.), pero la API y `kubectl` son las mismas.

@section: kind (recomendado en CI y laptops)

Requiere Docker (o Podman en modo compatible). Crea nodos como contenedores.

```bash
kind create cluster --name curso
kubectl cluster-info --context kind-curso
kubectl get nodes
```

Eliminar: `kind delete cluster --name curso`.

@section: Minikube

```bash
minikube start
minikube status
kubectl get nodes
```

Útil: `minikube service …` para abrir túneles a Services tipo NodePort en local.

@section: kubectl y kubeconfig

`kubectl` lee **`~/.kube/config`**: contextos, clusters, usuarios y **namespace** por defecto. Comandos frecuentes:

```bash
kubectl config current-context
kubectl config get-contexts
kubectl get pods -A
kubectl api-resources
```

Usa **`kubectl explain pod`** (o `deployment`, `service`…) para ver campos del esquema sin salir de la terminal.

@section: Buenas prácticas

*   No practiques en producción sin permiso.
*   Versiones: alinea **cliente kubectl** con la **minor** del servidor cuando sea posible.
*   Activa **completado** en bash/zsh para `kubectl`.

@quiz: ¿Qué fichero suele definir el contexto y el clúster al que apunta kubectl?
@option: /etc/kubernetes/admin.conf en tu laptop
@correct: ~/.kube/config (kubeconfig)
@option: Solo la variable DOCKER_HOST
