---
title: Creación y administración de ubicaciones personalizadas en Kubernetes habilitado para Azure Arc
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: Uso de ubicaciones personalizadas para implementar servicios de PaaS de Azure en clústeres de Kubernetes habilitado para Azure Arc
ms.openlocfilehash: f241ec384fc9ed7ee96d7415074e009cea486811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257031"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Creación y administración de ubicaciones personalizadas en Kubernetes habilitado para Azure Arc

 Las *Ubicaciones personalizadas* proporcionan un modo para que los administradores de inquilinos o clústeres configuren sus clústeres de Kubernetes habilitado para Azure Arc como ubicaciones de destinos para la implementación de instancias de servicios de Azure; es decir,  recursos como SQL Managed Instance habilitado para Azure Arc e Hiperescala de PostgreSQL habilitada para Azure Arc. En clústeres de Kubernetes habilitados para Azure Arc, la ubicación personalizada representa una abstracción de un espacio de nombres dentro del clúster de Kubernetes habilitado para Azure Arc. Los administradores de inquilinos o clústeres pueden asignar permisos de control de acceso basado en rol (RBAC) a desarrolladores de aplicaciones o administradores de bases de datos a fin de implementar recursos como SQL Managed Instances habilitado para Azure Arc, instancias de Hiperescala de PostgreSQL habilitada para Azure Arc o aplicaciones web de Azure en la ubicación personalizada. 
 
Puede encontrar una información general conceptual sobre esta característica en el artículo [Ubicaciones personalizadas: Kubernetes habilitado para Azure Arc](conceptual-custom-locations.md). 

En este artículo aprenderá a:
> [!div class="checklist"]
> * Habilitar ubicaciones personalizadas en el clúster de Kubernetes habilitado para Azure Arc.
> * Crear una ubicación personalizada.


## <a name="prerequisites"></a>Requisitos previos

- [Instale o actualice la CLI de Azure](/cli/azure/install-azure-cli) a la versión 2.16.0 o posteriores.

- Instale las extensiones de la CLI de Azure siguientes:
    - `connectedk8s` (versión 1.1.0 o posterior)
    - `k8s-extension` (versión 0.2.0 o posterior)
    - `customlocation` (versión 0.1.0 o posterior) 
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Si ya instaló previamente las extensiones `connectedk8s`, `k8s-extension` y `customlocation`, actualícelas a la versión más reciente con el siguiente comando:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```
    >[!NOTE]
    >Se recomienda usar la versión más reciente de las extensiones de la CLI a fin de obtener las nuevas características.  

- Compruebe que se completó el registro de proveedor de `Microsoft.ExtendedLocation`.
    1. Escriba los siguientes comandos:
    
        ```azurecli
        az provider register --namespace Microsoft.ExtendedLocation
        ```

    2. Supervise el proceso de registro. El registro puede tardar un máximo de 10 minutos.
    
        ```azurecli
        az provider show -n Microsoft.ExtendedLocation -o table
        ```

        Una vez que se registre, el estado `RegistrationState` tendrá el valor `Registered`.

- Compruebe que tiene un [clúster conectado a Kubernetes habilitado para Azure Arc](quickstart-connect-cluster.md) existente.
    - [Actualice los agentes](agent-upgrade.md#manually-upgrade-agents) a la versión 1.1.0 o posterior.

## <a name="enable-custom-locations-on-cluster"></a>Habilitación de ubicaciones personalizadas en el clúster

Si ha iniciado sesión en la CLI de Azure como un usuario de Azure AD, para habilitar esta característica en el clúster, ejecute el siguiente comando:

```azurecli
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

Si ha iniciado sesión en la CLI de Azure con una entidad de servicio, para habilitar esta característica en el clúster, siga estos pasos:

