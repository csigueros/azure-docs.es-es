---
title: Solución de problemas en Azure Data Box y Azure Data Box Heavy
description: Describe cómo solucionar problemas que se producen en Azure Data Box y Azure Data Box Heavy al copiar datos en estos dispositivos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 07/14/2021
ms.author: alkohli
ms.openlocfilehash: 06f0408587cc7c5533bdb852f7ad4f59094a33da
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220265"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Solucionar problemas relacionados con Azure Data Box y Azure Data Box Heavy

En este artículo se detalla información sobre cómo solucionar problemas que puede observar al usar Azure Data Box y Azure Data Box Heavy en los pedidos de importación. En el artículo se incluye la lista de posibles errores que aparecen cuando se copian datos en Data Box o cuando se cargan datos desde Data Box para un pedido de importación.

La información de este artículo no se aplica a los pedidos de exportación creados para Data Box.

## <a name="error-classes"></a>Clases de error

Los errores en Data Box y Data Box Heavy se resumen como sigue:

| Categoría de error*        | Descripción        | Acción recomendada    |
|----------------------------------------------|---------|--------------------------------------|
| Nombres de contenedor o recurso compartido | Los nombres de contenedor o de recurso compartido no siguen las reglas de nomenclatura de Azure.  |Descargue las listas de errores. <br> Cambiar el nombre de los contenedores o recursos compartidos. [Más información](#container-or-share-name-errors).  |
| Límite de tamaño de contenedor o recurso compartido | El total de datos en contenedores o recursos compartidos supera el límite de Azure.   |Descargue las listas de errores. <br> Reduzca los datos generales en el contenedor o recurso compartido. [Más información](#container-or-share-size-limit-errors).|
| Límite de tamaño de objeto o archivo | El objeto o los archivos en contenedores o recursos compartidos superan el límite de Azure.|Descargue las listas de errores. <br> Reduzca el tamaño de archivo en el contenedor o recurso compartido. [Más información](#object-or-file-size-limit-errors). |    
| Tipo de datos o archivo | No se admite el formato de datos o el tipo de archivo. |Descargue las listas de errores. <br> Para los blobs en páginas o discos administrados, asegúrese de que los datos tengan una alineación de 512 bytes y se copien en las carpetas creadas previamente. [Más información](#data-or-file-type-errors). |
| Errores internos de carpetas o archivos | El archivo o la carpeta tienen un error interno. |Descargue las listas de errores. <br> Quite el archivo y repita la copia. Para una carpeta, puede modificarla mediante el cambio de nombre o la adición o eliminación de un archivo. El error debería desaparecer en 30 minutos.  [Más información](#folder-or-file-internal-errors). |
| Errores no críticos de blob o archivo  | Los nombres de blob o archivo no siguen las reglas de nomenclatura de Azure o no se admite el tipo de archivo. | No se pueden copiar estos blobs o archivos, o se pueden cambiar los nombres. [Obtenga información sobre cómo corregir estos errores](#non-critical-blob-or-file-errors). |

\* Las cinco primeras categorías de error se corresponden a errores críticos y se deben corregir antes de avanzar a la preparación para el envío.


## <a name="container-or-share-name-errors"></a>Errores de nombre de contenedor o recurso compartido

Estos son errores relacionados con los nombres de contenedor y recurso compartido.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descripción del error:** El nombre del contenedor o recurso compartido debe tener entre 3 y 63 caracteres. 

**Resolución sugerida:** La carpeta en el recurso compartido (SMB/NFS) de Data Box o Data Box Heavy donde ha copiado los datos se convierte en un contenedor de Azure en su cuenta de almacenamiento. 

- En la página **Conectar y copiar** de la interfaz de usuario web local del dispositivo, descargue y revise los archivos de error para identificar los nombres de carpeta con problemas.
- Cambie el nombre de carpeta en el recurso compartido de Data Box o Data Box Heavy para asegurarse de lo siguiente:

    - El nombre tiene entre 3 y 63 caracteres.
    - Los nombres solo pueden tener letras, números y guiones.
    - Los nombres no pueden comenzar ni terminar con guiones.
    - Los nombres no pueden tener guiones consecutivos.
    - Ejemplos de nombres válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Ejemplos de nombres que no son válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para más información, consulte las convenciones de nomenclatura de Azure para los [nombres de contenedor](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) y los [nombres de recurso compartido](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descripción del error:** El nombre del contenedor o recurso compartido debe constar solo de letras, números o guiones.

**Resolución sugerida:** La carpeta en el recurso compartido (SMB/NFS) de Data Box o Data Box Heavy donde ha copiado los datos se convierte en un contenedor de Azure en su cuenta de almacenamiento. 

- En la página **Conectar y copiar** de la interfaz de usuario web local del dispositivo, descargue y revise los archivos de error para identificar los nombres de carpeta con problemas.
- Cambie el nombre de carpeta en el recurso compartido de Data Box o Data Box Heavy para asegurarse de lo siguiente:

    - El nombre tiene entre 3 y 63 caracteres.
    - Los nombres solo pueden tener letras, números y guiones.
    - Los nombres no pueden comenzar ni terminar con guiones.
    - Los nombres no pueden tener guiones consecutivos.
    - Ejemplos de nombres válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Ejemplos de nombres que no son válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para más información, consulte las convenciones de nomenclatura de Azure para los [nombres de contenedor](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) y los [nombres de recurso compartido](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descripción del error:** Los nombres de contenedor y los nombres de recurso compartido no se pueden comenzar ni terminar con guiones y no pueden tener guiones consecutivos.

**Resolución sugerida:** La carpeta en el recurso compartido (SMB/NFS) de Data Box o Data Box Heavy donde ha copiado los datos se convierte en un contenedor de Azure en su cuenta de almacenamiento. 

- En la página **Conectar y copiar** de la interfaz de usuario web local del dispositivo, descargue y revise los archivos de error para identificar los nombres de carpeta con problemas.
- Cambie el nombre de carpeta en el recurso compartido de Data Box o Data Box Heavy para asegurarse de lo siguiente:

    - El nombre tiene entre 3 y 63 caracteres.
    - Los nombres solo pueden tener letras, números y guiones.
    - Los nombres no pueden comenzar ni terminar con guiones.
    - Los nombres no pueden tener guiones consecutivos.
    - Ejemplos de nombres válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Ejemplos de nombres que no son válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para más información, consulte las convenciones de nomenclatura de Azure para los [nombres de contenedor](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) y los [nombres de recurso compartido](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**Descripción del error**: Los nombres de contenedor o directorio contienen caracteres no válidos.

**Resolución sugerida:**  Los nombres de directorio o contenedor que ha copiado contienen caracteres no admitidos.

- En la página Connect and copy (Conectar y copiar) de la interfaz de usuario web local, descargue y revise los archivos de error para identificar los nombres de carpeta con problemas. 
- Cambie el nombre del directorio o de los contenedores para asegurarse de que son compatibles con las convenciones de nomenclatura de Azure.

Para más información, consulte las convenciones de nomenclatura de Azure de los [directorios](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) y los [contenedores](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names).

## <a name="container-or-share-size-limit-errors"></a>Errores de límite de tamaño de contenedor o recurso compartido

Estos son errores relacionados con datos que superan el tamaño de datos permitido en un contenedor o recurso compartido.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descripción del error:** el recurso compartido de archivos de Azure limita a un recurso compartido a 5 TiB de datos, y los recursos compartidos de archivos de gran tamaño no están habilitados en la cuenta de almacenamiento. Se superó este límite para algunos recursos compartidos.

**Resolución sugerida:** En la página **Conectar y copiar** de la interfaz de usuario de web local, descargue y revise los archivos de error.

- Busque las carpetas que tienen este problema en los registros de error y asegúrese de que los archivos en esa carpeta tengan menos de 5 TiB.
- El límite de 5 TiB no se aplica a una cuenta de almacenamiento que permita los recursos compartidos de archivos de gran tamaño. Sin embargo, debe tener configurados algunos recursos compartidos de gran tamaño al realizar el pedido. 
  - Póngase en contacto con el [Soporte técnico de Microsoft](data-box-disk-contact-microsoft-support.md) y solicite una nueva etiqueta de envío.
  - [Habilitación de recursos compartidos de archivos de gran tamaño en la cuenta de almacenamiento](../storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [Expanda los recursos compartidos de archivos de la cuenta de almacenamiento](../storage/files/storage-how-to-create-file-share.md#expand-existing-file-shares) y establezca la cuota en 100 TiB.
  
  
## <a name="object-or-file-size-limit-errors"></a>Errores de límite de tamaño de objeto o archivo

Estos son errores relacionados con datos que superan el tamaño máximo del objeto o el archivo que se permite en Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descripción del error:** El tamaño del archivo supera el tamaño máximo para la carga.

**Resolución sugerida:** Los tamaños de blob o de archivo superan el límite máximo permitido para la carga.

- En la página **Conectar y copiar** de la interfaz de usuario de web local, descargue y revise los archivos de error.
- Asegúrese de que los tamaños de blob y de archivo no superen los límites de tamaño de objeto de Azure.

## <a name="data-or-file-type-errors"></a>Errores de tipo de datos o archivo

Estos son errores relacionados con tipos de archivo o tipos de datos no admitidos que se encuentra en el contenedor o recurso compartido. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descripción del error:** El blob o archivo no está alineado correctamente.

**Resolución sugerida:** El recurso compartido de blobs en páginas en Data Box o Data Box Heavy solo admite archivos que tienen una alineación de 512 bytes (por ejemplo, VHD/VHDX). Los datos que se copian en el recurso compartido de blobs en páginas se cargan en Azure como blobs en páginas.

Quite todos los datos que no sean VHD/VHDX del recurso compartido de blobs en páginas. Puede usar recursos compartidos de blob en bloques o archivos de Azure para datos genéricos.

Para más información, consulte la [Introducción a blobs en páginas de Azure](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descripción del error:** Un tipo de archivo no admitido está presente en un recurso compartido de disco administrado. Solo se permiten VHD fijos.

**Resolución sugerida:**

- Asegúrese de cargar solo VHD fijos para crear discos administrados.
- No se admiten archivos VHDX ni VHD **dinámicos** o de **diferenciación**.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descripción del error:** No se permite un directorio en ninguna de las carpetas ya existentes para los discos administrados. Solo se permiten VHD fijos en estas carpetas.

**Resolución sugerida:** Para discos administrados, dentro de cada recurso compartido, se crean las tres carpetas siguientes, que corresponden a contenedores de la cuenta de almacenamiento: SSD Premium, HDD estándar y SSD estándar. Estas carpetas se corresponden con el nivel de rendimiento para el disco administrado.

- Asegúrese de copiar los datos de blobs en páginas (VHD) en una de estas carpetas existentes.
- No se permiten una carpeta o un directorio en estas carpetas existentes. Quite las carpetas que haya creado dentro de las carpetas ya existentes.

Para más información, consulte [Copiar en discos administrados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Descripción del error:** No se permiten vínculos simbólicos en Linux. 

**Resolución sugerida:** Normalmente, los vínculos simbólicos son vínculos, canalizaciones y otros archivos de este tipo. Quite los vínculos, o resuelva los vínculos y copie los datos.

## <a name="folder-or-file-internal-errors"></a>Errores internos de carpetas o archivos

**Descripción del error:** El archivo o la carpeta están en un estado de error interno.

**Resolución sugerida:** Si se trata de un archivo, quítelo y vuelva a copiarlo. Si se trata de una carpeta, modifíquela. Cambie el nombre de la carpeta, o bien agregue o elimine un archivo de la carpeta. El error debería borrarse por sí solo en 30 minutos. Si el error persiste, póngase en contacto con el Soporte técnico de Microsoft.

## <a name="non-critical-blob-or-file-errors"></a>Errores no críticos de blob o archivo

En la sección siguiente se resumen todos los errores no críticos relacionados con los nombres de blobs, archivos o contenedores que se ven durante la copia de datos. Si estos errores existen, los nombres se modificarán para ajustarse a las convenciones de nomenclatura de Azure. El estado de pedido correspondiente para la carga de datos será **Completada con advertencias**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descripción del error:** Los nombres de blob o archivo contienen caracteres de control no admitidos.

**Resolución sugerida:** Los blobs o los archivos que ha copiado contienen nombres con caracteres no admitidos.

En la página **Conectar y copiar** de la interfaz de usuario de web local, descargue y revise los archivos de error.
Quite los archivos o cambie su nombre para quitar los caracteres no admitidos.

Para más información, consulte las convenciones de nomenclatura de Azure para los [nombres de blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) y los [nombres de archivo](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descripción del error:** Los nombres de blob o archivo contienen caracteres no válidos.

**Resolución sugerida:** Los blobs o los archivos que ha copiado contienen nombres con caracteres no admitidos.

En la página **Conectar y copiar** de la interfaz de usuario de web local, descargue y revise los archivos de error.
Quite los archivos o cambie su nombre para quitar los caracteres no admitidos.

Para más información, consulte las convenciones de nomenclatura de Azure para los [nombres de blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) y los [nombres de archivo](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descripción del error:** Los nombres de blob o archivo terminan con caracteres no válidos.

**Resolución sugerida:** Los blobs o los archivos que ha copiado contienen nombres con caracteres no admitidos.

En la página **Conectar y copiar** de la interfaz de usuario de web local, descargue y revise los archivos de error.
Quite los archivos o cambie su nombre para quitar los caracteres no admitidos.

Para más información, consulte las convenciones de nomenclatura de Azure para los [nombres de blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) y los [nombres de archivo](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descripción del error:** El nombre de blob o archivo contiene demasiados segmentos de ruta de acceso.

**Resolución sugerida:** Los blobs o los archivos que ha copiado superan el número máximo de segmentos de ruta de acceso. Un segmento de ruta de acceso es la cadena entre caracteres delimitadores consecutivos, por ejemplo, la barra diagonal "/".

- En la página **Conectar y copiar** de la interfaz de usuario de web local, descargue y revise los archivos de error.
- Asegúrese de que los [nombres de blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) y los [nombres de archivo](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) se ajusten a las convenciones de nomenclatura de Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descripción del error:** El nombre de blob o archivo es demasiado largo.

**Resolución sugerida:** Los nombres de blob o de archivo superan la longitud máxima.

- En la página **Conectar y copiar** de la interfaz de usuario de web local, descargue y revise los archivos de error.
- El nombre de blob no puede tener más de 1024 caracteres.
- Quite el blob o los archivos o cambie su nombre para que los nombres no superen los 1024 caracteres.

Para más información, consulte las convenciones de nomenclatura de Azure para los nombres de blob y los nombres de archivo.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descripción del error:** Uno de los segmentos del nombre de blob o archivo es demasiado largo.

**Resolución sugerida:** Uno de los segmentos de ruta de acceso en el nombre de blob o de archivo supera el número máximo de caracteres. Un segmento de ruta de acceso es la cadena entre caracteres delimitadores consecutivos, por ejemplo, la barra diagonal "/".

- En la página **Conectar y copiar** de la interfaz de usuario de web local, descargue y revise los archivos de error.
- Asegúrese de que los [nombres de blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) y los [nombres de archivo](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) se ajusten a las convenciones de nomenclatura de Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descripción del error:** Se han especificado nombres de contenedor incorrectos para los recursos compartidos de disco administrado.

**Resolución sugerida:** Para discos administrados, dentro de cada recurso compartido, se crean las carpetas siguientes, que corresponden a contenedores de la cuenta de almacenamiento: SSD Premium, HDD estándar y SSD estándar. Estas carpetas se corresponden con el nivel de rendimiento para el disco administrado.

- Asegúrese de copiar los datos de blobs en páginas (VHD) en una de estas carpetas existentes. Solo los datos de estos contenedores existentes se cargan en Azure.
- Ninguna otra carpeta que se cree en el mismo nivel que SSD Premium, HDD estándar y SSD estándar se corresponderá con un nivel de rendimiento válido y no podrá usarse.
- Quite los archivos o carpetas creados fuera de los niveles de rendimiento.

Para más información, consulte [Copiar en discos administrados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre los [requisitos de sistema del almacenamiento de blobs de Data Box](data-box-system-requirements-rest.md).
