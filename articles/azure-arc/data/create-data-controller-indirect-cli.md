---
title: Creación de un controlador de datos mediante la CLI
description: Cree un controlador de datos de Azure Arc en un clúster típico de Kubernetes de varios nodos que ya haya creado mediante la CLI.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: c2d67cd8e62a0b74ed5959cebfe691ffb044d975
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737169"
---
# <a name="create-azure-arc-data-controller-using-the-cli"></a>Creación de un controlador de datos de Azure Arc mediante la CLI


## <a name="prerequisites"></a>Requisitos previos

Revise el tema [Creación del controlador de datos de Azure Arc](create-data-controller.md) para obtener información general.

### <a name="install-tools"></a>Instalación de herramientas

Para crear el controlador de datos mediante la CLI, deberá instalar la extensión `arcdata` para la CLI de Azure (az). 

[Instale [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md).

Independientemente de la plataforma de destino que elija, tendrá que establecer las siguientes variables de entorno antes de la creación para el usuario administrador del controlador de datos. Puede proporcionar estas credenciales a otras personas que necesiten tener acceso de administrador al controlador de datos según sea necesario.

### <a name="set-environment-variables"></a>Establecimiento de variables de entorno

**AZDATA_USERNAME**: nombre de usuario de su elección para el usuario administrador de Kibana/Grafana. Ejemplo: `arcadmin`

**AZDATA_PASSWORD**: contraseña de su elección para el usuario administrador de Kibana/Grafana. La contraseña debe tener al menos 8 caracteres y contener caracteres de tres de los siguientes cuatro conjuntos: mayúsculas, minúsculas, números y símbolos.

#### <a name="linux-or-macos"></a>Linux o macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

#### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Tendrá que conectarse a un clúster de Kubernetes y autenticarse en él, además de tener un contexto de Kubernetes existente seleccionado antes de comenzar la creación del controlador de datos de Azure Arc. La forma en que se conecta a un clúster o servicio de Kubernetes varía; consulte la documentación de la distribución o el servicio de Kubernetes que usa para saber cómo conectarse al servidor de API de Kubernetes.

Puede comprobar que tiene una conexión de Kubernetes y confirmar el contexto actual con los siguientes comandos.

```console
kubectl cluster-info
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Creación del controlador de datos de Azure Arc

> [!NOTE]
> Puede usar un valor diferente para el parámetro `--namespace` del comando `az arcdata dc create` en los ejemplos siguientes, pero asegúrese de usar ese nombre de espacio de nombres en el parámetro `--namespace` de los demás comandos siguientes.

- [Creación de un controlador de datos de Azure Arc mediante la CLI](#create-azure-arc-data-controller-using-the-cli)
  - [Requisitos previos](#prerequisites)
    - [Linux o macOS](#linux-or-macos)
    - [Windows PowerShell](#windows-powershell)
  - [Creación del controlador de datos de Azure Arc](#create-the-azure-arc-data-controller)
    - [Creación en Azure Kubernetes Service (AKS)](#create-on-azure-kubernetes-service-aks)
    - [Creación en AKS en Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
    - [Creación en Red Hat OpenShift en Azure (ARO)](#create-on-azure-red-hat-openshift-aro)
      - [Creación de un perfil de implementación personalizado](#create-custom-deployment-profile)
      - [Creación de un controlador de datos](#create-data-controller)
    - [Creación en Red Hat OpenShift Container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
      - [Determinación de la clase de almacenamiento](#determine-storage-class)
      - [Creación de un perfil de implementación personalizado](#create-custom-deployment-profile-1)
      - [Establecimiento de la clase de almacenamiento](#set-storage-class)
      - [Establecimiento de LoadBalancer (opcional)](#set-loadbalancer-optional)
      - [Creación de un controlador de datos](#create-data-controller-1)
    - [Creación en Kubernetes ascendente de código abierto (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
    - [Creación en Elastic Kubernetes Service (EKS) de AWS](#create-on-aws-elastic-kubernetes-service-eks)
    - [Creación en Kubernetes Engine Service (GKE) de Google Cloud](#create-on-google-cloud-kubernetes-engine-service-gke)
  - [Supervisión del estado de creación](#monitoring-the-creation-status)
  - [Solución de problemas de creación](#troubleshooting-creation-problems)

### <a name="create-on-azure-kubernetes-service-aks"></a>Creación en Azure Kubernetes Service (AKS)

De forma predeterminada, el perfil de implementación de AKS usa la clase de almacenamiento `managed-premium`. La clase de almacenamiento `managed-premium` solo funcionará si tiene máquinas virtuales que se implementaron mediante imágenes de máquina virtual con discos premium.

Si va a usar `managed-premium` como clase de almacenamiento, puede ejecutar el siguiente comando para implementar el controlador de datos. Sustituya los marcadores de posición del comando por el nombre del grupo de recursos, el identificador de la suscripción y la ubicación de Azure.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace <namespace> --name arc --azure-subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --use-k8s

#Example:
#az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace arc --name arc --azure-subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --use-k8s
```

Si no está seguro de qué clase de almacenamiento debe utilizar, indique la clase de almacenamiento `default`, que es compatible con cualquier tipo de máquina virtual que esté usando. No le ofrecerá el rendimiento más rápido.

Si desea usar la clase de almacenamiento `default`, puede ejecutar este comando:

```azurecli
az arcdata dc create --profile-name azure-arc-aks-default-storage --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-default-storage  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Una vez ejecutado el comando, continúe en [Supervisión del estado de creación](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Creación en AKS en Azure Stack HCI

#### <a name="configure-storage-azure-stack-hci-with-aks-hci"></a>Configuración del almacenamiento (Azure Stack HCI con AKS-HCI)

Si usa Azure Stack HCI con AKS-HCI, realice una de las siguientes acciones, en función de la versión de Azure Stack HCI con AKS-HCI:

- Para la versión 1.20 y posteriores, cree una clase de almacenamiento personalizada con `fsGroupPolicy:File` (para más información: https://kubernetes-csi.github.io/docs/support-fsgroup.html) ). 
- Para la versión 1.19, use: 

   ```json
   fsType: ext4
   ```

Use este tipo para implementar el controlador de datos. Vea todas las instrucciones en [Creación de una clase de almacenamiento personalizada para un disco de AKS en Azure Stack HCI](/azure-stack/aks-hci/container-storage-interface-disks#create-a-custom-storage-class-for-an-aks-on-azure-stack-hci-disk).

De forma predeterminada, el perfil de implementación utiliza una clase de almacenamiento denominada `default` y el tipo de servicio `LoadBalancer`.

Puede ejecutar el siguiente comando para implementar el controlador de datos con la clase de almacenamiento `default` y el tipo de servicio `LoadBalancer`.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Una vez ejecutado el comando, continúe en [Supervisión del estado de creación](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Creación en Red Hat OpenShift en Azure (ARO)

#### <a name="create-custom-deployment-profile"></a>Creación de un perfil de implementación personalizado

Use el perfil `azure-arc-azure-openshift` para Azure RedHat Open Shift.

```azurecli
az arcdata dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Creación de un controlador de datos

Puede ejecutar el comando siguiente para crear el controlador de datos:

```azurecli
az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Una vez ejecutado el comando, continúe en [Supervisión del estado de creación](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Creación en Red Hat OpenShift Container Platform (OCP)

#### <a name="determine-storage-class"></a>Determinación de la clase de almacenamiento

También necesitará determinar qué clase de almacenamiento se va a usar ejecutando el siguiente comando.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Creación de un perfil de implementación personalizado

Cree un nuevo archivo de perfil de implementación personalizado basado en el perfil de implementación `azure-arc-openshift` ejecutando el siguiente comando. Este comando crea un directorio `custom` en el directorio de trabajo actual y un archivo de perfil de implementación personalizado `control.json` en ese directorio.

Use el perfil `azure-arc-openshift` para OpenShift Container Platform.

```azurecli
az arcdata dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Establecimiento de la clase de almacenamiento 

Ahora, establezca la clase de almacenamiento deseada reemplazando `<storageclassname>` en el siguiente comando por el nombre de la clase de almacenamiento que desea usar y que se determinó mediante la ejecución del comando `kubectl get storageclass` anterior.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Establecimiento de LoadBalancer (opcional)

De forma predeterminada, el perfil de implementación `azure-arc-openshift` usa `NodePort` como el tipo de servicio. Si usa un clúster de OpenShift que está integrado con un equilibrador de carga, puede cambiar la configuración para usar el tipo de servicio `LoadBalancer` con el siguiente comando:

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="create-data-controller"></a>Creación de un controlador de datos

Ahora ya puede crear el controlador de datos con el siguiente comando.

> [!NOTE]
>   El parámetro `--path` debe apuntar al _directorio_ que contiene el archivo control.json y no al propio archivo control.json.

> [!NOTE]
>   Al implementar en OpenShift Container Platform, deberá especificar el valor del parámetro `--infrastructure`.  Las opciones son: `aws`, `azure`, `alibaba`, `gcp` y `onpremises`.

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

Una vez ejecutado el comando, continúe en [Supervisión del estado de creación](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Creación en Kubernetes ascendente de código abierto (kubeadm)

De forma predeterminada, el perfil de implementación de kubeadm usa una clase de almacenamiento denominada `local-storage` y el tipo de servicio `NodePort`. Si le resulta aceptable, puede omitir las instrucciones que se indican a continuación para establecer la clase de almacenamiento y el tipo de servicio que desee y ejecutar inmediatamente el comando `az arcdata dc create` siguiente.

Si desea personalizar el perfil de implementación para especificar una clase de almacenamiento o un tipo de servicio específicos, empiece por crear un nuevo archivo de perfil de implementación personalizado basado en el perfil de implementación de kubeadm. Para ello, ejecute el siguiente comando. Este comando creará un directorio `custom` en el directorio de trabajo actual y un archivo de perfil de implementación personalizado `control.json` en ese directorio.

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path ./custom --k8s-namespace <namespace> --use-k8s
```

Puede buscar las clases de almacenamiento disponibles mediante la ejecución del siguiente comando.

```console
kubectl get storageclass
```

Ahora, establezca la clase de almacenamiento deseada reemplazando `<storageclassname>` en el siguiente comando por el nombre de la clase de almacenamiento que desea usar y que se determinó mediante la ejecución del comando `kubectl get storageclass` anterior.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

De forma predeterminada, el perfil de implementación de kubeadm usa `NodePort` como tipo de servicio. Si usa un clúster de Kubernetes que está integrado con un equilibrador de carga, puede cambiar la configuración con el siguiente comando.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer" --k8s-namespace <namespace> --use-k8s
```

Ahora ya puede crear el controlador de datos con el siguiente comando.

> [!NOTE]
>   Al implementar en OpenShift Container Platform, deberá especificar el valor del parámetro `--infrastructure`.  Las opciones son: `aws`, `azure`, `alibaba`, `gcp` y `onpremises`.

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom - --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

Una vez ejecutado el comando, continúe en [Supervisión del estado de creación](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Creación en Elastic Kubernetes Service (EKS) de AWS

De forma predeterminada, la clase de almacenamiento de EKS es `gp2` y el tipo de servicio es `LoadBalancer`.

Ejecute el siguiente comando para crear el controlador de datos mediante el perfil de implementación de EKS proporcionado.

```azurecli
az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Una vez ejecutado el comando, continúe en [Supervisión del estado de creación](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Creación en Kubernetes Engine Service (GKE) de Google Cloud

De forma predeterminada, la clase de almacenamiento de GKE es `standard` y el tipo de servicio es `LoadBalancer`.

Ejecute el siguiente comando para crear el controlador de datos mediante el perfil de implementación de GKE proporcionado.

```azurecli
az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Una vez ejecutado el comando, continúe en [Supervisión del estado de creación](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Supervisión del estado de creación

La creación del controlador tardará unos minutos en completarse. Puede supervisar el progreso en otra ventana de terminal con los siguientes comandos:

> [!NOTE]
>  En los siguientes comandos de ejemplo se da por hecho que ha creado un controlador de datos y un espacio de nombres de Kubernetes con el nombre `arc`. Si ha usado otro nombre de controlador de datos o de espacio de nombres, puede reemplazar `arc` por ese nombre.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

También puede comprobar el estado de creación de un pod determinado ejecutando un comando como el siguiente. Esto es especialmente útil para solucionar problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Solución de problemas de creación

Si tiene problemas con la creación, consulte la [guía de solución de problemas](troubleshoot-guide.md).
