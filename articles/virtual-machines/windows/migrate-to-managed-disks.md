---
title: Migración de máquinas virtuales de Azure a Managed Disks
description: Migre máquinas virtuales de Azure creadas con discos no administrados en cuentas de almacenamiento para usar Managed Disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9fc976ae7854ef3f787492c012877c9203e7712a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690167"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migración de VM de Azure a Managed Disks en Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows 

Azure Managed Disks simplifica la administración de almacenamiento al eliminar la necesidad de administrar cuentas de almacenamiento por separado.  También puede migrar las VM de Azure existentes a Managed Disks para aprovechar la mejor confiabilidad de las VM en un conjunto de disponibilidad. De esta forma, se garantiza que los discos de las distintas máquinas virtuales de un conjunto de disponibilidad estén lo suficientemente aisladas entre sí como para evitar un único punto de error. Coloca automáticamente discos de distintas VM en un conjunto de disponibilidad en unidades de escalado de almacenamiento diferentes (marcas de tiempo), lo que limita el impacto de errores únicos de unidad de escalado de almacenamiento generados debido a errores de hardware y software.
En virtud de sus necesidades, puede elegir entre cuatro tipos de opciones de almacenamiento. Para información acerca de los tipos de disco disponibles, consulte nuestro artículo [Selección de un tipo de disco](../disks-types.md).

## <a name="migration-scenarios"></a>Escenarios de migración

Puede migrar a Managed Disks en los escenarios siguientes:

|Escenario  |Artículo  |
|---------|---------|
|Conversión de VM independientes y VM de un conjunto de disponibilidad para establecer discos administrados     |[Conversión de VM para usar discos administrados](convert-unmanaged-to-managed-disks.md)         |
|Conversión de una sola máquina virtual del modelo clásico a Resource Manager en discos administrados     |[Creación de una VM desde un disco duro virtual clásico](create-vm-specialized-portal.md)         |
|Conversión de todas las máquinas virtuales de una red virtual desde el modelo clásico a Resource Manager en discos administrados     |[Migración de recursos de IaaS desde el modelo clásico a Resource Manager](../migration-classic-resource-manager-ps.md) y luego [Conversión de una VM desde discos no administrados a discos administrados](convert-unmanaged-to-managed-disks.md)         |
|Actualización de las máquinas virtuales con discos no administrados estándar a máquinas virtuales con discos administrados prémium     | En primer lugar, [convierta una máquina virtual Windows con discos no administrados a discos administrados](convert-unmanaged-to-managed-disks.md). Después, [actualice el tipo de almacenamiento de un disco administrado](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Managed Disks](../managed-disks-overview.md)
- Revise el [precio de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).