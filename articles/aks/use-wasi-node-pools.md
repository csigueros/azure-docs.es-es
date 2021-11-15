---
title: Creación de grupos de nodos de Interfaz del sistema de WebAssembly (WASI) en Azure Kubernetes Service (AKS) para ejecutar la carga de trabajo de WebAssembly (WASM) (versión preliminar)
description: Obtenga información sobre cómo crear grupos de nodos de Interfaz del sistema de WebAssembly (WASI) en Azure Kubernetes Service (AKS) para ejecutar la carga de trabajo de WebAssembly (WASM) en Kubernetes.
services: container-service
ms.topic: article
ms.date: 10/12/2021
ms.openlocfilehash: 91649a6c7893970eee5685554385eed1493c0094
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265970"
---
# <a name="create-webassembly-system-interface-wasi-node-pools-in-azure-kubernetes-service-aks-to-run-your-webassembly-wasm-workload-preview"></a>Creación de grupos de nodos de Interfaz del sistema de WebAssembly (WASI) en Azure Kubernetes Service (AKS) para ejecutar la carga de trabajo de WebAssembly (WASM) (versión preliminar)

[WebAssembly (WASM)][wasm] es un formato binario optimizado para una descarga rápida y una velocidad de ejecución máxima en un entorno de ejecución WASM. Un entorno de ejecución WASM está diseñado para ejecutarse en una arquitectura de destino y ejecutar elementos WebAssembly en un espacio aislado, apartado del equipo host, con un rendimiento casi nativo. De manera predeterminada, los elementos WebAssembly no pueden acceder a los recursos del host fuera del espacio aislado a menos que se permita explícitamente y no se pueden comunicar mediante sockets para acceder a las variables de entorno o el tráfico HTTP. El estándar [Interfaz del sistema de WebAssembly (WASI)][wasi] define una API para que los entornos de ejecución WASM proporcionen a los elementos WebAssembly acceso al entorno y los recursos fuera del host mediante un modelo de funcionalidades. [Krustlet][krustlet] es un proyecto de código abierto que permite ejecutar módulos WASM en Kubernetes. Krustlet crea un kubelet que se ejecuta en nodos con un entorno de ejecución WASM/WASI. AKS permite crear grupos de nodos que ejecutan ensamblados WASM mediante nodos con entornos de ejecución WASM/WASI y Krustlets.

## <a name="before-you-begin"></a>Antes de empezar

Los grupos de nodos WASM/WASI están actualmente en versión preliminar.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

En este artículo, se usa [Helm 3][helm] para instalar el gráfico *nginx* en una versión de Kubernetes compatible. Asegúrese de que usa la versión más reciente de Helm y de que tiene acceso al repositorio *bitnami* de Helm. Es posible que los pasos descritos en este artículo no sean compatibles con versiones anteriores del gráfico de Helm o Kubernetes.

También debe tener instalado el siguiente recurso:

* La versión más reciente de la CLI de Azure.
* La versión 0.5.34 o posterior de la extensión `aks-preview`

### <a name="register-the-wasmnodepoolpreview-preview-feature"></a>Registro de la característica en vista previa (GB) `WasmNodePoolPreview`

Para usar la característica, también debe habilitar la marca de característica `WasmNodePoolPreview` en la suscripción.

Registre la marca de la característica `WasmNodePoolPreview` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "WasmNodePoolPreview"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WasmNodePoolPreview')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Instalación de la CLI de Azure `aks-preview`

