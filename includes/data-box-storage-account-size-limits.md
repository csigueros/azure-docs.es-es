---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 1f026d0a8d038e532b9b0ec070a4849ebc8e8530
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803030"
---
Estos son los límites del tamaño de los datos que se copian en una cuenta de almacenamiento. Asegúrese de que los datos que carga se ajustan a estos límites. Para obtener la información más actualizada sobre estos límites, consulte [Objetivos de rendimiento y escalabilidad de Blob Storage](../articles/storage/blobs/scalability-targets.md) y [Objetivos de rendimiento y escalabilidad de Azure Files](../articles/storage/files/storage-files-scale-targets.md). 

| Tamaño de los datos que se copian en la cuenta de almacenamiento de Azure                      | Límite predeterminado          |
|---------------------------------------------------------------------|------------------------|
| Blob en bloques y blob en páginas                                            | El límite máximo es el mismo que el [límite de almacenamiento definido para la suscripción de Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) e incluye datos de todos los orígenes, incluido Data Box.   |
| Azure Files                                                          | <ul><li>Data Box admite recursos compartidos de archivos de gran tamaño (100 TiB) si se habilita antes de la creación del pedido de Data Box.<ul><li>Si no se habilitan los recursos compartidos de archivos grandes antes de la creación del pedido, se admitirá un tamaño máximo de recurso compartido de archivos de 5 TiB.</li><li>Para mejorar el rendimiento durante las cargas de datos, se recomienda [habilitar recursos compartidos de archivos grandes en la cuenta de almacenamiento y aumentar la capacidad del recurso compartido a 100 TiB](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Los recursos compartidos de archivos grandes solo se admiten para las cuentas de almacenamiento con almacenamiento con redundancia local (LRS).</li></ul><li>Data Box admite recursos compartidos de archivos Premium de Azure, que permiten un total de 100 TiB para todos los recursos compartidos de la cuenta de almacenamiento.<ul><li>La capacidad máxima utilizable es ligeramente inferior debido al espacio que usan los registros de copia y de auditoría. Se reserva un mínimo de 100 GiB para cada uno de ellos. Para obtener más información, consulte [Registros de auditoría para Azure Data Box y Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md).</li><li>Todas las carpetas de *StorageAccount_AzFile* deben seguir este límite. Para más información, consulte [Creación de un recurso compartido de archivos de Azure](../articles/storage/files/storage-how-to-create-file-share.md).</li></ul></li></ul> |
