---
title: Compatibilidad con la característica Blob Storage en cuentas de Azure Storage
description: Determine el nivel de compatibilidad para cada característica de cuenta de almacenamiento según el tipo de cuenta de almacenamiento y la configuración habilitada en la cuenta.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.openlocfilehash: e22e7dcfde1782600cdb53814014e6080ffbbfa1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271638"
---
# <a name="blob-storage-feature-support-in-azure-storage-accounts"></a>Compatibilidad con la característica Blob Storage en cuentas de Azure Storage

En este artículo se muestra si una característica es totalmente compatible (disponible con carácter general), si se admite en el nivel de versión preliminar o si todavía no se admite. Los niveles de compatibilidad se verán afectados por el tipo de cuenta de almacenamiento y si ciertas funcionalidades o protocolos están habilitados en la cuenta.

Los elementos que aparecen en estas tablas van a cambiar con el tiempo a medida que se siga ampliando la compatibilidad.

## <a name="standard-general-purpose-v2-accounts"></a>Cuentas estándar de uso general v2

| Característica de Azure Storage | Blob Storage (compatibilidad predeterminada) | Data Lake Storage Gen2 <sup>1</sup>   | NFS 3.0 <sup>1</sup>  |
|---------------|-------------------|---|---|
| [Nivel de acceso: archivo](access-tiers-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Nivel de acceso: esporádico](access-tiers-overview.md)    | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)|
| [Nivel de acceso: frecuente](access-tiers-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Acceso anónimo (público)](anonymous-read-access-configure.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)|
| [Seguridad de Azure Active Directory](authorize-access-azure-active-directory.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| [Inventario de blobs](blob-inventory.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Etiquetas de índice de blobs](storage-manage-find-blobs.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Instantáneas de blob](snapshots-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![No](../media/icons/no-icon.png) |
| [API de Blob Storage](reference.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>3</sup> | ![Sí](../media/icons/yes-icon.png) <sup>3</sup>|
| [Comandos de la CLI de Azure de Blob Storage](storage-quickstart-blobs-cli.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Eventos de Blob Storage](storage-blob-event-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>3</sup>  | ![No](../media/icons/no-icon.png) |
| [Comandos de PowerShell de Blob Storage](storage-quickstart-blobs-powershell.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Control de versiones de blobs](versioning-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Fuente de cambios](storage-blob-change-feed.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Dominios personalizados](storage-custom-domain-name.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Conmutación por error de cuenta administrada por el cliente](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Claves administradas por el cliente (cifrado)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Claves proporcionadas por el cliente (cifrado)](encryption-customer-provided-keys.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Opciones de redundancia de datos](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Ámbitos de cifrado](encryption-scope-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Almacenamiento inmutable](immutable-storage-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Seguimiento de la hora del último acceso para la administración del ciclo de vida](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| [Directivas de administración del ciclo de vida (eliminar blob)](./lifecycle-management-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Directivas de administración del ciclo de vida (niveles)](./lifecycle-management-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Registro en Azure Monitor](./monitor-blob-storage.md) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Métricas en Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Replicación de objetos para blobs en bloques](object-replication-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Blobs en páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![Sí](../media/icons/yes-icon.png) |![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Restauración a un momento dado para blobs en bloques](point-in-time-restore-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Eliminación temporal para blobs](./soft-delete-blob-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![No](../media/icons/no-icon.png) |
| [Eliminación temporal de contenedores](soft-delete-container-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| [Sitios web estáticos](storage-blob-static-website.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Registros de Storage Analytics (clásico)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>3</sup> | ![No](../media/icons/no-icon.png) |
| [Métricas de Storage Analytics (clásico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |

<sup>1</sup> Data Lake Storage Gen2 y el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

<sup>2</sup> La característica se admite en el nivel de versión preliminar.

<sup>3</sup> Consulte [Problemas conocidos con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md). Estos problemas se aplican a todas las cuentas que tienen habilitada la característica de espacio de nombres jerárquico.

## <a name="premium-block-blob-accounts"></a>Cuentas de blob en bloques Premium

| Característica de Azure Storage | Blob Storage (compatibilidad predeterminada) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|---------------|-------------------|---|---|
| [Nivel de acceso: archivo](access-tiers-overview.md)  | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Nivel de acceso: esporádico](access-tiers-overview.md) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Nivel de acceso: frecuente](access-tiers-overview.md) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Acceso anónimo (público)](anonymous-read-access-configure.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Seguridad de Azure Active Directory](authorize-access-azure-active-directory.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| [Inventario de blobs](blob-inventory.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Etiquetas de índice de blobs](storage-manage-find-blobs.md) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Instantáneas de blob](snapshots-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![No](../media/icons/no-icon.png) |
| [API de Blob Storage](reference.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>3</sup> | ![Sí](../media/icons/yes-icon.png) <sup>3</sup>|
| [Comandos de la CLI de Azure de Blob Storage](storage-quickstart-blobs-cli.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Eventos de Blob Storage](storage-blob-event-overview.md) | ![Sí](../media/icons/yes-icon.png)  <sup>3</sup>  | ![Sí](../media/icons/yes-icon.png) <sup>3</sup>| ![No](../media/icons/no-icon.png) |
| [Comandos de PowerShell de Blob Storage](storage-quickstart-blobs-powershell.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Control de versiones de blobs](versioning-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Fuente de cambios](storage-blob-change-feed.md) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Dominios personalizados](storage-custom-domain-name.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Conmutación por error de cuenta administrada por el cliente](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Claves administradas por el cliente (cifrado)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)  | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Claves proporcionadas por el cliente (cifrado)](encryption-customer-provided-keys.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Opciones de redundancia de datos](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Ámbitos de cifrado](encryption-scope-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Almacenamiento inmutable](immutable-storage-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Seguimiento de la hora del último acceso para la administración del ciclo de vida](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| [Directivas de administración del ciclo de vida (eliminar blob)](./lifecycle-management-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Directivas de administración del ciclo de vida (niveles)](./lifecycle-management-overview.md) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Registro en Azure Monitor](./monitor-blob-storage.md) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Métricas en Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Replicación de objetos para blobs en bloques](object-replication-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Blobs en páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Restauración a un momento dado para blobs en bloques](point-in-time-restore-overview.md) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| [Eliminación temporal para blobs](./soft-delete-blob-overview.md)   | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![No](../media/icons/no-icon.png) |
| [Eliminación temporal de contenedores](soft-delete-container-overview.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| [Sitios web estáticos](storage-blob-static-website.md) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| [Registros de Storage Analytics (clásico)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup>  <sup>3</sup> | ![No](../media/icons/no-icon.png)|
| [Métricas de Storage Analytics (clásico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |

<sup>1</sup> Data Lake Storage Gen2 y el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

<sup>2</sup> La característica se admite en el nivel de versión preliminar.

<sup>3</sup> Consulte [Problemas conocidos con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md). Estos problemas se aplican a todas las cuentas que tienen habilitada la característica de espacio de nombres jerárquico.

## <a name="see-also"></a>Consulte también

- [Problemas conocidos con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)

- [Problemas conocidos con el protocolo Network File System (NFS) 3.0 en Azure Blob Storage](network-file-system-protocol-known-issues.md)
