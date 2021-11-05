---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/16/2021
ms.author: danlep
ms.openlocfilehash: a5bfee2b0348a68476855edc2b113338b779f20d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030505"
---
## <a name="import-images-to-your-cloud-registry"></a>Importación de imágenes en el registro en la nube

Importe las siguientes imágenes de contenedor en el registro en la nube mediante el comando [az acr import](/cli/azure/acr#az_acr_import). Omita este paso si ya ha importado estas imágenes.

### <a name="connected-registry-image"></a>Imagen de registro conectado

Para admitir escenarios de IoT Edge, la imagen de contenedor para el entorno de ejecución del registro conectado debe estar disponible en la instancia privada de Azure Container Registry. Use el comando [az acr import](/cli/azure/acr#az_acr_import) para importar la imagen del registro conectado en el registro privado. 

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/acr/connected-registry:0.5.0
```

### <a name="iot-edge-and-api-proxy-images"></a>IoT Edge e imágenes del proxy de API

Para admitir el registro conectado en IoT Edge anidado, debe implementar módulos para IoT Edge y el proxy de API. Importe estas imágenes en el registro privado.

El [módulo del proxy de API de IoT Edge](../articles/iot-edge/how-to-configure-api-proxy-module.md) permite que un dispositivo IoT Edge exponga varios servicios mediante el protocolo HTTPS en el mismo puerto, como 443.

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-agent:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-hub:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-api-proxy:1.1.2

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-diagnostics:1.2.4
```

### <a name="hello-world-image"></a>Imagen de Hola mundo

Para probar el registro conectado, importe la imagen `hello-world`. Este repositorio se sincronizará con el registro conectado y lo extraerán los clientes del registro conectado.

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/hello-world:1.1.2
```