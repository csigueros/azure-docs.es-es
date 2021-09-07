---
title: Características de Blob Storage disponibles en Azure Data Lake Storage Gen2 | Microsoft Docs
description: Más información acerca de las características de Blob Storage que puede usar con Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: normesta
ms.openlocfilehash: 234f3ae60aaf389b347ce8378c889cdeb61e0738
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729724"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Características de Blob Storage disponibles en Azure Data Lake Storage Gen2

Las características de Blob Storage, como el [registro de diagnóstico](../common/storage-analytics-logging.md), los [niveles de acceso](storage-blob-storage-tiers.md) y las [directivas de administración del ciclo de vida de Blob Storage](storage-lifecycle-management-concepts.md), ahora funcionan con las cuentas que tienen un espacio de nombres jerárquico. Por tanto, puede habilitar los espacios de nombres jerárquicos en las cuentas de Blob Storage sin perder el acceso a estas características.

En esta tabla se enumeran las características de Blob Storage que puede usar con Azure Data Lake Storage Gen2. Los elementos que aparecen en estas tablas van a cambiar con el tiempo a medida que se siga ampliando la compatibilidad. Para obtener más información sobre problemas específicos asociados con el estado de versión preliminar de una característica, consulte el artículo [problemas conocidos](data-lake-storage-known-issues.md).

## <a name="supported-blob-storage-features"></a>Características de Blob Storage compatibles

En la tabla siguiente se muestra la compatibilidad de cada característica de Blob Storage con Data Lake Storage Gen2. Hay una columna para los niveles de [rendimiento prémium](premium-tier-for-data-lake-storage.md) y estándar. 

