---
title: Migración desde AWS y otras plataformas a Managed Disks en Azure
description: Cree máquinas virtuales en Azure con VHD cargados desde otras nubes, como AWS u otras plataformas de virtualización, y utilice Azure Managed Disks.
author: roygara
manager: twooley
ms.service: storage
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 77d2e23b202957018ee80186dd28b6b2360698fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740071"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migración desde Amazon Web Services (AWS) y otras plataformas a Managed Disks en Azure

Puede cargar archivos VHD desde AWS o soluciones de virtualización locales en Azure para crear máquinas virtuales que usen Managed Disks. Azure Managed Disks elimina la necesidad de administrar las cuentas de almacenamiento para las máquinas virtuales de IaaS de Azure. Especifique solo el tipo y el tamaño del disco que necesita, y Azure lo crea y administra automáticamente. 

Puede cargar VHD generalizados o especializados. 
- **VHD generalizado**: elimina toda la información personal de la cuenta mediante Sysprep. 
- **VHD especializado**: mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. 

> [!IMPORTANT]
> Antes de cargar los VHD en Azure, debe consultar [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](prepare-for-upload-vhd-image.md)
>
>


| Escenario                                                                                                                         | Documentación                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Tiene instancias EC2 de AWS existentes que le gustaría migrar a máquinas virtuales de Azure mediante discos administrados.                              | [Migración de una máquina virtual de Amazon Web Services (AWS) a Azure](aws-to-azure.md)                           |
| Tiene una máquina virtual de otra plataforma de virtualización que le gustaría usar como imagen para crear varias máquinas virtuales de Azure. | [Carga de un VHD generalizado y uso de este para crear una nueva máquina virtual en Azure](upload-generalized-managed.md) |
| Cuenta con una VM personalizada de forma exclusiva que quisiera recrear en Azure.                                                      | [Carga de un VHD especializado en Azure y creación de una máquina nueva](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Introducción a Managed Disks

Azure Managed Disks simplifica la administración de la máquina virtual al eliminar la necesidad de administrar cuentas de almacenamiento. Managed Disks también aprovecha las ventajas de la mejor confiabilidad de las máquinas virtuales de un conjunto de disponibilidad. Esto garantiza que los discos de las distintas VM de un conjunto de disponibilidad estarán lo suficientemente aislados entre sí como para evitar un único punto de error. Coloca automáticamente discos de distintas VM en un conjunto de disponibilidad en unidades de escalado de almacenamiento diferentes (marcas de tiempo), lo que limita el impacto de errores únicos de unidad de escalado de almacenamiento generados debido a errores de hardware y software.
En virtud de sus necesidades, puede elegir entre cuatro tipos de opciones de almacenamiento. Para información acerca de los tipos de disco disponibles, consulte nuestro artículo [Selección de un tipo de disco](../disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planeación de la migración a Managed Disks

Esta sección puede ayudarlo a tomar la mejor decisión sobre los tipos de discos y VM.

Si planea migrar de discos no administrados a discos administrados, debe saber que los usuarios con el rol [Colaborador de máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) no podrán cambiar el tamaño de la máquina virtual (como podían hacer antes de la conversión). El motivo es que las máquinas virtuales con discos administrados requieren que el usuario tenga el permiso de escritura/discos/Microsoft.Compute para los discos del sistema operativo.

### <a name="location"></a>Location

Elija una ubicación en la que Azure Managed Disks esté disponible. Si va a migrar a SSD Premium, asegúrese de que Premium Storage también esté disponible en la región a la que planea migrar. Consulte [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) para obtener información actualizada sobre las ubicaciones disponibles.

### <a name="vm-sizes"></a>Tamaños de VM

Si va a migrar a SSD Premium, debe actualizar el tamaño de la máquina virtual a un tamaño compatible con Premium Storage disponible en la región en la que se ubica la máquina virtual. Revise los tamaños de máquina virtual compatibles con Premium Storage. Las especificaciones de tamaño de las máquinas virtuales de Azure se muestran en [Tamaños de máquinas virtuales](../sizes.md).
Revise las características de rendimiento de las máquinas virtuales que trabajan con Premium Storage y elija el tamaño de máquina virtual que se mejor se ajuste a su carga de trabajo. Procure que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.

### <a name="disk-sizes"></a>Tamaños de disco

Para más información sobre los tipos de disco disponibles, consulte [¿Qué tipos de disco están disponibles en Azure?](../disks-types.md)

### <a name="disk-caching-policy"></a>Directiva de almacenamiento en caché de disco 

**Managed Disks Premium**

De forma predeterminada, la directiva de almacenamiento en caché de los discos es *Solo lectura* para todos los discos de datos Premium y *Lectura y Escritura* para el disco de sistema operativo Premium conectado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación.

### <a name="pricing"></a>Precios

Revise los [precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="next-steps"></a>Pasos siguientes

- Antes de cargar los VHD en Azure, debe consultar [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](prepare-for-upload-vhd-image.md)