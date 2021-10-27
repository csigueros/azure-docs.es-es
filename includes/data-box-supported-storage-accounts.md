---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 8c823b6709dbd5010ec48c2fbca185d9a211cb40
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000107"
---
Aquí se proporciona una lista de los tipos de cuentas de almacenamiento y almacenamientos compatibles con un dispositivo Data Box. Para una lista completa de todas las funcionalidades para todos los tipos de cuentas de almacenamiento, consulte [Tipos de cuentas de almacenamiento](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

#### <a name="supported-storage-accounts-for-imports"></a>Cuentas de almacenamiento admitidas para importaciones

En el caso de los pedidos de importación, en la siguiente tabla se muestran las cuentas de almacenamiento admitidas.

| **Tipos de cuenta de almacenamiento/almacenamiento compatible** | **Blob en bloques** |**Blob en páginas**<sup>1</sup> |**Azure Files** |**Niveles de acceso admitidos**|
| --- | --- | -- | -- | -- |
| Estándar clásico | Y | Y | Y |
| Estándar de uso general v1  | Y | Y | Y | Acceso frecuente y esporádico |
| Premium de uso general v1  |  | Y| | |
| Estándar de uso general v2<sup>2</sup>  | Y | Y | Y | Acceso frecuente y esporádico|
| Premium de uso general v2  |  |Y | |  |
| FileStorage Premium de Azure |  |  | Y |  |  
| Estándar de Blob Storage | Y | | | Acceso frecuente y esporádico |


<sup>1</sup> *Los datos cargados en blobs en páginas deben tener 512 bytes alineados como discos duros virtuales.*

<sup>2</sup> *Azure Data Lake Storage Gen2 (ADLS Gen2) se admite para importaciones, pero no para exportaciones.*


#### <a name="supported-storage-accounts-for-exports"></a>Cuentas de almacenamiento admitidas para exportaciones

En el caso de los pedidos de exportación, en la siguiente tabla se muestran las cuentas de almacenamiento admitidas.

| **Tipos de cuenta de almacenamiento/almacenamiento compatible** | **Blob en bloques** |**Blob en páginas** _ |_ *Archivos de Azure** |**Niveles de acceso admitidos**|
| --- | --- | -- | -- | -- |
| Estándar clásico | Y | Y | Y | |
| Estándar de uso general v1  | Y | Y | Y | Acceso frecuente y esporádico |
| Premium de uso general v1  |  | Y| | |
| Estándar de uso general v2  | Y | Y | Y | Acceso frecuente y esporádico |
| Premium de uso general v2  |  |Y | | |
| FileStorage Premium de Azure |  |  | Y |  |
| Estándar de Blob Storage |Y | | | Acceso frecuente y esporádico |
| Almacenamiento prémium de blobs en bloques |Y | | | Acceso frecuente y esporádico |
| Almacenamiento prémium de blobs en páginas | |Y | | |

#### <a name="caveats-for-storage-accounts"></a>Advertencias para las cuentas de almacenamiento

- Para las cuentas de uso general:
  - En el caso de los pedidos de importación, Data Box no admite los tipos de almacenamiento Queue, Table y Disk.
  - En el caso de los pedidos de exportación, Data Box no admite los tipos de almacenamiento Queue, Table, Disk y Azure Data Lake Gen2.
- Data Box no admite blobs en anexos en las cuentas de Blob Storage y de almacenamiento de blobs en bloque.
- La compatibilidad con el protocolo Network File System (NFS) 3.0 en Azure Blob Storage no se admite con Data Box.
- Los datos cargados en blobs en páginas deben tener 512 bytes alineados como discos duros virtuales.
- Para exportaciones:
  - Se pueden exportar hasta 80 TB.
  - El historial de archivos y las instantáneas de blobs no se exportan.
  - Los blobs del nivel de acceso de archivo no se admiten para la exportación. Rehidrate los blobs del nivel de acceso de archivo antes de exportarlos. Para más información, consulte [Rehidratación de un blob de archivo a un nivel en línea](../articles/storage/blobs/archive-rehydrate-overview.md).