1. Capture el identificador de objeto de la aplicación de Azure AD que utiliza el servicio Azure Arc:

    ```azurecli
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. Use el valor `<objectId>` del paso anterior para habilitar la característica de ubicaciones personalizadas en el clúster:

    ```azurecli
    az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
    ```

> [!NOTE]
> 1. La característica Ubicaciones personalizadas depende de la característica Conexión de clúster. Por lo tanto, es necesario habilitar ambas características para que funcionen las ubicaciones personalizadas.
> 2. `az connectedk8s enable-features` debe ejecutarse en una máquina en el que el archivo `kubeconfig` apunte al clúster en el que se van a habilitar las características.

## <a name="create-custom-location"></a>Creación de una ubicación personalizada

1. Implemente la extensión de clúster de servicio de Azure de la instancia de servicio de Azure que intenta instalar en el clúster:

    * [Data Services habilitado para Azure Arc](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > El proxy de salida sin autenticación y el proxy de salida con autenticación básica son compatibles con la extensión de clúster de Data Services habilitado para Azure Arc. El proxy de salida que espera certificados de confianza no está admitido actualmente.


    * [Azure App Service en Azure Arc](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Event Grid en Kubernetes](../../event-grid/kubernetes/install-k8s-extension.md)

2. Obtenga el identificador de Azure Resource Manager del clúster de Kubernetes habilitado para Azure Arc, al que se hace referencia en pasos posteriores como `connectedClusterId`:

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

3. Obtenga el identificador de Azure Resource Manager de la extensión de clúster implementada a partir del clúster de Kubernetes habilitado para Azure Arc, al que se hace referencia en pasos posteriores como `extensionId`:

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

4. Cree una ubicación personalizada mediante una referencia al clúster de Kubernetes habilitado para Azure Arc y la extensión:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
    ```

**Parámetros obligatorios**

| Nombre de parámetro | Descripción |
|----------------|------------|
| `--name, --n` | Nombre de la ubicación personalizada |
| `--resource-group, --g` | Grupo de recursos de la ubicación personalizada  | 
| `--namespace` | Espacio de nombres del clúster enlazado a la ubicación personalizada que se va a crear |
| `--host-resource-id` | Identificador de Azure Resource Manager del clúster de Kubernetes habilitado para Azure Arc (clúster conectado) |
| `--cluster-extension-ids` | Identificadores de Azure Resource Manager de las instancias de extensión de clúster instaladas en el clúster conectado. Proporcione una lista de los identificadores de extensión del clúster separados por espacios  |

**Parámetros opcionales**

| Nombre de parámetro | Descripción |
|--------------|------------|
| `--assign-identity` | El valor predeterminado es `None`. Crea una [identidad administrada asignada por el sistema](../../active-directory/managed-identities-azure-resources/overview.md) si el parámetro está establecido en "SystemAssigned" |
| `--location, --l` | Ubicación del recurso de Azure Resource Manager de la ubicación personalizada en Azure. De manera predeterminada, se establecerá en la ubicación (o región de Azure) del clúster conectado |
| `--tags` | Lista de etiquetas separadas por espacio: key[=value] [key[=value] ...]. Utilice '' para desactivar las etiquetas existentes |
| `--kubeconfig` | Kubeconfig de administración del clúster. Se debe pasar como archivo si el clúster no está habilitado para AAD |


## <a name="show-details-of-a-custom-location"></a>Visualización de los detalles de una ubicación personalizada

Visualización de los detalles de una ubicación personalizada

```azurecli
    az customlocation show -n <customLocationName> -g <resourceGroupName> 
```

**Parámetros obligatorios**

| Nombre de parámetro | Descripción |
|----------------|------------|
| `--name, --n` | Nombre de la ubicación personalizada |
| `--resource-group, --g` | Grupo de recursos de la ubicación personalizada  | 

## <a name="list-custom-locations"></a>Enumeración de ubicaciones personalizadas

Enumera todas las ubicaciones personalizadas de un grupo de recursos

```azurecli
    az customlocation show -g <resourceGroupName> 
```

**Parámetros obligatorios**

| Nombre de parámetro | Descripción |
|----------------|------------|
| `--resource-group, --g` | Grupo de recursos de la ubicación personalizada  | 


