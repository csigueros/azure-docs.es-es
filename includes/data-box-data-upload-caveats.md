---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/28/2021
ms.author: alkohli
ms.openlocfilehash: 3a0a63d68c5a63eeb4ac7855b3541471814a734b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220680"
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
- Administración de carga: 
  - Para mejorar el rendimiento durante las cargas de datos, se recomienda [habilitar recursos compartidos de archivos grandes en la cuenta de almacenamiento y aumentar la capacidad del recurso compartido a 100 TiB](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Los recursos compartidos de archivos grandes solo se admiten para las cuentas de almacenamiento con almacenamiento con redundancia local (LRS).
  - Si se han producido errores al cargar datos en Azure, se crea un registro de errores en la cuenta de almacenamiento de destino. La ruta de acceso a este registro de errores está disponible cuando se completa la carga. Puede examinar el registro para realizar acciones correctivas. No elimine datos del origen sin comprobar los datos cargados.
  - Los metadatos de archivo y los permisos NTFS se pueden conservar cuando se cargan los datos en Azure Files mediante las instrucciones de [Conservación de las ACL de archivo, los atributos y las marcas de tiempo con Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).
  - La jerarquía de los archivos se mantiene mientras se carga en la nube tanto para blobs como para Azure Files. Por ejemplo, copió un archivo en esta ruta de acceso: `<container folder>\A\B\C.txt`. Este archivo se carga en la misma ruta de acceso en la nube.
  
