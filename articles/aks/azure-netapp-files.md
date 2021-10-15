---
title: Integración de Azure NetApp Files con Azure Kubernetes Service | Microsoft Docs
description: Obtenga información sobre cómo aprovisionar Azure NetApp Files con Azure Kubernetes Service.
services: container-service
ms.topic: article
ms.date: 10/04/2021
ms.openlocfilehash: 177526fa98ada37341fadc90e183f224e1aa0830
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544799"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integración de Azure NetApp Files con Azure Kubernetes Service

Un volumen persistente representa un fragmento de almacenamiento aprovisionado para su uso con pods de Kubernetes. Un volumen persistente puede usarse en uno o varios pods y puede aprovisionarse de forma dinámica o estática. En este artículo se muestra cómo crear volúmenes de [Azure NetApp Files][anf] para que los usen los pods en un clúster de Azure Kubernetes Service (AKS).

[Azure NetApp Files][anf] es un servicio de almacenamiento de archivos de alto rendimiento, medido y de clase empresarial que se ejecuta en Azure. Los usuarios de Kubernetes tienen dos opciones cuando se trata de usar volúmenes de Azure NetApp Files para cargas de trabajo de Kubernetes:

* Crear volúmenes de Azure NetApp Files de forma **estática**: en este escenario, la creación de volúmenes se logra de forma externa a AKS; los volúmenes se crean mediante la UI `az`/Azure y, a continuación, se exponen en Kubernetes mediante la creación de un objeto `PersistentVolume`.
* Crear Azure NetApp Files volúmenes **a petición**, orquestando mediante Kubernetes: este método es el modo de operación **preferido** para crear varios volúmenes directamente a través de Kubernetes, y se logra mediante[Astra Trident](https://netapp-trident.readthedocs.io/).

Se **recomienda encarecidamente** un controlador CSI listo para producción para que los usuarios de Kubernetes utilicen directamente volúmenes de Azure NetApp Files mediante AKS. Este requisito se cumple con Astra Trident, un orquestador de almacenamiento dinámico de código abierto para Kubernetes. Se trata de un orquestador de almacenamiento de nivel empresarial diseñado para Kubernetes, totalmente compatible con NetApp. Simplifica el acceso al almacenamiento en todos los entornos de Kubernetes mediante la automatización del aprovisionamiento de almacenamiento. Los consumidores pueden utilizar el controlador CSI de Astra Trident para que Azure NetApp Files abstraiga los detalles subyacentes y los volúmenes de creación, expansión y realización de instantáneas a petición.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, consulte el inicio rápido de AKS [mediante la CLI de Azure][aks-quickstart-cli] o [mediante Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Su clúster de AKS también debe estar [en una región que admita Azure NetApp Files][anf-regions].

También es preciso que esté instalada y configurada la versión 2.0.59 de la CLI de Azure u otra versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][install-azure-cli].

### <a name="prerequisites"></a>Requisitos previos

Las siguientes consideraciones se aplican al usar Azure NetApp Files:

* Azure NetApp Files solo está disponible [en regiones de Azure seleccionadas][anf-regions].
* Después de la implementación inicial de un clúster de AKS, puede elegir aprovisionar volúmenes de Azure NetApp Files de forma estática o dinámica.
* Para usar el aprovisionamiento dinámico con Azure NetApp Files, instale y configure [Astra Trident](https://netapp-trident.readthedocs.io/), versión 19.07 o posterior.

## <a name="configure-azure-netapp-files"></a>Configuración de Azure NetApp Files

Registre el proveedor de recursos *Microsoft.NetApp*:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Esta operación puede tardar un tiempo en finalizar.

Cuando crea una cuenta de Azure NetApp para usarla con AKS, debe crearla en el grupo de recursos del **nodo**. En primer lugar, obtenga el nombre del grupo de recursos con el comando [az aks show][az-aks-show] y agregue el parámetro de consulta `--query nodeResourceGroup`. En este ejemplo se obtiene el grupo de recursos del nodo para el clúster de AKS denominado *myAKSCluster* en el nombre del grupo de recursos *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Cree una cuenta de Azure NetApp Files en el grupo de recursos del **nodo** y la misma región que su clúster de AKS mediante [az netappfiles account create][az-netappfiles-account-create]. En el siguiente ejemplo se crea una cuenta denominada *myaccount1* en el grupo de recursos *MC_myResourceGroup_myAKSCluster_eastus* y la región *eastus*:

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Cree un nuevo grupo de capacidad mediante el comando [az netappfiles pool create][az-netappfiles-pool-create]. En el siguiente ejemplo se crea un nuevo grupo de capacidad denominado *mypool1* con 4 TB de tamaño y nivel de servicio *Premium*:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Cree una subred para [delegarla a Azure NetApp Files][anf-delegate-subnet] mediante [az network vnet subnet create][az-network-vnet-subnet-create]. *Esta subred debe estar en la misma red virtual que el clúster de AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Los volúmenes se pueden aprovisionar de forma estática o dinámica. Ambas opciones se tratan con detalle a continuación.

## <a name="provision-azure-netapp-files-volumes-statically"></a>Aprovisionamiento estático de volúmenes de Azure NetApp Files

Cree un volumen mediante [az netappfiles volume create][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

### <a name="create-the-persistentvolume"></a>Creación del volumen PersistentVolume

Enumere los detalles de su volumen mediante [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Cree un archivo `pv-nfs.yaml` que defina un volumen PersistentVolume. Reemplace `path` por *creationToken* y `server` por *ipAddress* del comando anterior. Por ejemplo:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Actualice el *servidor* y la *ruta de acceso* a los valores del volumen de NFS (Network File System) que creó en el paso anterior. Cree el volumen PersistentVolume con el comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f pv-nfs.yaml
```

Compruebe que el *Estado* del volumen PersistentVolume es *Disponible* mediante el comando [kubectl describe][kubectl-describe]:

```console
kubectl describe pv pv-nfs
```

### <a name="create-the-persistentvolumeclaim"></a>Creación de la notificación PersistentVolumeClaim

Cree un archivo `pvc-nfs.yaml` que defina un volumen PersistentVolume. Por ejemplo:

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
      storage: 1Gi
```

Cree la notificación PersistentVolumeClaim con el comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f pvc-nfs.yaml
```

Compruebe que el *Estado* de la notificación PersistentVolumeClaim es *Enlazado* mediante el comando [kubectl describe][kubectl-describe]:

```console
kubectl describe pvc pvc-nfs
```

### <a name="mount-with-a-pod"></a>Montaje con un pod

Cree un archivo `nginx-nfs.yaml` que defina un pod que use la notificación PersistentVolumeClaim. Por ejemplo:

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
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
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

Compruebe que el pod se está *ejecutando* mediante el comando [kubectl describe][kubectl-describe]:

```console
kubectl describe pod nginx-nfs
```

Compruebe que su volumen se ha montado en el pod mediante [kubectl exec][kubectl-exec] para conectarse a pod y, a continuación, `df -h` para comprobar si el volumen está montado.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="provision-azure-netapp-files-volumes-dynamically"></a>Aprovisionamiento dinámico de volúmenes de Azure NetApp Files

### <a name="install-and-configure-astra-trident"></a>Instalación y configuración de Astra Trident

Para aprovisionar volúmenes de forma dinámica, debe instalar Astra Trident. Astra Trident es el aprovisionador de almacenamiento dinámico de NetApp que está diseñado para Kubernetes. Simplifique el consumo de almacenamiento para aplicaciones de Kubernetes mediante los controladores de [Container Storage Interface (CSI)](https://kubernetes-csi.github.io/docs/) estándar del sector de Astra Trident. Astra Trident se implementa en clústeres de Kubernetes como pods y proporciona servicios de orquestación de almacenamiento dinámico para las cargas de trabajo de Kubernetes.

Puede obtener más información en la [documentación](https://netapp-trident.readthedocs.io/en/latest/index.html).

Antes de continuar con el paso siguiente, deberá hacer lo siguiente:

1. **Instale Astra Trident**. Trident se puede instalar mediante operator/Helm chart/`tridentctl`. Las instrucciones que se proporcionan a continuación explican cómo se puede instalar Astra Trident mediante el operador. Para obtener información sobre cómo funcionan los otros métodos de instalación, consulte la [Guía de instalación](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html).

2. **Cree un back-end**. Para indicar a Astra Trident la suscripción de Azure NetApp Files y dónde debe crear los volúmenes, se crea un back-end. Este paso requiere detalles sobre la cuenta que se creó en el paso anterior.

#### <a name="install-astra-trident-using-the-operator"></a>Instalación de Astra Trident mediante el operador

Esta sección le guiará por la instalación de Astra Trident mediante el operador. También puede optar por realizar la instalación mediante alguno de sus otros métodos:

* [Gráfico de Helm](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/operator-deploy.html#deploy-trident-operator-by-using-helm)
* [tridentctl](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/tridentctl-deploy.html)

Consulte [Implementación de Trident](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html) para saber cómo funciona cada opción e identificar la que mejor se adapte a sus necesidades.

Descargue Astra Trident del [repositorio de GitHub](https://github.com/NetApp/trident/releases). Elija la versión deseada y descargue el paquete del instalador.

```console
#Download Astra Trident

$  wget https://github.com/NetApp/trident/releases/download/v21.07.1/trident-installer-21.07.1.tar.gz
$  tar xzvf trident-installer-21.07.1.tar.gz
```
Implemente el operador mediante `deploy/bundle.yaml`.

```console
$  kubectl create ns trident

namespace/trident created

$  kubectl apply -f trident-installer/deploy/bundle.yaml -n trident

serviceaccount/trident-operator created
clusterrole.rbac.authorization.k8s.io/trident-operator created
clusterrolebinding.rbac.authorization.k8s.io/trident-operator created
deployment.apps/trident-operator created
podsecuritypolicy.policy/tridentoperatorpods created
```

Cree un objeto `TridentOrchestrator` para instalar Astra Trident.

```console
$ kubectl apply -f trident-installer/deploy/crds/tridentorchestrator_cr.yaml

tridentorchestrator.trident.netapp.io/trident created 
```

El operador se instala mediante los parámetros proporcionados en la especificación `TridentOrchestrator`. Puede obtener información sobre los parámetros de configuración y back-end de ejemplo en las extensas [guías de instalación](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html) y de [back-end](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/backends/index.html).

Confirme que se ha instalado Astra Trident. 

```console
$  kubectl describe torc trident
Name:         trident
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  trident.netapp.io/v1
Kind:         TridentOrchestrator
...
Spec:
  Debug:      true
  Namespace:  trident
Status:
  Current Installation Params:
    IPv6:                       false
    Autosupport Hostname:
    Autosupport Image:          netapp/trident-autosupport:21.01
    Autosupport Proxy:
    Autosupport Serial Number:
    Debug:                      true
    Enable Node Prep:           false
    Image Pull Secrets:
    Image Registry:
    k8sTimeout:           30
    Kubelet Dir:          /var/lib/kubelet
    Log Format:           text
    Silence Autosupport:  false
    Trident Image:        netapp/trident:21.07.1
  Message:                Trident installed
  Namespace:              trident
  Status:                 Installed
  Version:                v21.07.1
Events:
  Type    Reason      Age   From                        Message
  ----    ------      ----  ----                        -------
  Normal  Installing  74s   trident-operator.netapp.io  Installing Trident
  Normal  Installed   67s   trident-operator.netapp.io  Trident installed
```

### <a name="create-a-backend"></a>Crear un back-end

Una vez instalado Astra Trident, cree un back-end que apunte a la suscripción de Azure NetApp Files.

```console
$  kubectl apply -f trident-installer/sample-input/backends-samples/azure-netapp-files/backend-anf.yaml -n trident

secret/backend-tbc-anf-secret created
tridentbackendconfig.trident.netapp.io/backend-tbc-anf created
```

Antes de ejecutar el comando, deberá actualizar `backend-anf.yaml` para incluir detalles sobre la suscripción de Azure NetApp Files, como:

* `subscriptionID` para la suscripción de Azure con Azure NetApp Files habilitado. The 
* `tenantID`, `clientID` y `clientSecret` desde un [Registro de aplicación](../active-directory/develop/howto-create-service-principal-portal.md) en Azure Active Directory (AD) con permisos suficientes para el servicio Azure NetApp Files. El registro de aplicación debe tener el rol `Owner` o `Contributor` predefinido por Azure.
* Ubicación de Azure que contiene al menos una subred delegada.

Además, puede optar por proporcionar un nivel de servicio diferente. Azure NetApp Files ofrece tres [niveles de servicio](../azure-netapp-files/azure-netapp-files-service-levels.md): Estándar, Premium y Ultra.

### <a name="create-a-storageclass"></a>Creación de una clase StorageClass

Una clase de almacenamiento se usa para definir cómo se crea dinámicamente una unidad de almacenamiento con un volumen persistente. Para utilizar volúmenes de Azure NetApp Files, se debe crear una clase de almacenamiento. Cree un archivo denominado `anf-storageclass.yaml` y cópielo en el manifiesto proporcionado a continuación.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-netapp-files
provisioner: csi.trident.netapp.io
parameters:
  backendType: "azure-netapp-files"
  fsType: "nfs"
```

Cree la clase de almacenamiento con el comando [kubectl apply][kubectl-apply]:

```console
$  kubectl apply -f anf-storageclass.yaml

storageclass/azure-netapp-files created

$  kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
azure-netapp-files   csi.trident.netapp.io   Delete          Immediate           false                  3s
```

### <a name="create-a-persistentvolumeclaim"></a>Creación de una notificación PersistentVolumeClaim

PersistentVolumeClaim (PVC) es una solicitud de almacenamiento por parte de un usuario. Tras la creación de una clase PersistentVolumeClaim, Astra Trident crea automáticamente un volumen de Azure NetApp Files y lo pone a disposición de las cargas de trabajo de Kubernetes para su consumo.

Cree un archivo denominado `anf-pvc.yaml` y proporcione el siguiente manifiesto. En este ejemplo, se crea un volumen de 1 TiB que es *ReadWriteMany*.

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: anf-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Ti
  storageClassName: azure-netapp-files
```

Cree la notificación del volumen persistente con el comando [kubectl apply][kubectl-apply]:

```console
$  kubectl apply -f anf-pvc.yaml

persistentvolumeclaim/anf-pvc created

$  kubectl get pvc
kubectl get pvc -n trident
NAME      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS         AGE
anf-pvc   Bound    pvc-bffa315d-3f44-4770-86eb-c922f567a075   1Ti        RWO            azure-netapp-files   62s
```

### <a name="use-the-persistent-volume"></a>Uso del volumen persistente

Una vez creada la PVC, se puede activar un pod para acceder al volumen de Azure NetApp Files. El manifiesto siguiente se puede usar para definir un pod NGINX que monte el volumen de Azure NetApp Files que se creó en el paso anterior. En este ejemplo, el volumen se monta en `/mnt/data`.

Cree un archivo llamado `anf-nginx-pod.yaml` que contiene el manifiesto siguiente:

```yml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: mcr.microsoft.com/oss/nginx/nginx:latest1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/data"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: anf-pvc
```

Cree el pod con el comando [kubectl apply][kubectl-apply]:

```console
$  kubectl apply -f anf-nginx-pod.yaml

pod/nginx-pod created
```

Kubernetes ha creado ahora un pod con el volumen montado y accesible dentro del contenedor `nginx` en `/mnt/data`. Confirme comprobando los registros de eventos del pod mediante `kubectl describe`:

```console
$  kubectl describe pod nginx-pod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  anf-pvc
    ReadOnly:   false
  default-token-k7952:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-k7952
    Optional:    false
[...]
Events:
  Type    Reason                  Age   From                     Message
  ----    ------                  ----  ----                     -------
  Normal  Scheduled               15s   default-scheduler        Successfully assigned trident/nginx-pod to brameshb-non-root-test
  Normal  SuccessfulAttachVolume  15s   attachdetach-controller  AttachVolume.Attach succeeded for volume "pvc-bffa315d-3f44-4770-86eb-c922f567a075"
  Normal  Pulled                  12s   kubelet                  Container image "mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine" already present on machine
  Normal  Created                 11s   kubelet                  Created container nginx
  Normal  Started                 10s   kubelet                  Started container nginx
```

Astra Trident admite muchas características con Azure NetApp Files, como:

* [Expansión de volúmenes](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/vol-expansion.html)
* [Instantáneas de volúmenes a petición](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/snapshots.html)
* [Importación de volúmenes](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/import.html)

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre Azure NetApp Files, consulte [¿Qué es Azure NetApp Files?][anf]

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netapp-files-dynamic]: azure-netapp-files-dynamic.md
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
