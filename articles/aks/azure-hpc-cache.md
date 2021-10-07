---
title: Integración de Azure HPC Cache con Azure Kubernetes Service
description: Aprender a integrar HPC Cache con Azure Kubernetes Service
services: container-service
author: jbut
ms.author: jebutl
ms.topic: article
ms.date: 09/08/2021
ms.openlocfilehash: 9cf8e3a6450787a65e21fcca79f8886efe016d8b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699629"
---
# <a name="integrate-azure-hpc-cache-with-azure-kubernetes-service"></a>Integración de Azure HPC Cache con Azure Kubernetes Service

[Azure HPC Cache][hpc-cache] acelera el acceso a los datos para las tareas de informática de alto rendimiento (HPC). Mediante el almacenamiento en caché de archivos en Azure, Azure HPC Cache aporta la escalabilidad de la informática en la nube al flujo de trabajo existente. En este artículo se le muestra cómo integrar Azure HPC Cache con Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, consulte el inicio rápido de AKS [mediante la CLI de Azure][aks-quickstart-cli] o [mediante Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Su clúster de AKS debe estar [en una región que admita Azure HPC Cache][hpc-cache-regions].

También tiene que instalar y configurar la versión 2.7 de la CLI de Azure u otra versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][install-azure-cli].  Consulte [hpc-cache-cli-prerequisites] para obtener más información sobre el uso de la CLI de Azure con HPC Cache.

También tendrá que instalar la extensión de la CLI de Azure hpc-cache.  Haga lo siguiente:

```azurecli
az extension add --upgrade -n hpc-cache
```

## <a name="set-up-azure-hpc-cache"></a>Configuración de Azure HPC Cache

En esta sección se explican los pasos para crear y configurar una instancia de HPC Cache.

### <a name="1-find-the-aks-node-resource-group"></a>1. Búsqueda del grupo de recursos del nodo de AKS

En primer lugar, obtenga el nombre del grupo de recursos con el comando [az aks show][az-aks-show] y agregue el parámetro de consulta `--query nodeResourceGroup`. Creará su grupo de HPC Cache en el mismo grupo de recursos.

En este ejemplo se obtiene el nombre del grupo de recursos del nodo para el clúster de AKS denominado *myAKSCluster* en el nombre del grupo de recursos *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

### <a name="2-create-the-cache-subnet"></a>2. Creación de la subred de caché

Hay una serie de [requisitos previos][hpc-cache-prereqs] que deben cumplirse antes de ejecutar una instancia de HPC Cache.  Lo más importante es que la memoria caché requiere una subred *dedicada* con al menos 64 direcciones IP disponibles. Esta subred no debe hospedar otras máquinas virtuales o contenedores.  Esta subred debe ser accesible desde los nodos de AKS.

Cree la subred de HPC Cache dedicada:

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --address-prefixes 10.0.0.0/26
```

Registre el proveedor de recursos *Microsoft.StorageCache*:

```azurecli
az provider register --namespace Microsoft.StorageCache --wait
```

> [!NOTE]
> El registro del proveedor de recursos puede tardar algún tiempo en completarse.

### <a name="3-create-the-hpc-cache"></a>3. Creación de una instancia de HPC Cache

Cree una instancia de HPC Cache en el grupo de recursos de nodo del paso 1 y en la misma región que el clúster de AKS. Use [az hpc-cache create][az-hpc-cache-create].

> [!NOTE]
> La instancia de HPC Cache tarda aproximadamente 20 minutos en crearse.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
az hpc-cache create \
  --resource-group $RESOURCE_GROUP \
  --cache-size-gb "3072" \
  --location eastus \
  --subnet $SUBNET_ID \
  --sku-name "Standard_2G" \
  --name MyHpcCache
```

### <a name="4-create-a-storage-account-and-new-container"></a>4. Creación de una cuenta de almacenamiento y un contenedor

Cree la cuenta de Azure Storage para el contenedor de Blob Storage.  La instancia de HPC Cache almacenará en caché el contenido almacenado en este contenedor de Blob Storage.

> [!IMPORTANT]
> Tendrá que seleccionar un nombre de cuenta de almacenamiento único.  Reemplace "uniquestorageaccount" por algo que será único para usted.

Compruebe que el nombre de la cuenta de almacenamiento que ha seleccionado está disponible.

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account check-name --name $STORAGE_ACCOUNT_NAME
```

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account create \
  -n $STORAGE_ACCOUNT_NAME \
  -g $RESOURCE_GROUP \
  -l eastus \
  --sku Standard_LRS
```

Cree el contenedor de blobs en la cuenta de almacenamiento.

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
AD_USER=$(az ad signed-in-user show --query objectId -o tsv)
CONTAINER_NAME=mystoragecontainer
az role assignment create --role "Storage Blob Data Contributor" --assignee $AD_USER --scope $STORAGE_ACCOUNT_ID
az storage container create --name $CONTAINER_NAME --account-name jebutlaksstorage --auth-mode login
```

Proporcione permisos a la cuenta de servicio de Azure HPC Cache para acceder a la cuenta de almacenamiento y al contenedor de blobs.

```azurecli
HPC_CACHE_USER="StorageCache Resource Provider"
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
$HPC_CACHE_ID=$(az ad sp list --display-name "${HPC_CACHE_USER}" --query "[].objectId" -o tsv)
az role assignment create --role "Storage Account Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
az role assignment create --role "Storage Blob Data Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
```

### <a name="5-configure-the-storage-target"></a>5. Configuración del destino de almacenamiento

Agregue el contenedor de blobs a la instancia de HPC Cache como destino de almacenamiento.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
CONTAINER_NAME=mystoragecontainer
az hpc-cache blob-storage-target add \
  --resource-group $RESOURCE_GROUP \
  --cache-name MyHpcCache \
  --name MyStorageTarget \
  --storage-account $STORAGE_ACCOUNT_ID \
  --container-name $CONTAINER_NAME \
  --virtual-namespace-path "/myfilepath"
```

