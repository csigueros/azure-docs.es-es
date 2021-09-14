---
title: Migración de la configuración del grupo de Batch de Cloud Services a Virtual Machines
description: Obtenga información acerca de cómo actualizar la configuración del grupo a la configuración más reciente y recomendada.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 9d4ce46c5291333f161f260c3b4706ea9dcf9a24
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538892"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Migración de la configuración del grupo de Batch de Cloud Services a la máquina virtual

Actualmente, los grupos de Batch se pueden crear mediante [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) o [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). Se recomienda usar únicamente la configuración de máquina virtual, ya que esta es compatible con todas las funcionalidades de Batch.

Los grupos de configuración de Cloud Services no admiten algunas de las características de Batch actuales ni admitirán las características que se acaban de agregar. No podrá crear nuevos grupos "CloudServiceConfiguration" ni agregar nuevos nodos a los grupos existentes después del [29 de febrero de 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

Si las soluciones de Batch actualmente usan grupos "cloudServiceConfiguration", se recomienda cambiar a "virtualMachineConfiguration" lo antes posible. Esto le permitirá beneficiarse de todas las funcionalidades de Batch, como una ampliada [selección de serie de máquinas virtuales](batch-pool-vm-sizes.md), máquinas virtuales de Linux, [contenedores](batch-docker-container-workloads.md), [redes virtuales de Azure Resource Manager](batch-virtual-network.md) y [de cifrado de discos de nodo](disk-encryption.md).

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Creación de un grupo mediante la configuración de la máquina virtual

No puede cambiar un grupo activo existente que use "cloudServiceConfiguration" para usar "virtualMachineConfiguration". En su lugar, deberá crear grupos nuevos. Una vez que haya creado los nuevos grupos de "virtualMachineConfiguration" y replicado todos los trabajos y tareas, puede eliminar los grupos "cloudServiceConfiguration" antiguos que ya no usa.

Todas las API de Batch, las herramientas de línea de comandos, Azure Portal y la interfaz de usuario de Batch Explorer le permiten crear grupos mediante "virtualMachineConfiguration".

Para ver un tutorial sobre el proceso de creación de grupos que usan "virtualMachineConfiguration", vea el [tutorial de .NET](tutorial-parallel-dotnet.md) o el [tutorial de Python](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Diferencias de configuración de grupos

Algunas de las diferencias principales entre las dos configuraciones son las siguientes:

- Los nodos de grupo "cloudServiceConfiguration" solo usan el sistema operativo Windows. Los grupos "virtualMachineConfiguration" pueden usar el sistema operativo Windows o Linux.
- En comparación con los grupos "cloudServiceConfiguration", los grupos "virtualMachineConfiguration" tienen un conjunto más completo de funcionalidades, como la compatibilidad con contenedores, los discos de datos y el cifrado de discos.
- Los tiempos de inicio y eliminación de grupos y nodos pueden diferir ligeramente entre los grupos "cloudServiceConfiguration" y "virtualMachineConfiguration".
- Los nodos del grupo "virtualMachineConfiguration" usan discos de sistema operativo administrados. El [tipo de disco administrado](../virtual-machines/disks-types.md) que se usa para cada nodo depende del tamaño de máquina virtual elegido para el grupo. Si se especifica un tamaño de máquina virtual "s" para el grupo, por ejemplo, "Standard_D2s_v3", se usa una SSD Premium. Si se especifica un tamaño de máquina virtual "no s", por ejemplo, "Standard_D2_v3", se usa una HDD estándar.

   > [!IMPORTANT]
   > Al igual que con Virtual Machines y Virtual Machine Scale Sets, el disco administrado del SO que se usa para cada nodo conlleva un costo, que es adicional al costo de las máquinas virtuales. Los grupos "virtualMachineConfiguration" pueden usar [discos de SO efímeros](create-pool-ephemeral-os-disk.md), que crean el disco de SO en la caché de máquinas virtuales o SSD temporal, para evitar costos adicionales asociados a los discos administrados. No hay ningún costo de disco de SO para los nodos "cloudServiceConfiguration", ya que el disco de SO se crea en la SSD local de los nodos.

## <a name="azure-data-factory-custom-activity-pools"></a>Grupos de actividades personalizadas de Azure Data Factory

Los grupos de Azure Batch se pueden usar para ejecutar actividades personalizadas de Data Factory. Los grupos "cloudServiceConfiguration" que se usan para ejecutar actividades personalizadas deberán eliminarse, y será necesario crear grupos "virtualMachineConfiguration".

Cuando cree sus nuevos grupos para ejecutar actividades personalizadas de Data Factory, siga estos procedimientos:

- Detenga todas las canalizaciones antes de crear los nuevos grupos y eliminar los antiguos para asegurarse de que no se interrumpan las ejecuciones.
- Se puede usar el mismo identificador de grupo para evitar cambios de configuración del servicio vinculado.
- Reanude las canalizaciones cuando se hayan creado nuevos grupos.

Para obtener más información sobre el uso de Azure Batch para ejecutar actividades personalizadas de Data Factory, consulte [Servicio vinculado de Azure Batch ](../data-factory/compute-linked-services.md#azure-batch-linked-service) y [Actividades personalizadas en una canalización de Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre las [configuraciones de grupo](nodes-and-pools.md#configurations).
- Obtenga más información sobre los [procedimientos recomendados de los grupos](best-practices.md#pools).
- Consulte la referencia de la API REST para la [adición de grupos](/rest/api/batchservice/pool/add) y [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).