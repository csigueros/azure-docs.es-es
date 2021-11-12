---
title: Problemas conocidos de NFS 3.0 en Azure Blob Storage
description: Obtenga información sobre las limitaciones y los problemas conocidos de la compatibilidad con el protocolo Network File System (NFS) 3.0 en Azure Blob Storage.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 13808d38a4ddb7e00af64b02df390eee1e1f9469
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421976"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Problemas conocidos con el protocolo Network File System (NFS) 3.0 en Azure Blob Storage

Este artículo describe las limitaciones y los problemas conocidos de la compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage.

> [!IMPORTANT]
> Dado que debe habilitar la característica de espacio de nombres jerárquico de la cuenta para usar NFS 3.0, todos los problemas conocidos que se describen en el artículo [Problemas conocidos con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md) también se aplican a la cuenta.

## <a name="nfs-30-support"></a>Compatibilidad con NFS 3.0

- La compatibilidad con NFS 3.0 no se puede habilitar en las cuentas de almacenamiento existentes.

- La compatibilidad con NFS 3.0 no se puede deshabilitar en una cuenta de almacenamiento después de haberla habilitado.

- Las opciones de redundancia GRS, GZRS y RA-GRS no se admiten al crear una cuenta de almacenamiento de NFS 3.0.

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

Al habilitar la compatibilidad con el protocolo NFS 3.0, algunas características de Blob Storage serán totalmente compatibles, pero es posible que otras solo se admitan en el nivel de versión preliminar o que todavía no se admitan en absoluto.

Para ver cómo se admite cada característica de Blob Storage en cuentas que tienen habilitada la compatibilidad con NFS 3.0, vea [Compatibilidad con características de Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md).

## <a name="see-also"></a>Vea también

- [Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage](network-file-system-protocol-support.md)
- [Montaje de Blob Storage con el protocolo Network File System (NFS) 3.0](network-file-system-protocol-support-how-to.md)
