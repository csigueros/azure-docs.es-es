---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/10/2021
ms.author: alkohli
ms.openlocfilehash: d02eb129dd66234cf7c8522ca8204fb0026da092
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353848"
---
- Contenedores, recursos compartidos y carpetas:
  - No copie los archivos directamente en ninguno de los recursos compartidos creados previamente. Debe crear una carpeta en el recurso compartido y, después, copiar los archivos en ella.
  - Una carpeta en *StorageAccount_BlockBlob* y *StorageAccount_PageBlob* es un contenedor. Por ejemplo, los contenedores se crean como *StorageAccount_BlockBlob/container* y *StorageAccount_PageBlob/container*.
  - Cada carpeta creada directamente en *StorageAccount_AzFile* se convierte en un recurso compartido de archivos de Azure.
  - Azure Blob Storage no admite directorios. Si crea una carpeta en la carpeta *StorageAccount_BlockBlob* y, después, se crean carpetas virtuales en el nombre del blob. Para Azure Files, se mantiene la estructura de directorios real.
- Combinación del contenido de la carpeta:
  - Todos los archivos escritos en los recursos compartidos *StorageAccount_BlockBlob* y *StorageAccount_BlockBlob* se cargan como blob en bloques y blob en páginas, respectivamente.
  - Si una carpeta tiene el mismo nombre que un contenedor existente, el contenido de la carpeta se combina con el del contenedor. Los archivos o blobs que aún no están en la nube se agregan al contenedor. Si un archivo o blob tiene el mismo nombre que un archivo o blob que ya está en el contenedor, se sobrescribe el archivo o blob existente.
  - Todas las jerarquías de directorios vacías (sin archivos) creadas en las carpetas *StorageAccount_BlockBlob* y *StorageAccount_BlockBlob* no se cargan.
- Si usa los protocolos SMB y NFS para las copias de datos, se recomienda que:
  - Use diferentes cuentas de almacenamiento para SMB y NFS.
  - No copie los mismos datos en el mismo destino final de Azure mediante SMB y NFS. En estos casos, no se puede determinar el resultado final.
  - Aunque la copia a través de SMB y NFS en paralelo puede funcionar, no se recomienda hacerlo, ya que esto es propenso a errores humanos. Espere hasta que se complete la copia de datos SMB antes de iniciar una copia de datos NFS.
- Administración de carga: 
  - Para mejorar el rendimiento durante las cargas de datos, se recomienda [habilitar recursos compartidos de archivos grandes en la cuenta de almacenamiento y aumentar la capacidad del recurso compartido a 100 TiB](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Los recursos compartidos de archivos grandes solo se admiten para las cuentas de almacenamiento con almacenamiento con redundancia local (LRS).
  - Si se han producido errores al cargar datos en Azure, se crea un registro de errores en la cuenta de almacenamiento de destino. La ruta de acceso a este registro de errores está disponible cuando se completa la carga. Puede examinar el registro para realizar acciones correctivas. No elimine datos del origen sin comprobar los datos cargados.
  - Los metadatos de archivo y los permisos NTFS se pueden conservar cuando se cargan los datos en Azure Files mediante las instrucciones de [Conservación de las ACL de archivo, los atributos y las marcas de tiempo con Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).
  - La jerarquía de los archivos se mantiene mientras se carga en la nube tanto para blobs como para Azure Files. Por ejemplo, copió un archivo en esta ruta de acceso: `<container folder>\A\B\C.txt`. Este archivo se carga en la misma ruta de acceso en la nube.
  
