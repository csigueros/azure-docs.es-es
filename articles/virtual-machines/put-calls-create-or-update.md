---
title: PUT llama a operaciones de creación o actualización
description: Llamadas PUT a operaciones de creación o actualización en recursos de proceso
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: 07d22c4535bc10012ea5df6cfb222f5a53eb0a81
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697290"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>Llamadas PUT a operaciones de creación o actualización en recursos de proceso

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes


Los recursos de `Microsoft.Compute` no admiten la definición convencional de la semántica de *HTTP PUT*. En su lugar, estos recursos utilizan la semántica de PATCH para los verbos PUT y PATCH.

Las operaciones de **creación** aplican valores predeterminados cuando sea necesario. Sin embargo, las **actualizaciones** de recursos realizadas a través de PUT o PATCH no aplican ninguna propiedad predeterminada. Las operaciones de **actualización** aplican la semántica estricta de PATCH.

Por ejemplo, la propiedad `caching` de disco de una máquina virtual tiene como valor predeterminado `ReadWrite` si el recurso es un disco del sistema operativo.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Sin embargo, para las operaciones de **actualización** cuando se excluye una propiedad o se pasa un valor *NULL*, permanecerá sin cambios y no habrá valores predeterminados.

Esto es importante al enviar operaciones de actualización a un recurso con la intención de quitar una asociación. Si ese recurso es un recurso de `Microsoft.Compute`, es necesario llamar explícitamente a la propiedad correspondiente que desea quitar y asignarle un valor. Para lograr esto, los usuarios pueden pasar una cadena vacía como **" "** . Esto le indicará a la plataforma que quite esa asociación.

> [!IMPORTANT]
> No se admite la aplicación de revisión en un elemento de matriz. En su lugar, el cliente tiene que realizar una solicitud PUT o PATCH con todo el contenido de la matriz actualizada. Por ejemplo, para desasociar un disco de datos de una máquina virtual, realice una solicitud GET para obtener el modelo de máquina virtual actual, quite el disco que se va a desasociar de `properties.storageProfile.dataDisks` y realice una solicitud PUT con la entidad máquina virtual actualizada.

## <a name="examples"></a>Ejemplos

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Corrección de la carga útil para quitar una asociación de grupos con ubicación por proximidad

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Cargas útiles para quitar una asociación de grupos con ubicación por proximidad

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre las llamadas de creación o actualización para [máquinas virtuales](/rest/api/compute/virtualmachines/createorupdate) y [conjuntos de escalado de máquinas virtuales](/rest/api/compute/virtualmachinescalesets/createorupdate)