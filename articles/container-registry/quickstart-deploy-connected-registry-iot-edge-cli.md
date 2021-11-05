---
title: 'Inicio rápido: Implementación de un registro conectado en un dispositivo con IoT Edge'
description: Use comandos de la CLI de Azure y Azure Portal para implementar una instancia de Azure Container Registry conectada en un dispositivo con Azure IoT Edge.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: e7d7ac92bda1aade5a446e6b0fe2a2b9966d5eb9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091097"
---
# <a name="quickstart-deploy-a-connected-registry-to-an-iot-edge-device"></a>Inicio rápido: Implementación de un registro conectado en un dispositivo con IoT Edge

En este inicio rápido, se usa la CLI de Azure para implementar un [registro conectado](intro-connected-registry.md) como un módulo en un dispositivo con Azure IoT Edge. El dispositivo con IoT Edge puede a la instancia de Azure Container Registry primaria en la nube.

Para obtener información general sobre el uso de un registro conectado con IoT Edge, consulte [Uso de un registro conectado con Azure IoT Edge](overview-connected-registry-and-iot-edge.md). Este escenario corresponde a un dispositivo en la [capa superior](overview-connected-registry-and-iot-edge.md#top-layer) de una jerarquía de IoT Edge. 


[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub y dispositivo con IoT Edge. Para ver los pasos de implementación, consulte [Inicio rápido: Implementación del primer módulo con IoT Edge en un dispositivo Linux virtual](../iot-edge/quickstart-linux.md).
  > [!IMPORTANT]
  > Para obtener acceso posteriormente a los módulos implementados en el dispositivo con IoT Edge, asegúrese de abrir los puertos 8000, 5671 y 8883 en el dispositivo. Para conocer los pasos de configuración, consulte [Apertura de puertos en una máquina virtual con Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md). 

* Recurso del registro conectado en Azure. Para conocer los pasos de implementación, consulte las guías de inicio rápido mediante la [CLI de Azure][quickstart-connected-registry-cli] o [Azure Portal][quickstart-connected-registry-portal]. 

    * En este escenario se puede usar un registro conectado en los modos `ReadWrite` o `ReadOnly`. 
    * En los comandos de este artículo, el nombre del registro conectado se almacena en la variable de entorno *CONNECTED_REGISTRY_RW*.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>Recuperación de la configuración de un registro conectado

Antes de implementar el registro conectado en el dispositivo con IoT Edge, es preciso recuperar la configuración del recurso del registro conectado en Azure.

Use el comando [az acr connected-registry get-settings][az-acr-connected-registry-get-settings] para obtener la información de configuración necesaria para instalar un registro conectado. En el ejemplo siguiente se especifica HTTPS como protocolo primario. Este protocolo es necesario cuando el registro primario es un registro en la nube.

```azurecli
az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https
```

De forma predeterminada, la información de la configuración no incluye la contraseña del [token de sincronización](overview-connected-registry-access.md#sync-token), que también es necesaria para implementar el registro conectado. Opcionalmente, genere una de las contraseñas pasando los parámetros `--generate-password 1` o `generate-password 2`. Guarde la contraseña generada en una ubicación segura. Recuerde que no podrá recuperarla de nuevo.

> [!WARNING]
> Al volver a generar una contraseña, se rotan las credenciales del token de sincronización. Si configuró un dispositivo con la contraseña anterior, es preciso que actualice la configuración.

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-a-deployment-manifest-for-iot-edge"></a>Configuración de un manifiesto de implementación para IoT Edge

Un manifiesto de implementación es un documento JSON que describe los módulos que se implementan en el dispositivo con IoT Edge. Para más información, consulte [Descripción de cómo se pueden utilizar, configurar y reutilizar los módulos de IoT Edge](../iot-edge/module-composition.md).

Para implementar el registro conectado y los módulos de proxy de API mediante el CLI de Azure, guarde el siguiente manifiesto de implementación localmente en forma de archivo `manifest.json`. Usará la ruta de acceso al archivo en la sección siguiente, al ejecutar el comando para aplicar la configuración al dispositivo.

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

## <a name="deploy-the-connected-registry-and-api-proxy-modules-on-iot-edge"></a>Implementación del registro conectado y los módulos de proxy de API en IoT Edge

Use el siguiente comando para implementar el registro conectado y los módulos de proxy de API en el dispositivo con IoT Edge, mediante el manifiesto de implementación creado en la sección anterior. Especifique el identificador del dispositivo de la capa superior de IoT Edge y el nombre de la instancia de IoT Hub donde se indica.

```azurecli
# Set the IOT_EDGE_TOP_LAYER_DEVICE_ID and IOT_HUB_NAME environment variables for use in the following Azure CLI command
IOT_EDGE_TOP_LAYER_DEVICE_ID=<device-id>
IOT_HUB_NAME=<hub-name>

az iot edge set-modules \
  --device-id $IOT_EDGE_TOP_LAYER_DEVICE_ID \
  --hub-name $IOT_HUB_NAME \
  --content manifest.json
```

Para más detalles, consulte [Implementación de módulos de Azure IoT Edge con la CLI de Azure](../iot-edge/how-to-deploy-modules-cli.md).

Para comprobar el estado del registro conectado, use el siguiente comando [az acr connected-registry show][az-acr-connected-registry-show]. El nombre del registro conectado es el valor de *$CONNECTED_REGISTRY_RW*.

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --output table
```

Después de una implementación correcta, el registro conectado muestra el estado `Online`.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a implementar un registro conectado en un dispositivo con IoT Edge. En las siguientes guías aprenderá a extraer imágenes del registro conectado recién implementado o a implementar el registro conectado en dispositivos con IoT Edge anidados.


> [!div class="nextstepaction"]
> [Extracción de imágenes de un registro conectado][pull-images-from-connected-registry]

> [!div class="nextstepaction"]
> [Tutorial: Implementación de un registro conectado en dispositivos con IoT Edge anidados][tutorial-connected-registry-nested]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registr#az_acr_connected_registry_show
[az-acr-import]:/cli/azure/acr#az_acr_import
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential?#az_acr_token_credential_generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
[tutorial-connected-registry-nested]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
