---
title: Problemas conocidos de NFS 3.0 en Azure Blob Storage | Microsoft Docs
description: Obtenga información sobre las limitaciones y los problemas conocidos de la compatibilidad con el protocolo Network File System (NFS) 3.0 en Azure Blob Storage.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 8af0b4a5c26d70f9fdedc30d07c8953bfee76fb4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727137"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Problemas conocidos con el protocolo Network File System (NFS) 3.0 en Azure Blob Storage

Este artículo describe las limitaciones y los problemas conocidos de la compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage.

## <a name="nfs-30-support"></a>Compatibilidad con NFS 3.0

- La compatibilidad con NFS 3.0 no se puede habilitar en las cuentas de almacenamiento existentes.

- La compatibilidad con NFS 3.0 no se puede deshabilitar en una cuenta de almacenamiento después de haberla habilitado.

## <a name="nfs-30-features"></a>Características de NFS 3.0

Todavía no se admiten las siguientes características de NFS 3.0.

- NFS 3.0 a través de UDP. Solo se admite NFS 3.0 a través de TCP.

- Bloqueo de archivos con Network Lock Manager (NLM). El montaje de comandos debe incluir el parámetro `-o nolock`.

- Montaje de subdirectorios. Solo puede montar el directorio raíz (contenedor).

- Enumeración de los montajes (por ejemplo: mediante el comando `showmount -a`).

- Enumeración de las exportaciones (por ejemplo: mediante el comando `showmount -e`).

- Vínculo físico

- Exportación de un contenedor como de solo lectura.

## <a name="nfs-30-clients"></a>Clientes de NFS 3.0

El cliente Windows para NFS aún no es compatible

## <a name="blob-storage-features"></a>Característica de Blob Storage

Las siguientes características de Blob Storage aún no se admiten.

- [Seguridad de Azure Active Directory (AD).](../common/authorize-data-access.md?toc=/azure/storage/blobs/toc.json)
- [Integración de Azure Backup](../../backup/blob-backup-overview.md)
- [Registro de Azure Storage Analytics](../common/storage-analytics-logging.md?toc=/azure/storage/blobs/toc.json)
- [Etiquetas de índice de blobs](storage-blob-index-how-to.md)
- [Eventos de Blob Storage](storage-blob-event-overview.md)
- [Control de versiones de blobs](versioning-enable.md)
- [Fuente de cambios](storage-blob-change-feed.md)
- [Conmutación por error de cuenta administrada por el cliente](../common/storage-disaster-recovery-guidance.md?toc=/azure/storage/blobs/toc.json)
- [Claves proporcionadas por el cliente para el cifrado de Azure Storage](encryption-customer-provided-keys.md) 
- [Ámbitos de cifrado](encryption-scope-overview.md)
- [Seguimiento de la hora del último acceso para la administración del ciclo de vida](storage-lifecycle-management-concepts.md#move-data-based-on-last-accessed-date-preview)
- [Replicación de objetos para blobs en bloques](object-replication-overview.md)
- [Blobs en páginas](storage-blobs-introduction.md#blobs)
- [Restauración a un momento dado para blobs en bloques](point-in-time-restore-overview.md)
- [Eliminación temporal para blobs](soft-delete-blob-overview.md)
- [Eliminación temporal de contenedores](soft-delete-container-overview.md)
- [Instantáneas de blob](snapshots-overview.md)
- [Hospedaje de sitios web estáticos](storage-blob-static-website.md)

## <a name="blob-storage-apis"></a>API de Blob Storage

NFS 3.0, las API de Blob y Data Lake Storage Gen2 API pueden operar en los mismos datos. 

En esta sección se describen los problemas y las limitaciones del uso de NFS 3.0, las API de Blob y de las API de Data Lake Storage Gen2 para operar en los mismos datos. 

- No puede usar NFS 3.0, la API de blob o las API de Data Lake Storage para escribir en la misma instancia de un archivo. Si escribe en un archivo con NFS, los bloques de ese archivo no estarán visibles para las llamadas a la API de blob  [Get Block List](/rest/api/storageservices/get-block-list) . La única excepción es cuando se usa para sobrescribir algún elemento. Puede sobrescribir un archivo o blob mediante cualquier API. También se puede sobrescribir con NFS 3.0 mediante la opción de truncamiento cero.

- Cuando use la operación [List Blobs](/rest/api/storageservices/list-blobs) (Enumerar blobs) sin especificar un delimitador, los resultados incluirán tanto los directorios como los blobs. Si decide usar un delimitador, use solo una barra diagonal (`/`). Este es el único delimitador admitido.

- Si usa la API [Delete Blob](/rest/api/storageservices/delete-blob) para eliminar un directorio, solo se eliminará si está vacío. Esto significa que no puede utilizar la API de blob para eliminar los directorios de forma recursiva.

Las siguientes API REST de blob no se admiten:

* [Put Blob (Page)](/rest/api/storageservices/put-blob) [Poner blob (página)]
* [Put Page](/rest/api/storageservices/put-page)
* [Get Page Ranges](/rest/api/storageservices/get-page-ranges) (Conseguir intervalos de páginas)
* [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob) (Copia incremental del blob)
* [Put Page from URL](/rest/api/storageservices/put-page-from-url) (Poner página de dirección URL)
* [Put Block List](/rest/api/storageservices/put-block-list)

No se admiten discos de máquina virtual no administrados en cuentas que tengan un espacio de nombres jerárquico. Si desea habilitar un espacio de nombres jerárquico en una cuenta de almacenamiento, coloque los discos de máquina virtual no administrados en una cuenta de almacenamiento que no tenga habilitada la característica de espacio de nombres jerárquico. 

## <a name="see-also"></a>Vea también

- [Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage](network-file-system-protocol-support.md)
- [Montaje de Blob Storage con el protocolo Network File System (NFS) 3.0](network-file-system-protocol-support-how-to.md)