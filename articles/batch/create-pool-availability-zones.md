---
title: Creación de un grupo en zonas de disponibilidad
description: Aprenda a crear un grupo de Batch con directiva de zona para ayudarlo a protegerse de errores.
ms.topic: how-to
ms.date: 08/06/2021
ms.openlocfilehash: 0abec404b0ec951bb398cfb06ae41290e986a241
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741129"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Creación de un grupo de Azure Batch en Availability Zones

Las regiones de Azure que admiten [Availability Zones](https://azure.microsoft.com/global-infrastructure/availability-zones/) tienen un mínimo de tres zonas independientes, cada una de las cuales con su propia fuente de alimentación, red y sistema de refrigeración. Al crear un grupo de Azure Batch con la configuración de máquina virtual, puede elegir aprovisionar el grupo de Batch en Availability Zones. La creación del grupo con esta directiva de zona lo ayuda a proteger los nodos de ejecución de Batch de los errores de nivel de centro de datos de Azure.

Por ejemplo, puede crear el grupo con una directiva de zona en una región de Azure que admite tres instancias de Availability Zones. Si un centro de datos de Azure en una zona de disponibilidad tiene un error de infraestructura, el grupo de Batch seguirá teniendo nodos correctos en las otras dos instancias de Availability Zones, por lo que el grupo seguirá estando disponible para la programación de tareas.

## <a name="regional-support-and-other-requirements"></a>Compatibilidad regional y otros requisitos

Batch mantiene la paridad con Azure con compatibilidad de Availability Zones. Para usar la opción de zona, el grupo se debe crear en una [región de Azure compatible](../availability-zones/az-region.md).

Para que el grupo de Batch se asigne a través de las zonas de disponibilidad, la región de Azure en la que se crea el grupo debe admitir la SKU de máquina virtual solicitada en más de una zona. Para validar esto, llame a la [API Resource SKUs List](/rest/api/compute/resourceskus/list) y compruebe el campo **locationInfo** de [resourceSku](/rest/api/compute/resourceskus/list#resourcesku). Asegúrese de que se admite más de una zona para la SKU de máquina virtual solicitada.

Para las [cuentas de Batch del modo de suscripción de usuario](accounts.md#batch-accounts), asegúrese de que la suscripción en la que va a crear el grupo no tenga una restricción de oferta de zona en la SKU de máquina virtual solicitada. Para confirmar esto, llame a la [API Resource SKUs List](/rest/api/compute/resourceskus/list) y compruebe [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Si existe una restricción de zona, puede enviar una [incidencia de soporte técnico](/troubleshoot/azure/general/region-access-request-process) para quitar la restricción de zona.

Además, tenga en cuenta que no puede crear un grupo con una directiva de zona si tiene habilitada la comunicación entre nodos y usa una [SKU de máquina virtual que admite InfiniBand](../virtual-machines/workloads/hpc/enable-infiniband.md).

## <a name="create-a-batch-pool-across-availability-zones"></a>Creación de un grupo de Batch en Availability Zones

En los ejemplos siguientes se muestra cómo crear un grupo de Batch en Availability Zones.

> [!NOTE]
> Al crear el grupo con una directiva de zona, el servicio Batch intentará asignar el grupo en todas las instancias de Availability Zones de la región seleccionada. No puede especificar una asignación determinada en las zonas.

### <a name="batch-management-client-net-sdk"></a>SDK de .NET de cliente de administración de Batch

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>REST de Batch

URL de la API REST

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Cuerpo de la solicitud

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [flujo de trabajo y los recursos principales del servicio Batch](batch-service-workflow-features.md), como grupos, nodos, trabajos y tareas.
- Obtenga información sobre la [creación un grupo en una subred de una red virtual de Azure](batch-virtual-network.md).
- Obtenga información sobre la [creación de un grupo de Azure Batch sin direcciones IP públicas](./batch-pool-no-public-ip-address.md).