## <a name="update-a-custom-location"></a>Actualización de una ubicación personalizada

Utilice el comando `update` cuando quiera agregar etiquetas nuevas, asocie identificadores de extensión de clúster nuevos a la ubicación personalizada mientras conserva las etiquetas existentes y las extensiones de clúster asociadas. Se pueden actualizar `--cluster-extension-ids`, `--tags` y `assign-identity`. 

```azurecli
    az customlocation update -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```
**Parámetros obligatorios**

| Nombre de parámetro | Descripción |
|----------------|------------|
| `--name, --n` | Nombre de la ubicación personalizada |
| `--resource-group, --g` | Grupo de recursos de la ubicación personalizada  | 
| `--namespace` | Espacio de nombres del clúster enlazado a la ubicación personalizada que se va a crear |
| `--host-resource-id` | Identificador de Azure Resource Manager del clúster de Kubernetes habilitado para Azure Arc (clúster conectado) |

**Parámetros opcionales**

| Nombre de parámetro | Descripción |
|--------------|------------|
| `--assign-identity` | Se puede actualizar a `None` o `"SystemAssigned` si desea asignar una [identidad administrada asignada por el sistema](../../active-directory/managed-identities-azure-resources/overview.md) a la ubicación personalizada |
| `--cluster-extension-ids` | Asocie extensiones de clúster nuevas a esta ubicación personalizada. Para ello, proporcione identificadores de Azure Resource Manager de las instancias de extensión de clúster instaladas en el clúster conectado. Proporcione una lista de los identificadores de extensión del clúster separados por espacios |
| `--tags` | Agregue etiquetas nuevas además de las etiquetas existentes. Lista de etiquetas separadas por espacio: key[=value] [key[=value] ...]. |

## <a name="patch-a-custom-location"></a>Revisión de una ubicación personalizada

Use el comando `patch` cuando quiera reemplazar las etiquetas y los identificadores de extensión de clúster existentes por etiquetas e identificadores de extensión de clúster nuevos. Se puede aplicar una revisión a `--cluster-extension-ids`, `assign-identity` y `--tags`. 

```azurecli
    az customlocation patch -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```

**Parámetros obligatorios**

| Nombre de parámetro | Descripción |
|----------------|------------|
| `--name, --n` | Nombre de la ubicación personalizada |
| `--resource-group, --g` | Grupo de recursos de la ubicación personalizada  | 

**Parámetros opcionales**

| Nombre de parámetro | Descripción |
|--------------|------------|
| `--assign-identity` | Se puede actualizar a `None` o `"SystemAssigned` si desea asignar una [identidad administrada asignada por el sistema](../../active-directory/managed-identities-azure-resources/overview.md) a la ubicación personalizada |
| `--cluster-extension-ids` | Asocie extensiones de clúster nuevas a esta ubicación personalizada. Para ello, proporcione identificadores de Azure Resource Manager de las instancias de extensión de clúster instaladas en el clúster conectado. Proporcione una lista de los identificadores de extensión del clúster separados por espacios |
| `--tags` | Agregue etiquetas nuevas además de las etiquetas existentes. Lista de etiquetas separadas por espacio: key[=value] [key[=value] ...]. |

## <a name="delete-a-custom-location"></a>Eliminación de una ubicación personalizada

 ```azurecli
    az customlocation delete -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
   ```

## <a name="next-steps"></a>Pasos siguientes

- Conéctese de forma segura al clúster mediante [Conexión de clúster](cluster-connect.md).
- Continúe con [Azure App Service en Azure Arc](../../app-service/overview-arc-integration.md) para obtener instrucciones de un extremo a otro sobre cómo instalar extensiones, crear ubicaciones personalizadas y crear el entorno de Kubernetes de App Service. 
- Cree un tema de Event Grid y una suscripción a evento para [Event Grid en Kubernetes](../../event-grid/kubernetes/overview.md).
- Obtenga más información sobre las [extensiones de Kubernetes habilitado para Azure Arc](extensions.md#currently-available-extensions) disponibles actualmente.