También se necesita la versión 0.5.34 o posterior de la extensión *aks-preview* de la CLI de Azure. Instale la extensión de la CLI de Azure *aks-preview* mediante el comando [az extension add][az-extension-add]. También puede instalar las actualizaciones disponibles mediante el comando [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitaciones

* No puede ejecutar WebAssemblies y contenedores en el mismo grupo de nodos.
* Solo está disponible el entorno de ejecución WebAssembly (WASI), mediante el proveedor Wasmtime.
* Los grupos de nodos WASM/WASI no se pueden usar para el grupo de nodos del sistema.
* El *tipo de sistema operativo* de los grupos de nodos WASM/WASI debe ser Linux.
* Krustlet no funciona con Azure CNI en este momento. Para más información, consulte [Compatibilidad con CNI sobre el problema de GitHub de Krustlet][krustlet-cni-support].
* Krustlet no proporciona configuración de redes para los elementos WebAssembly. El manifiesto WebAssembly debe proporcionar la configuración de red, como la dirección IP.

## <a name="add-a-wasmwasi-node-pool-to-an-existing-aks-cluster"></a>Adición de un grupo de nodos WASM/WASI a un clúster de AKS existente

Para agregar un grupo de nodos WASM/WASI, utilice el comando [az aks node pool add][az-aks-nodepool-add]. En el ejemplo siguiente, se crea un grupo de nodos WASI llamado *mywasipool* con un nodo.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mywasipool \
    --node-count 1 \
    --workload-runtime wasmwasi 
```

> [!NOTE]
> El valor predeterminado del parámetro *workload-runtime* es *ocicontainer*. Para crear un grupo de nodos que ejecute cargas de trabajo de contenedor, omita el parámetro *workload-runtime* o establezca el valor en *ocicontainer*.

Compruebe el valor de *workloadRuntime* mediante `az aks nodepool show`. Por ejemplo:

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mywasipool
```

En la salida de ejemplo siguiente, se muestra que *mywasipool* tiene el tipo de *workloadRuntime* establecido en *WasmWasi*.

```output
{
  ...
  "name": "mywasipool",
  ..
  "workloadRuntime": "WasmWasi"
}
```

En el caso de un grupo de nodos WASM/WASI, compruebe que la marca esté establecida en `kubernetes.io/arch=wasm32-wagi:NoSchedule` y `kubernetes.io/arch=wasm32-wagi:NoExecute`, lo que impedirá que se programen pods de contenedor en este grupo de nodos. Además, debería ver que el valor de nodeLabels es `kubernetes.io/arch: wasm32-wasi`, lo que impide que se programen pods WASM en grupos de nodos de contenedores normales (OCI).

> [!NOTE]
> Las marcas de un grupo de nodos WASI no son visibles mediante `az aks nodepool list`. Use `kubectl` para comprobar que las marcas estén establecidas en los nodos del grupo de nodos WASI.

Para configurar `kubectl` para conectarse a su clúster de Kubernetes, use el comando [az aks get-credentials][az-aks-get-credentials]. El siguiente comando:  

```azurecli
az aks get-credentials -n myakscluster -g myresourcegroup
```

Utilice `kubectl get nodes` para mostrar los nodos del clúster.

```output
$ kubectl get nodes -o wide
NAME                                 STATUS   ROLES   AGE   VERSION         INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
aks-mywasipool-12456878-vmss000000   Ready    agent    9m   1.0.0-alpha.1   WASINODE_IP   <none>        <unknown>            <unknown>          mvp
aks-nodepool1-12456878-vmss000000    Ready    agent   13m   v1.20.9         NODE1_IP      <none>        Ubuntu 18.04.6 LTS   5.4.0-1059-azure   containerd://1.4.9+azure
```

Guarde el valor de *WASINODE_IP*, ya que se utiliza en un paso posterior.

Use `kubectl describe node` para mostrar las etiquetas y las marcas de un nodo del grupo de nodos WASI. En el ejemplo siguiente, se muestran los detalles de *aks-mywasipool-12456878-vmss000000*.

```output
$ kubectl describe node aks-mywasipool-12456878-vmss000000

Name:               aks-mywasipool-12456878-vmss000000
Roles:              agent
Labels:             agentpool=mywasipool
...
                    kubernetes.io/arch=wasm32-wagi
...
Taints:             kubernetes.io/arch=wasm32-wagi:NoExecute
                    kubernetes.io/arch=wasm32-wagi:NoSchedule
```


## <a name="running-wasmwasi-workload"></a>Ejecución de una carga de trabajo WASM/WASI

Para ejecutar una carga de trabajo en un grupo de nodos WASM/WASI, agregue un selector de nodos y tolerancias a la implementación. Por ejemplo:

```yml
...
spec:
  nodeSelector:
    kubernetes.io/arch: "wasm32-wagi"
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
...
```

Para ejecutar una implementación de ejemplo, cree un archivo `wasi-example.yaml` con la siguiente definición YAML:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: krustlet-wagi-demo
  labels:
    app: krustlet-wagi-demo
  annotations:
    alpha.wagi.krustlet.dev/default-host: "0.0.0.0:3001"
    alpha.wagi.krustlet.dev/modules: |
      {
        "krustlet-wagi-demo-http-example": {"route": "/http-example", "allowed_hosts": ["https://api.brigade.sh"]},
        "krustlet-wagi-demo-hello": {"route": "/hello/..."},
        "krustlet-wagi-demo-error": {"route": "/error"},
        "krustlet-wagi-demo-log": {"route": "/log"},
        "krustlet-wagi-demo-index": {"route": "/"}
      }
spec:
  hostNetwork: true
  nodeSelector:
    kubernetes.io/arch: wasm32-wagi
  containers:
    - image: webassembly.azurecr.io/krustlet-wagi-demo-http-example:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-http-example
    - image: webassembly.azurecr.io/krustlet-wagi-demo-hello:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-hello
    - image: webassembly.azurecr.io/krustlet-wagi-demo-index:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-index
    - image: webassembly.azurecr.io/krustlet-wagi-demo-error:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-error
    - image: webassembly.azurecr.io/krustlet-wagi-demo-log:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-log
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
```

Utilice `kubectl` para ejecutar la implementación de ejemplo:

```azurecli-interactive
kubectl apply -f wasi-example.yaml
```

> [!NOTE]
> El pod de la implementación de ejemplo puede permanecer en el estado *Registrado*. Este comportamiento es el esperado y debe continuar en el paso siguiente.

Cree el archivo `values.yaml` con el siguiente código YAML de ejemplo, reemplazando *WASINODE_IP* por el valor del paso anterior.

```yml
serverBlock: |-
  server {
    listen 0.0.0.0:8080;
    location / {
            proxy_pass http://WASINODE_IP:3001;
    }
  }
```

Con [Helm][helm], agregue el repositorio *bitnami* e instale el gráfico *nginx* con el archivo `values.yaml` que creó en el paso anterior. Al instalar NGINX con el archivo `values.yaml` anterior, se crea un proxy inverso a la implementación de ejemplo, lo que le permite acceder a ella mediante una dirección IP externa.

>[!NOTE]
> En el ejemplo siguiente se extrae una imagen de contenedor público de Docker Hub. Se recomienda configurar un secreto de extracción para autenticarse mediante una cuenta de Docker Hub en lugar de realizar una solicitud de extracción anónima. Para mejorar la confiabilidad al trabajar con contenido público, importe y administre la imagen en un registro de contenedor privado de Azure. [Más información sobre cómo trabajar con imágenes públicas.][dockerhub-callout]

```console
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install hello-wasi bitnami/nginx -f values.yaml
```

Use `kubectl get service` para mostrar la dirección IP externa del servicio *hello-wasi-ngnix*.

```output
$ kubectl get service
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
hello-wasi-nginx   LoadBalancer   10.0.58.239   EXTERNAL_IP      80:32379/TCP   112s
kubernetes         ClusterIP      10.0.0.1      <none>           443/TCP        145m
```

Compruebe que la implementación de ejemplo esté en ejecución mediante el comando `curl` en la ruta de acceso `/hello` de *EXTERNAL_IP*.

```azurecli-interactive
curl EXTERNAL_IP/hello
```

La salida del ejemplo siguiente confirma que la implementación de ejemplo está en ejecución.

```output
$ curl EXTERNAL_IP/hello
hello world
```

> [!NOTE] 
> Para publicar el servicio en su propio dominio, consulte [Azure DNS][azure-dns-zone] y el proyecto [external-dns][external-dns].

## <a name="clean-up"></a>Limpieza

Para quitar NGINX, use `helm delete`.

```console
helm delete hello-wasi
```

Para quitar la implementación de ejemplo, use `kubectl delete`.

```azurecli-interactive
kubectl delete -f wasi-example.yaml
```

Para quitar el grupo de nodos WASM/WASI, use `az aks nodepool delete`.

```azurecli-interactive
az aks nodepool delete --name mywasipool -g myresourcegroup --cluster-name myakscluster
```


<!-- EXTERNAL LINKS -->
[helm]: https://helm.sh/
[krustlet]: https://krustlet.dev/
[krustlet-cni-support]: https://github.com/krustlet/krustlet/issues/533
[wasm]: https://webassembly.org/
[wasi]: https://wasi.dev/
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- INTERNAL LINKS -->

[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[dockerhub-callout]: ../container-registry/buffer-gate-public-content.md
[install-azure-cli]: /cli/azure/install-azure-cli
[use-multiple-node-pools]: use-multiple-node-pools.md
[use-system-pool]: use-system-pools.md