|Característica de Blob Storage |Estándar |Premium |Artículos relacionados |
|---------------|-------------------|---|
|Nivel de acceso frecuente|Disponibilidad general|No compatible|[Azure Blob Storage: niveles de acceso frecuente, esporádico y de archivo](storage-blob-storage-tiers.md)|
|Nivel de acceso esporádico|Disponibilidad general|No compatible|[Azure Blob Storage: niveles de acceso frecuente, esporádico y de archivo](storage-blob-storage-tiers.md)|
|Eventos|Disponibilidad general|Disponibilidad general|[Reacción a eventos de Blob Storage](storage-blob-event-overview.md)|
|Métricas (clásica)|Disponibilidad general|Disponibilidad general|[Métricas de Azure Storage Analytics (clásicas)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas en Azure Monitor|Disponibilidad general|Vista previa|[Métricas de Azure Storage en Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos de PowerShell de Blob Storage|Disponibilidad general|Disponibilidad general|[Inicio rápido: Carga, descarga y enumeración de blobs mediante PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandos de la CLI de Azure de Blob Storage|Disponibilidad general|Disponibilidad general|[Inicio rápido: Creación, descarga y enumeración de blobs mediante la CLI de Azure](storage-quickstart-blobs-cli.md)|
|API de Blob Storage|Disponibilidad general|Disponibilidad general|[Inicio rápido: Biblioteca cliente de Azure Blob Storage v12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Inicio rápido: Administración de blobs con el SDK de Java v12](storage-quickstart-blobs-java.md)<br>[Inicio rápido: Administración de blobs con el SDK de Python v12](storage-quickstart-blobs-python.md)<br>[Inicio rápido: Administración de blobs con el SDK de JavaScript v12 en Node.js](storage-quickstart-blobs-nodejs.md)|
|Claves administradas por el cliente|Disponibilidad general|Disponibilidad general|[Claves administradas por el cliente para el cifrado de Azure Storage](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)|
|Registros de diagnóstico|Disponibilidad general|Vista previa |[Registro de Azure Storage Analytics](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Nivel de acceso de archivo|Disponibilidad general|No compatible|[Azure Blob Storage: niveles de acceso frecuente, esporádico y de archivo](storage-blob-storage-tiers.md)|
|Directivas de administración del ciclo de vida (niveles)|Disponibilidad general|Todavía no se admite|[Administración del ciclo de vida de Azure Blob Storage](storage-lifecycle-management-concepts.md)|
|Directivas de administración del ciclo de vida (eliminar blob)|Disponibilidad general|Disponibilidad general|[Administración del ciclo de vida de Azure Blob Storage](storage-lifecycle-management-concepts.md)|
|Eliminación temporal de contenedores|Disponibilidad general|Disponibilidad general|[Eliminación temporal de contenedores](soft-delete-container-overview.md)|
|Registro en Azure Monitor|Vista previa |Vista previa|[Supervisión de Azure Storage](./monitor-blob-storage.md)|
|Instantáneas|Versión preliminar|Vista previa|[Instantáneas de blob](snapshots-overview.md)|
|Sitios web estáticos|Disponibilidad general<div role="complementary" aria-labelledby="preview-form"></div>|Disponibilidad general<div role="complementary" aria-labelledby="preview-form"></div>|[Hospedaje de sitios web estáticos en Azure Storage](storage-blob-static-website.md)|
|Almacenamiento inmutable|Versión preliminar<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Versión preliminar<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Almacenamiento inmutable de los datos críticos para la empresa en Azure Blob Storage](immutable-storage-overview.md)|
|Inventario de Azure Storage|Versión preliminar|Versión preliminar|[Uso del inventario de Azure Storage para administrar datos de blobs (versión preliminar)](blob-inventory.md)|
|Dominios personalizados|Versión preliminar<div role="complementary" aria-labelledby="preview-form-1"><sup>1</sup></div>|Versión preliminar<div role="complementary" aria-labelledby="preview-form-1"><sup>1</sup></div>|[Asignación de un dominio personalizado a un punto de conexión de Azure Blob Storage](storage-custom-domain-name.md)|
|Eliminación temporal de blobs|Vista previa|Vista previa|[Eliminación temporal para blobs](./soft-delete-blob-overview.md)|
|Blobfuse|Disponibilidad general|Disponibilidad general|[Cómo montar el almacenamiento de blobs como sistema de archivos con blobfuse](storage-how-to-mount-container-linux.md)|
|Acceso anónimo (público) |Disponibilidad general|Disponibilidad general| Consulte [Configuración de acceso de lectura público anónimo a contenedores y blobs](anonymous-read-access-configure.md).|
|Conmutación por error de cuenta administrada por el cliente|Todavía no se admite|Todavía no se admite|[Recuperación ante desastres y conmutación por error de la cuenta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Claves proporcionadas por el cliente|Todavía no se admite|Todavía no se admite|[Especificación de una clave de cifrado en una solicitud a Blob Storage](encryption-customer-provided-keys.md)|
|Ámbitos de cifrado|Todavía no se admite|Todavía no se admite|[Creación y administración de ámbitos de cifrado](encryption-scope-manage.md)|
|Fuente de cambios|Todavía no se admite|Todavía no se admite|[Compatibilidad con la fuente de cambios en Azure Blob Storage](storage-blob-change-feed.md)|
|Replicación de objetos|Todavía no se admite|Todavía no se admite|[Configuración de la replicación de objetos para blobs en bloques](object-replication-configure.md)|
|Control de versiones de blobs|Todavía no se admite|Todavía no se admite|[Habilitar y administrar las versiones de blob](versioning-enable.md)|
|Restauración a un momento dado|Todavía no se admite|Todavía no se admite|[Restauración a un momento dado para blobs en bloques](point-in-time-restore-overview.md)|
|Etiquetas de índice de blobs|Todavía no se admite|Todavía no se admite|[Administración y búsqueda de datos de Azure Blob con etiquetas de índice de blobs](storage-manage-find-blobs.md)|

<div id="preview-form-2"><sup>1</sup>Un nombre de dominio personalizado solo se puede asignar al punto de conexión de Blob service o del sitio web estático. No se admite el punto de conexión de almacenamiento de Data Lake </a>.  </div>

## <a name="see-also"></a>Consulte también

- [Problemas conocidos con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Servicios de Azure que admiten Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de código abierto que admiten Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acceso multiprotocolo en Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
