---
title: Deshabilitación de la exportación de artefactos
description: Establezca una propiedad del registro para evitar la filtración de datos de un registro de contenedor de Azure prémium.
ms.topic: how-to
ms.date: 07/27/2021
ms.openlocfilehash: 8fa32197069376c71c035df0285852f4041efc64
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779860"
---
# <a name="disable-export-of-artifacts-from-an-azure-container-registry"></a>Deshabilitación de la exportación de artefactos desde un registro de contenedor de Azure 

Para evitar que los usuarios del registro de una organización filtre artefactos de forma malintencionada o accidental fuera de una red virtual, puede configurar la *directiva de exportación* del registro para deshabilitar las exportaciones.

La directiva de exportación es una propiedad introducida en la versión de API **2021-06-01-preview** para los registros de contenedor prémium. La propiedad `exportPolicy`, cuando su estado se establece en `disabled`, bloquea la exportación de artefactos desde un registro restringido a la red cuando un usuario intenta:

* [Importación](container-registry-import-images.md) de los artefactos del registro a otro registro de contenedor de Azure
* Creación de una [canalización de exportación](container-registry-transfer-images.md) del registro para transferir artefactos a otro registro de contenedor

> [!NOTE]
> Deshabilitar la exportación de artefactos no impide que los usuarios autorizados accedan al registro dentro de la red virtual para extraer artefactos o realizar otras operaciones de plano de datos. Para auditar este uso, se recomienda configurar las opciones de diagnóstico para [supervisar](monitor-service.md) las operaciones del registro. 

## <a name="prerequisites"></a>Requisitos previos

* Un registro de contenedor prémium configurado con un [punto de conexión privado](container-registry-private-link.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="other-requirements-to-disable-exports"></a>Otros requisitos para deshabilitar las exportaciones

* **Deshabilitar el acceso a la red pública**: para deshabilitar la exportación de artefactos, también se debe deshabilitar el acceso público al registro (la `publicNetworkAccess`propiedad del registro debe establecerse en `disabled`). Puede deshabilitar el acceso de red pública al registro antes de deshabilitar la exportación o deshabilitarla al mismo tiempo.

    Al deshabilitar el acceso al punto de conexión público del registro, asegúrese de que las operaciones del registro solo se permitan dentro de la red virtual. Se prohíbe el acceso público al registro para extraer artefactos y realizar otras operaciones. 

*  **Quitar canalizaciones de exportación:** antes de establecer el estado `exportPolicy` del registro en `disabled`, elimine las canalizaciones de exportación existentes configuradas en el registro. Si se configura una canalización, no se puede cambiar el estado de `exportPolicy`.

## <a name="disable-exportpolicy-for-an-existing-registry"></a>Deshabilitación de exportPolicy para un registro existente

Cuando se crea un registro, el estado de `exportPolicy` se establece en de forma predeterminada en `enabled`, lo que permite exportar artefactos. Puede actualizar el estado a `disabled` mediante una plantilla de ARM o el comando `az resource update`.

### <a name="arm-template"></a>Plantilla ARM 

Incluya el siguiente código JSON para actualizar el estado de `exportPolicy` y establezca la propiedad en `publicNetworkAccess` en `disabled`. Obtenga más información sobre cómo [implementar recursos con plantillas de ARM](../azure-resource-manager/templates/deploy-cli.md).

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
      "publicNetworkAccess": "disabled",
      "policies": {
        "exportPolicy": {
          "status": "disabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

Ejecute [az resource update](/cli/azure/resource/#az_resource_update) para establecer el estado de `exportPolicy` de un registro existente en `disabled`. Sustituya los nombres del registro y el grupo de recursos.

Como se muestra en este ejemplo, al deshabilitar la propiedad `exportPolicy`, establezca también la propiedad `publicNetworkAccess` en `disabled`.

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=disabled" \
    --set "properties.publicNetworkAccess=disabled"  
```

La salida muestra que el estado de la directiva de exportación está deshabilitado.

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "identity": null,
  "kind": null,
  "location": "centralus",
  "managedBy": null,
  "name": "myregistry",
  "plan": null,
  "properties": {
    [...]
    "policies": {
      "exportPolicy": {
        "status": "disabled"
      },
      "quarantinePolicy": {
        "status": "disabled"
      },
      "retentionPolicy": {
        "days": 7,
        "lastUpdatedTime": "2021-07-20T23:20:30.9985256+00:00",
        "status": "disabled"
      },
      "trustPolicy": {
        "status": "disabled",
        "type": "Notary"
      },
    "privateEndpointConnections": [],
    "provisioningState": "Succeeded",
    "publicNetworkAccess": "Disabled",
    "zoneRedundancy": "Disabled"
[...]
}
```

## <a name="enable-exportpolicy"></a>Habilitación de exportPolicy 

Después de deshabilitar el estado de `exportPolicy` en un registro, puede volver a habilitarlo en cualquier momento mediante una plantilla de ARM o el comando `az resource update`.

### <a name="arm-template"></a>Plantilla ARM 

Incluya el siguiente código JSON para actualizar el estado de `exportPolicy` en `enabled`. Obtenga más información sobre cómo [implementar recursos con plantillas de ARM](../azure-resource-manager/templates/deploy-cli.md).

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
     "policies": {
        "exportPolicy": {
          "status": "enabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

Ejecute [az resource update](/cli/azure/resource/#az_resource_update) para establecer el estado de `exportPolicy` en `enabled`. Sustituya los nombres del registro y el grupo de recursos.

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=enabled"
```
 
## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Roles y permisos de Azure Container Registry](container-registry-roles.md).
* Si desea evitar la eliminación accidental de artefactos del Registro, consulte [Bloqueo de imágenes de contenedor](container-registry-image-lock.md).
* Más información sobre las [directivas de Azure](container-registry-azure-policy.md) integradas para proteger Azure Container Registry