### <a name="6-set-up-client-load-balancing"></a>6. Configuración del equilibrio de carga de cliente

Cree una zona DNS privada de Azure para las direcciones IP orientadas al cliente.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
PRIVATE_DNS_ZONE="myhpccache.local"
az network private-dns zone create \
  -g $RESOURCE_GROUP \
  -n $PRIVATE_DNS_ZONE
az network private-dns link vnet create \
  -g $RESOURCE_GROUP \
  -n MyDNSLink \
  -z $PRIVATE_DNS_ZONE \
  -v $VNET_NAME \
  -e true
```

Cree el nombre DNS round robin.

```azurecli
DNS_NAME="server"
PRIVATE_DNS_ZONE="myhpccache.local"
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
HPC_MOUNTS0=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[0]" -o tsv | tr --delete '\r')
HPC_MOUNTS1=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[1]" -o tsv | tr --delete '\r')
HPC_MOUNTS2=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[2]" -o tsv | tr --delete '\r')
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS0
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS1
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS2
```

## <a name="create-the-aks-persistent-volume"></a>Creación del volumen persistente de AKS

Cree un archivo `pv-nfs.yaml` para definir un [volumen persistente][persistent-volume].

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 10000Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: server.myhpccache.local
    path: /
```

En primer lugar, asegúrese de que tiene credenciales para el clúster de Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Actualice el *servidor* y la *ruta de acceso* a los valores del volumen de NFS (Network File System) que creó en el paso anterior. Cree el volumen persistente con el comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f pv-nfs.yaml
```

Compruebe que el estado del volumen persistente esté **Disponible** mediante el comando [kubectl describe][kubectl-describe]:

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistent-volume-claim"></a>Creación de la notificación de volumen persistente

Cree un archivo `pvc-nfs.yaml` que defina una [notificación de volumen persistente][persistent-volume-claim]. Por ejemplo:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 100Gi
```

Use el comando [kubectl apply][kubectl-apply] para crear la notificación de volumen persistente:

```console
kubectl apply -f pvc-nfs.yaml
```

Compruebe que el estado de la notificación de volumen persistente está **Enlazado** mediante el comando [kubectl describe][kubectl-describe]:

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-the-hpc-cache-with-a-pod"></a>Montaje de la instancia de HPC Cache con un pod

Cree un archivo `nginx-nfs.yaml` para definir un pod que use la notificación de volumen persistente. Por ejemplo:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/myfilepath/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Cree el pod con el comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f nginx-nfs.yaml
```

Compruebe que el pod se está ejecutando mediante el comando [kubectl describe][kubectl-describe]:

```console
kubectl describe pod nginx-nfs
```

Compruebe que su volumen se ha montado en el pod mediante [kubectl exec][kubectl-exec] para conectarse a pod y, a continuación, `df -h` para comprobar si el volumen está montado.

```console
kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
server.myhpccache.local:/myfilepath 8.0E         0      8.0E   0% /mnt/azure/myfilepath
...
```

## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes

### <a name="running-applications-as-non-root"></a>Ejecución de aplicaciones como no raíz

Si necesita ejecutar una aplicación como un usuario no raíz, es posible que tenga que deshabilitar el squash raíz para convertir un directorio en otro usuario.  El usuario no raíz tendrá que ser propietario de un directorio para acceder al sistema de archivos.  Para que el usuario sea propietario de un directorio, el usuario raíz debe asignar un directorio a ese usuario, pero si la instancia de HPC Cache es de squash raíz, esta operación se denegará porque el usuario raíz (UID 0) se está asignando al usuario anónimo.  Puede encontrar más información sobre el squash raíz y las directivas de acceso de cliente [aquí][hpc-cache-access-policies].

### <a name="sending-feedback"></a>Envío de opiniones

Estaremos encantados de ayudarle.  Envíe sus comentarios o preguntas a <aks-hpccache-feed@microsoft.com>.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre Azure HPC Cache, consulte la [información general de HPC Cache][hpc-cache].
* Para obtener más información sobre el uso de NFS con AKS, consulte [Crear manualmente y usar un volumen de servidor NFS (sistema de archivos de red) de Linux con Azure Kubernetes Service (AKS)][aks-nfs].

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[hpc-cache]: ../hpc-cache/hpc-cache-overview.md
[hpc-cache-access-policies]: ../hpc-cache/access-policies.md
[hpc-cache-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache&regions=all
[hpc-cache-cli-prerequisites]: ../hpc-cache/az-cli-prerequisites.md
[hpc-cache-prereqs]: ../hpc-cache/hpc-cache-prerequisites.md
[az-hpc-cache-create]: /cli/azure/hpc-cache#az_hpc_cache_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[persistent-volume]: concepts-storage.md#persistent-volumes
[persistent-volume-claim]: concepts-storage.md#persistent-volume-claims
