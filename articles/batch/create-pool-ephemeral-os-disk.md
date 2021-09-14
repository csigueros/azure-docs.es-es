---
title: Uso de nodos de disco de SO efímero para grupos de Azure Batch
description: Obtenga información sobre cómo y por qué crear un grupo de Batch que use nodos de disco de SO efímero.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 760e2848917c6b1c502ac6ba9aae00d5ddd89c8c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544387"
---
# <a name="use-ephemeral-os-disk-nodes-for-azure-batch-pools"></a>Uso de nodos de disco de SO efímero para grupos de Azure Batch

Algunas series de máquinas virtuales (VM) de Azure admiten el uso de [discos de SO efímeros](../virtual-machines/ephemeral-os-disks.md), que crean el disco de SO en el almacenamiento local de la máquina virtual del nodo. La configuración predeterminada del grupo de Batch usa [discos administrados de Azure](../virtual-machines/managed-disks-overview.md) para el disco de SO del nodo, donde el disco administrado es como un disco físico, pero virtualizado y persistente en la instancia remota de Azure Storage.

Para las cargas de trabajo de Batch, las principales ventajas de usar discos de SO efímeros son los costos reducidos asociados a los grupos, la posibilidad de un tiempo de inicio de nodo más rápido y un mejor rendimiento de la aplicación debido a un mejor rendimiento del disco de SO. Al elegir si se deben usar discos de SO efímeros para la carga de trabajo, tenga en cuenta lo siguiente:

- Hay una menor latencia de lectura y escritura en los discos de SO efímeros, lo que puede dar lugar a un mejor rendimiento de la aplicación.
- No hay ningún costo de almacenamiento para los discos de SO efímeros, mientras que sí lo hay por cada disco de SO administrado.
- Volver a crear el nodo, cuando se admite en Batch, será más rápido para los discos efímeros en comparación con los discos administrados.
- El tiempo de inicio del nodo puede ser ligeramente más rápido cuando se usan discos de SO efímeros.
- La durabilidad y disponibilidad de los discos de SO efímeros es reducida; cuando se quita una máquina virtual por cualquier motivo, se pierde el disco de SO. Como las cargas de trabajo de Batch son sin estado de forma intrínseca y normalmente no dependen de los cambios en el disco de SO que se conserva, los discos de SO efímeros son adecuados para su uso en la mayoría de las cargas de trabajo de Batch.
- El uso de un disco de SO efímero no es compatible actualmente con todas las series de máquinas virtuales de Azure. Si un tamaño de máquina virtual no admite un disco de SO efímero, se debe usar un disco de SO administrado.

> [!NOTE]
> La configuración de discos de SO efímeros solo se aplica a los grupos "virtualMachineConfiguration" y no se admite en los grupos "cloudServiceConfiguration". Se recomienda usar "virtualMachineConfiguration" para los grupos de Batch, ya que los grupos "cloudServiceConfiguration" no admiten todas las características y no se planea ninguna funcionalidad nueva. No podrá crear nuevos grupos "cloudServiceConfiguration" ni agregar nuevos nodos a los grupos existentes después del [29 de febrero de 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Para más información, consulte [Migración de la configuración del grupo de Batch de Cloud Services a máquina virtual](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="vm-series-support"></a>Compatibilidad con series de máquina virtual

Para determinar si una serie de máquinas virtuales admite discos de SO efímeros, consulte la documentación de cada instancia de máquina virtual. Por ejemplo, las [series Ddv4 y Ddsv4](../virtual-machines/ddv4-ddsv4-series.md) admiten discos de SO efímeros.

Como alternativa, puede consultar mediante programación para comprobar la funcionalidad "EphemeralOSDiskSupported". En las [preguntas más frecuentes sobre discos de SO efímeros](../virtual-machines/ephemeral-os-disks.md#frequently-asked-questions) se proporciona un cmdlet de PowerShell de ejemplo para consultar esta funcionalidad.

## <a name="create-a-pool-that-uses-ephemeral-os-disks"></a>Creación de un grupo que use discos de SO efímeros

De forma predeterminada, la propiedad `EphemeralOSDiskSettings` no está establecida. Debe establecer esta propiedad para configurar el uso de discos de SO efímeros en los nodos del grupo.

En el ejemplo siguiente se muestra cómo crear un grupo de Batch donde los nodos usan discos de SO efímeros y no discos administrados.

### <a name="batch-net-api"></a>API de .NET de Batch

```csharp
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: nodeAgentSku
        );
virtualMachineConfiguration.OSDisk = new OSDisk();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings = new DiffDiskSettings();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings.Placement = DiffDiskPlacement.CacheDisk;
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [flujo de trabajo y los recursos principales del servicio Batch](batch-service-workflow-features.md), como grupos, nodos, trabajos y tareas.
- Obtenga información sobre los [costos asociados que pueden tener las cargas de trabajo de Azure Batch](budget.md).
