---
title: Extracción de imágenes de un registro conectado
description: Use los comandos de la CLI de Azure Container Registry para configurar un token de cliente y extraer imágenes de un registro conectado en un dispositivo IoT Edge.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5409afea3ab1ab1ebedaed87adbafd2b15fe9418
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017377"
---
# <a name="pull-images-from-a-connected-registry-on-iot-edge-device"></a>Extracción de imágenes de un registro conectado en un dispositivo IoT Edge

Para extraer imágenes de un [registro conectado,](intro-connected-registry.md)configure un [token de cliente](overview-connected-registry-access.md#client-tokens) y pase las credenciales del token para acceder al contenido del Registro.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
* Recurso del registro conectado en Azure. Para ver los pasos de implementación, consulte [Guía de inicio rápido: Creación de un registro conectado mediante la CLI de Azure][quickstart-connected-registry-cli].
* Instancia del registro conectado implementada en un dispositivo IoT Edge. Para ver los pasos de implementación, consulte [Inicio rápido: Implementación de un registro conectado en un dispositivo IoT Edge](quickstart-deploy-connected-registry-iot-edge-cli.md) o [Tutorial: Implementación de un registro conectado en dispositivos IoT Edge anidados](tutorial-deploy-connected-registry-nested-iot-edge-cli.md). En los comandos de este artículo, el nombre del registro conectado se almacena en la variable de entorno *$CONNECTED_REGISTRY_RW*.

## <a name="create-a-scope-map"></a>Creación de un mapa de ámbito

Use el comando [az acr scope-map create][az-acr-scope-map-create] para crear un mapa de ámbito para el acceso de lectura al repositorio `hello-world`:

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr scope-map create \
  --name hello-world-scopemap \
  --registry $REGISTRY_NAME \
  --repository hello-world content/read \
  --description "Scope map for the connected registry."
```

## <a name="create-a-client-token"></a>Creación de un token de cliente

Use el comando [az acr token create][az-acr-token-create] para crear un token de cliente y asociarlo a la asignación de ámbito recién creada:

```azurecli
az acr token create \
  --name myconnectedregistry-client-token \
  --registry $REGISTRY_NAME \
  --scope-map hello-world-scopemap
```

El comando devolverá detalles sobre el token recién generado, incluidas las contraseñas.

  > [!IMPORTANT]
  > Asegúrese de guardar las contraseñas generadas. Son contraseñas únicas y no se pueden recuperar. Puede generar contraseñas nuevas mediante el comando [az acr token credential generate][az-acr-token-credential-generate].

## <a name="update-the-connected-registry-with-the-client-token"></a>Actualización del registro conectado con el token de cliente

Use el comando [az acr connected-registry update][az-acr-connected-registry-update] para actualizar el registro conectado con el token de cliente recién creado. 

```azurecli
az acr connected-registry update \
  --name $CONNECTED_REGISTRY_RW \
  --registry $REGISTRY_NAME \
  --add-client-token myconnectedregistry-client-token
```

## <a name="pull-an-image-from-the-connected-registry"></a>Extracción de una imagen del registro conectado

Desde una máquina con acceso al dispositivo IoT Edge, use el siguiente comando de ejemplo para iniciar sesión en el registro conectado con las credenciales del token de cliente. Para consultar procedimientos recomendados para administrar credenciales de inicio de sesión, consulte la referencia del comando [docker login](https://docs.docker.com/engine/reference/commandline/login/).

> [!CAUTION]
> Si configura el registro conectado como un registro no seguro, actualice la lista de registros no seguros en la configuración del demonio de Docker para incluir la dirección IP (o FQDN) y el puerto del registro conectado en el dispositivo IoT Edge. Esta configuración solo se debe usar con fines de prueba. Para obtener más información, consulte [Prueba de un registro no seguro](https://docs.docker.com/registry/insecure/).

```
docker login --username myconnectedregistry-client-token \
  --password <token_password> <IP_address_or_FQDN_of_connected_registry>:<port>
```

En escenarios de IoT Edge, asegúrese de incluir el puerto que se usa para llegar al registro conectado en el dispositivo. Ejemplo:

```
docker login --username myconnectedregistry-client-token \
  --password xxxxxxxxxxx 192.0.2.13:8000
```

Ejecute el siguiente comando para extraer la imagen `hello-world`:

```
docker pull <IP_address_or_FQDN_of_connected_registry>:<port>/hello-world
```

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre los [tokens con ámbito de repositorio](container-registry-repository-scoped-permissions.md).
* Obtenga más información sobre el [acceso a un registro conectado](overview-connected-registry-access.md).

<!-- LINKS - internal -->
[az-acr-scope-map-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential#az_acr_token_credential_generate
[az-acr-connected-registry-update]: /cli/azure/acr/connect-registry#az_acr_connected_registry_update] 
[container-registry-intro]: container-registry-intro.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
