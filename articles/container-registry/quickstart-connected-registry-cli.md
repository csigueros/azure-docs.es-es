---
title: 'Inicio rápido: Creación de un registro conectado mediante la CLI'
description: Use los comandos de la CLI de Azure para crear un recurso de Azure Container Registry conectado que pueda sincronizar imágenes y otros artefactos con el registro en la nube.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 926d9ca087f509a1aa76dcec235fd7996bce4a78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093754"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-cli"></a>Guía de inicio rápido: Creación de un registro conectado mediante la CLI de Azure

En este inicio rápido, usará la CLI de Azure para crear un recurso de [registro conectado](intro-connected-registry.md) en Azure. La característica de registro conectado de Azure Container Registry le permite implementar un registro de forma remota o local y sincronizar imágenes y otros artefactos con el registro en la nube. 

Aquí se crean dos recursos de registro conectados para un registro en la nube: un registro conectado permite la funcionalidad de lectura y escritura (extracción e inserción de artefactos) y otro permite la funcionalidad de solo lectura. 

Después de crear un registro conectado, puede seguir otras guías para implementarlo y usarlo en la infraestructura local o remota.

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

* Azure Container Registry: si aún no tiene un registro de contenedor, [cree uno](container-registry-get-started-azure-cli.md) (se requiere el nivel Premium) en una [región](intro-connected-registry.md#available-regions) que admita registros conectados. 

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>Habilitación del punto de conexión de datos dedicado para el registro en la nube

Habilite el punto de conexión de datos dedicado para Azure Container Registry en la nube mediante el comando [az acr update][az-acr-update]. Este paso es necesario para que un registro conectado se comunique con el registro en la nube.

```azurecli
# Set the REGISTRY_NAME environment variable to identify the existing cloud registry
REGISTRY_NAME=<container-registry-name>

az acr update --name $REGISTRY_NAME \
  --data-endpoint-enabled
```

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>Creación de un recurso de registro conectado para la funcionalidad de lectura y escritura

Cree un registro conectado mediante el comando [az acr connected-registry create][az-acr-connected-registry-create]. El nombre del registro conectado debe empezar con una letra y contener solo caracteres alfanuméricos. Debe tener entre 5 y 40 caracteres y ser único en la jerarquía de este registro de contenedor de Azure.

```azurecli
# Set the CONNECTED_REGISTRY_RW environment variable to provide a name for the connected registry with read/write functionality
CONNECTED_REGISTRY_RW=<connnected-registry-name>

az acr connected-registry create --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy"
```

Este comando crea un recurso de registro conectado cuyo nombre es el valor de *$CONNECTED_REGISTRY_RW* y lo vincula al registro en la nube cuyo nombre es el valor de *$REGISTRY_NAME*. En las guías de inicio rápido posteriores, aprenderá sobre las opciones que tiene para implementar el registro conectado. 
* Los repositorios especificados se sincronizarán entre el registro en la nube y el registro conectado una vez implementado. 
* Dado que no se especifica ninguna opción `--mode` para el registro conectado, se crea en el [modo ReadWrite](intro-connected-registry.md#modes) predeterminado. 
* Dado que no hay ninguna programación de sincronización definida para este registro conectado, los repositorios se sincronizarán entre el registro en la nube y el registro conectado sin interrupciones.

  > [!IMPORTANT]
  > Para admitir escenarios anidados en los que las capas inferiores no tienen acceso a Internet, siempre debe permitir la sincronización del repositorio `acr/connected-registry`. Este repositorio contiene la imagen del entorno de ejecución del registro conectado.

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>Creación de un recurso de registro conectado para la funcionalidad de solo lectura

También puede usar el comando [az acr connected-registry create][az-acr-connected-registry-create] para crear un registro conectado con funcionalidad de solo lectura. 

```azurecli
# Set the CONNECTED_REGISTRY_READ environment variable to provide a name for the connected registry with read-only functionality
CONNECTED_REGISTRY_RO=<connnected-registry-name>
az acr connected-registry create --registry $REGISTRY_NAME \
  --parent $CONNECTED_REGISTRY_RW \
  --name $CONNECTED_REGISTRY_RO \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy" \
  --mode ReadOnly
```

Este comando crea un recurso de registro conectado cuyo nombre es el valor de *$CONNECTED_REGISTRY_RO* y lo vincula al registro en la nube cuyo nombre es el valor de *$REGISTRY_NAME*. 
* Los repositorios especificados se sincronizarán entre el registro primario denominado con el valor *$CONNECTED_REGISTRY_RW* y el registro conectado una vez implementado.
* Este recurso se crea en el [modo ReadOnly](intro-connected-registry.md#modes), que habilita la funcionalidad de solo lectura (extracción de artefactos) una vez implementada. 
* Dado que no hay ninguna programación de sincronización definida para este registro conectado, los repositorios se sincronizarán entre el registro primario y el registro conectado sin interrupciones.

## <a name="verify-that-the-resources-are-created"></a>Comprobación de la creación de los recursos

Puede usar el comando [az acr connected-registry list][az-acr-connected-registry-list] para comprobar que se han creado los recursos. 

```azurecli
az acr connected-registry list \
  --registry $REGISTRY_NAME \
  --output table
```

Debería ver una respuesta que indica lo siguiente. Dado que los registros conectados aún no están implementados, el estado "Sin conexión" indica que están desconectados actualmente de la nube.

```
NAME                 MODE        CONNECTION STATE    PARENT               LOGIN SERVER    LAST SYNC (UTC)
-------------------  --------    ------------------  -------------------  --------------  -----------------
myconnectedregrw    ReadWrite    Offline
myconnectedregro    ReadOnly     Offline             myconnectedregrw
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, usará la CLI de Azure para crear dos recursos de registros conectados en Azure. Esos nuevos recursos de registro conectados están vinculados al registro en la nube y permiten la sincronización de artefactos con el registro en la nube.

Continúe con las guías de implementación del registro conectado para aprender a implementar y usar un registro conectado en la infraestructura de IoT Edge.

> [!div class="nextstepaction"]
> [Guía de inicio rápido: Implementación del registro conectado en IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
