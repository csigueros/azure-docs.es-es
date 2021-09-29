---
title: Eliminación temporal para contenedores
titleSuffix: Azure Storage
description: La eliminación temporal para contenedores protege los datos para que pueda recuperarlos más fácilmente si una aplicación u otro usuario de la cuenta de almacenamiento los modifican o eliminan por error.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 51aa58bf3f3c0d672d90b70301f84306e09e06e9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585711"
---
# <a name="soft-delete-for-containers"></a>Eliminación temporal para contenedores

La eliminación temporal para contenedores impide que los datos se eliminen accidentalmente, ya que conserva los datos eliminados en el sistema durante un período de tiempo especificado. Durante el período de retención, puede restaurar un contenedor eliminado temporalmente y su contenido a su estado en el momento en que se eliminaron. Una vez que expire el período de retención, el contenedor y su contenido se eliminarán permanentemente.

## <a name="recommended-data-protection-configuration"></a>Configuración de protección de datos recomendada

La eliminación temporal de blobs forma parte de una exhaustiva estrategia para proteger los datos de los blobs. Para una protección óptima de los datos de blobs, Microsoft recomienda habilitar las siguientes características de protección de datos:

- Eliminación temporal de contenedores, para restaurar un contenedor que se ha eliminado. Para obtener información sobre cómo habilitar la eliminación temporal de contenedores, consulte [Habilitación y administración de la eliminación temporal de contenedores](soft-delete-container-enable.md).
- Control de versiones de blobs, para conservar automáticamente las versiones anteriores de un blob. Cuando el control de versiones de blobs está habilitado, puede restaurar una versión anterior de un blob para recuperar los datos si se modifican o eliminan por error. Para obtener información sobre cómo habilitar el control de versiones de blobs, consulte [Habilitación y administración del control de versiones de blobs](versioning-enable.md).
- Eliminación temporal de blobs, para restaurar un blob, una instantánea o una versión que se ha eliminado. Para obtener información sobre cómo habilitar la eliminación temporal de blobs, consulte [Habilitación y administración de la eliminación temporal para blobs](soft-delete-blob-enable.md).

Para obtener más información sobre las recomendaciones de Microsoft con respecto a la protección de datos, consulte [Información general sobre la protección de datos](data-protection-overview.md).

## <a name="how-container-soft-delete-works"></a>Funcionamiento de la eliminación temporal de contenedores

Al habilitar la eliminación temporal de contenedores, puede especificar un período de retención de 1 a 365 días para los contenedores eliminados. El período de retención predeterminado es de 7 días. Durante el período de retención, puede recuperar un contenedor eliminado mediante una llamada a la operación **Restore Container** (Restaurar contenedor).

Al restaurar un contenedor, también se restauran los blobs del contenedor, así como las versiones de blob y las instantáneas. Sin embargo, solo puede usar la eliminación temporal de contenedores para restaurar blobs si se eliminó el propio contenedor. Para restaurar un blob eliminado cuando su contenedor principal no se ha eliminado, debe usar la eliminación temporal de blobs o el control de versiones de blobs.

> [!WARNING]
> La eliminación temporal de contenedores solo puede restaurar contenedores completos y su contenido en el momento de la eliminación. No se puede restaurar un blob eliminado dentro de un contenedor mediante la eliminación temporal del contenedor. Microsoft también recomienda habilitar la eliminación temporal y el control de versiones de blobs para proteger blobs individuales en un contenedor.
>
> Cuando restaure un contenedor, deberá restaurarlo con su nombre original. Si el nombre original se ha usado para crear otro contenedor, no podrá restaurar el contenedor eliminado temporalmente.

En el diagrama siguiente se muestra cómo se puede restaurar un contenedor eliminado cuando está habilitada la eliminación temporal de contenedores:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagrama que muestra cómo se puede restaurar un contenedor eliminado temporalmente":::

Una vez que haya expirado el período de retención, el contenedor se eliminará de manera permanente de Azure Storage y no se podrá recuperar. El reloj del período de retención comienza en el momento en que se elimina el contenedor. Puede cambiar el período de retención en cualquier momento, pero tenga en cuenta que un período de retención actualizado se aplica solo a los contenedores recién eliminados. Los contenedores previamente eliminados se eliminarán de forma permanente según el período de retención que estaba en vigor en el momento en que se eliminó el contenedor.

La deshabilitación de la eliminación temporal de contenedores no provoca la eliminación permanente de los contenedores que se eliminaron temporalmente. Los contenedores que ya haya eliminado temporalmente se eliminarán de forma permanente en el momento de expiración del período de retención que estaba en vigor en el momento en que se eliminó el contenedor.

La eliminación temporal de contenedores está disponible para los siguientes tipos de cuentas de almacenamiento:

- Cuentas de almacenamiento de uso general v2 y v1
- Cuentas de almacenamiento de blob en bloques
- Cuentas de Almacenamiento de blobs

También se admiten las cuentas de almacenamiento con un espacio de nombres jerárquico habilitado para usarse con Azure Data Lake Storage Gen2.

La versión 2019-12-12 o posteriores de la API REST de Azure Storage admiten la eliminación temporal de contenedores.

> [!IMPORTANT]
> La eliminación temporal de contenedores no protege contra la eliminación de una cuenta de almacenamiento, sino solo contra la eliminación de los contenedores de esa cuenta. Para proteger una cuenta de almacenamiento de la eliminación, configure un bloqueo en el recurso de la cuenta de almacenamiento. Para obtener más información sobre el bloqueo de recursos de Azure Resource Manager, consulte [Bloqueo de recursos para impedir cambios inesperados](../../azure-resource-manager/management/lock-resources.md).

## <a name="feature-support"></a>Compatibilidad de características

En esta tabla se muestra cómo se admite esta característica en la cuenta y el impacto en la compatibilidad al habilitar determinadas funcionalidades.

| Tipo de cuenta de almacenamiento                | Blob Storage (compatibilidad predeterminada)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| De uso general estándar, v2 | ![Sí](../media/icons/yes-icon.png) |![Sí](../media/icons/yes-icon.png)              | ![No](../media/icons/no-icon.png) |
| Blobs en bloques Premium          | ![Sí](../media/icons/yes-icon.png) |![Sí](../media/icons/yes-icon.png)              | ![No](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2 y el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

## <a name="pricing-and-billing"></a>Precios y facturación

No hay ningún cargo adicional para habilitar la eliminación temporal de contenedores. Los datos en contenedores eliminados temporalmente se facturan a la misma tasa que los datos activos.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la eliminación temporal de contenedores](soft-delete-container-enable.md)
- [Eliminación temporal para blobs](soft-delete-blob-overview.md)
- [Control de versiones de blobs](versioning-overview.md)
