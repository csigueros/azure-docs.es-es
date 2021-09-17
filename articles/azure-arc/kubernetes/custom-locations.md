---
title: Creación y administración de ubicaciones personalizadas en Kubernetes habilitado para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: Uso de ubicaciones personalizadas para implementar servicios de PaaS de Azure en clústeres de Kubernetes habilitado para Azure Arc
ms.openlocfilehash: a4586f6f527bd98f0f347e51c787f2bcda7c6d8d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768306"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Creación y administración de ubicaciones personalizadas en Kubernetes habilitado para Azure Arc

Como extensión de ubicación de Azure, *Ubicaciones personalizadas* proporciona un modo para que los administradores de inquilinos usen sus clústeres de Kubernetes habilitado para Azure Arc como ubicaciones de destino para implementar instancias de servicios de Azure. Entre los ejemplos de recursos de Azure se incluyen SQL Managed Instance habilitado para Azure Arc e Hiperescala de PostgreSQL habilitada para Azure Arc.

De forma similar a las ubicaciones de Azure, los usuarios finales dentro del inquilino con acceso a Ubicaciones personalizadas pueden implementar recursos allí mediante el proceso privado de su empresa.

En este artículo aprenderá a:
> [!div class="checklist"]
> * Habilitar ubicaciones personalizadas en el clúster de Kubernetes habilitado para Azure Arc.
> * Implemente la extensión de clúster de servicio de Azure de la instancia de servicio de Azure en el clúster.
> * Cree una ubicación personalizada en el clúster de Kubernetes habilitado para Azure Arc.

Puede encontrar una información general conceptual sobre esta característica en el artículo sobre [ubicaciones de clúster: Kubernetes habilitado para Azure Arc](conceptual-custom-locations.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

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

1. Implemente la extensión de clúster de servicio de Azure de la instancia de servicio de Azure que finalmente quiera en el clúster:

    * [Data Services habilitado para Azure Arc](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > El proxy de salida sin autenticación y el proxy de salida con autenticación básica son compatibles con la extensión de clúster de Data Services habilitada para Arc. El proxy de salida que espera certificados de confianza no está admitido actualmente.


    * [Azure App Service en Azure Arc](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Event Grid en Kubernetes](../../event-grid/kubernetes/install-k8s-extension.md)

1. Obtenga el identificador de Azure Resource Manager del clúster de Kubernetes habilitado para Azure Arc, al que se hace referencia en pasos posteriores como `connectedClusterId`:

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Obtenga el identificador de Azure Resource Manager de la extensión de clúster implementada a partir del clúster de Kubernetes habilitado para Azure Arc, al que se hace referencia en pasos posteriores como `extensionId`:

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Cree una ubicación personalizada haciendo referencia al clúster de Kubernetes habilitado para Azure Arc y la extensión:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Pasos siguientes

- Conéctese de forma segura al clúster mediante [Conexión de clúster](cluster-connect.md).
- Continúe con [Azure App Service en Azure Arc](../../app-service/overview-arc-integration.md) para obtener instrucciones de un extremo a otro sobre cómo instalar extensiones, crear ubicaciones personalizadas y crear el entorno de Kubernetes de App Service. 
- Cree un tema de Event Grid y una suscripción a evento para [Event Grid en Kubernetes](../../event-grid/kubernetes/overview.md).
- Obtenga más información sobre las [extensiones de Kubernetes habilitadas para Azure Arc](extensions.md#currently-available-extensions) disponibles actualmente.