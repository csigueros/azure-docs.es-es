---
title: Introducción a los grupos de discos de Azure (versión preliminar)
description: Obtenga información sobre los grupos de discos de Azure (versión preliminar).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b0dfb510ecf1722f1b8df9289a864ecd519be055
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653153"
---
# <a name="azure-disk-pools-preview"></a>Grupos de discos de Azure (versión preliminar)

Un grupo de discos de Azure (versión preliminar) es un recurso de Azure que permite que las aplicaciones y las cargas de trabajo accedan a un grupo de discos administrados desde un único punto de conexión. Un grupo de discos puede exponer un destino de interfaz estándar de equipos pequeños de Internet (iSCSI) para habilitar el acceso a los datos de los discos dentro de este grupo mediante iSCSI. Cada grupo de discos puede tener un destino iSCSI y cada disco se puede exponer como un LUN iSCSI. Puede conectar discos del grupo de discos a hosts de Azure VMware Solution como almacenes de datos. Esto le permite escalar el almacenamiento independientemente de los hosts de Azure VMware Solution. Una vez configurado el almacén de datos, puede crear volúmenes en él y adjuntarlos a las instancias de VMware.

## <a name="how-it-works"></a>Funcionamiento

Cuando se implementa un grupo de discos, se crea automáticamente un grupo de recursos administrado. Este grupo de recursos administrado contiene todos los recursos de Azure necesarios para el funcionamiento de un grupo de discos. La convención de nomenclatura para estos grupos de recursos es: MSP_(nombre-de-grupo-de-recursos)_(nombre-de-grupo-de-discos)\_(nombre-de-región).

Al agregar un disco administrado al grupo de discos, el disco se asocia a controladores iSCSI administrados. Se pueden agregar varios discos administrados como destinos de almacenamiento a un grupo de discos; cada destino de almacenamiento se presenta como un LUN iSCSI en el destino iSCSI del grupo de discos. Los grupos de discos ofrecen compatibilidad nativa con Azure VMware Solution. Un clúster de Azure VMware Solution puede conectarse a un grupo de discos, lo que abarcaría todos los hosts de Azure VMware Solution en ese entorno. En el diagrama siguiente se muestra cómo puede usar los grupos de discos con hosts de Azure VMware Solution.

:::image type="content" source="media/disks-pools/disk-pool-diagram.png" alt-text="Diagrama que muestra cómo funcionan los grupos de discos; cada controlador iSCSI puede acceder a cada disco Ultra a través de iSCSI y los hosts de Azure VMware Solution pueden acceder al controlador iSCSI mediante iSCSI.":::

## <a name="restrictions"></a>Restricciones

En la versión preliminar, los grupos de discos tienen las siguientes restricciones:

- Solo se pueden agregar discos Ultra o SSD Premium a un grupo de discos.
- Actualmente no se admiten los discos que usan [almacenamiento con redundancia de zona (ZRS)](disks-redundancy.md#zone-redundant-storage-for-managed-disks-preview). 

### <a name="regional-availability"></a>Disponibilidad regional

Los grupos de discos están actualmente disponibles en las regiones siguientes:

- Este de Australia
- Centro de Canadá
- Centro de EE. UU.
- Este de EE. UU.
- Oeste de EE. UU. 2
- Japón Oriental
- Norte de Europa
- Oeste de Europa
- Sudeste de Asia
- Sur de Reino Unido 2


## <a name="billing"></a>Facturación

Al implementar un grupo de discos, hay dos áreas principales que incurrirán en costos de facturación:

- Los discos agregados al grupo de discos
- Los recursos de Azure implementados en el grupo de recursos administrados que acompañan al grupo de discos. Estos recursos son:
    - Máquinas virtuales.
    - Discos administrados.
    - Una interfaz de red.
    - Una cuenta de almacenamiento para los registros de diagnóstico y las métricas.
        
Se le facturarán los recursos dentro de este grupo de recursos administrados y los discos individuales que son el almacenamiento de datos real. Por ejemplo, si tiene un grupo de discos con un disco P30 agregado, se le facturará por el disco P30 y por todos los recursos implementados en el grupo de recursos administrado. Aparte de estos recursos y los discos, no hay cargos de servicio adicionales para los grupos de discos. Para obtener más información sobre el grupo de recursos administrado, consulte la sección [Funcionamiento](#how-it-works).

Consulte la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) para obtener los precios regionales de máquinas virtuales y discos y evaluar el costo de un grupo de discos. Los recursos de Azure consumidos por el grupo de discos se pueden tener en cuenta en Azure Reservations, si lo tiene.


## <a name="next-steps"></a>Pasos siguientes

Consulte la [guía de planeamiento de grupos de discos](disks-pools-planning.md).
