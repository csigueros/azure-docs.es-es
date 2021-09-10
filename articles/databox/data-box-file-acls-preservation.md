---
title: Conservación de las ACL de archivo, los atributos y las marcas de tiempo con Azure Data Box
description: ACL, marcas de tiempo y atributos conservados durante la copia de datos a través de SMB en Azure Data Box. Copia de metadatos con las herramientas de copia de datos de Windows y Linux.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 96d3957a7626e393728d4a309bc56ecaa19d4e83
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400927"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Conservación de las ACL de archivo, los atributos y las marcas de tiempo con Azure Data Box

Azure Data Box le permite conservar las listas de control de acceso (ACL), las marcas de tiempo y los atributos de archivo al enviar datos a Azure. En este artículo se describen los metadatos que se pueden transferir al copiar datos a Data Box mediante el bloque de mensajes del servidor (SMB) para cargarlos en Azure Files. 

Se proporcionan pasos específicos para copiar metadatos con las herramientas de copia de datos de Windows y Linux. Los metadatos no se conservan al transferir datos a Blob Storage.

En este artículo, las ACL, las marcas de tiempo y los atributos de archivo que se transfieren se denominan colectivamente *metadatos*.

## <a name="transferred-metadata"></a>Metadatos transferidos

Los metadatos siguientes se transfieren cuando los datos de Data Box se cargan en Azure Files.

#### <a name="timestamps"></a>Marcas de tiempo

Se transfieren las marcas de tiempo siguientes:
- CreationTime
- Hora de última escritura

La siguiente marca de tiempo no se transfiere:
- LastAccessTime
  
#### <a name="file-attributes"></a>Atributos de archivo

Los atributos de archivo en los archivos y directorios se transfieren a menos que se indique lo contrario.

Se transfieren los siguientes atributos de archivo:
- FILE_ATTRIBUTE_READONLY (solo archivo)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (solo directorio)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (solo archivo)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

No se transfieren los siguientes atributos de archivo:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Los atributos de solo lectura de los directorios no se transfieren.

#### <a name="acls"></a>ACL

Todas las ACL para los directorios y archivos que se copian en Data Box a través de SMB se copian y se transfieren. Las transferencias incluyen las ACL discrecionales (DACL) y las ACL del sistema (SACL). Para Linux, solo se transfieren las ACL de Windows NT.

Las ACL no se transfieren durante las copias de datos en Network File System (NTS) ni cuando se usa el servicio de copia de datos para transferir los datos. El servicio de copia de datos lee los datos directamente en los recursos compartidos y no puede leer las ACL.

Incluso si la herramienta de copia de datos no copia las ACL, las ACL predeterminadas en los directorios y archivos se transfieren a Azure Files. Las ACL predeterminadas tienen permisos para la cuenta predefinida de administrador, la cuenta del sistema y la cuenta de usuario del recurso compartido SMB que se usó para montar y copiar datos en Data Box.

Las ACL contienen descriptores de seguridad con las siguientes propiedades: ACLs, Owner, Group, SACL.

La transferencia de ACL está habilitada de forma predeterminada. Es posible que desee deshabilitar esta opción en la interfaz de usuario web local de su Data Box. Para obtener más información consulte [Uso de la interfaz de usuario web local para administrar los dispositivos Data Box y Data Box Heavy](./data-box-local-web-ui-admin.md).

> [!NOTE]
> Los archivos con ACL que contienen cadenas de entradas de control de acceso condicional (ACE) no se copian. Este es un problema conocido. Para evitar esto, copie estos archivos en el recurso compartido de Azure Files manualmente; para ello, monte el recurso compartido y, a continuación, use una herramienta de copia que admita la copia de ACL.

## <a name="copying-data-and-metadata"></a>Copia de datos y metadatos

Para transferir las ACL, las marcas de tiempo y los atributos de los datos, use los procedimientos siguientes para copiar los datos en Data Box. 

### <a name="windows-data-copy-tool"></a>Herramienta de copia de datos de Windows

Para copiar datos en el Data Box a través de SMB, use una herramienta de copia de archivos compatible con SMB, como `robocopy`. El siguiente comando de ejemplo copia todos los archivos y directorios, transfiriendo los metadatos junto con los datos.

Al usar la opción `/copyall` o `/dcopy:DAT`, asegúrese de que los privilegios de operador de copia de seguridad necesarios no estén deshabilitados. Para obtener más información consulte [Uso de la interfaz de usuario web local para administrar los dispositivos Data Box y Data Box Heavy](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|Opción |Descripción |
|------------------- | ----- |
|`/copyall` |Copa todos los atributos.|
|`/e`      |Copia los subdirectorios, incluyendo los directorios vacíos.         |
|`/dcopy:DAT`  |Copia datos, atributos y marcas de tiempo. Nota: La opción /dcopy:DAT se debe utilizar para transferir `CreationTime` en los directorios. |
|`/r:3`    |Especifica 3 reintentos para las copias con errores.         |
|`/w:60`   |Especifica un tiempo de espera de 60 segundos entre los reintentos.         |
|`/is`     |Incluye los mismos archivos.         |
|`/nfl`    |No registra los nombres de archivo.         |
|`/ndl`    |No registra los nombres de directorio.        |
|`/np`     |No muestra el progreso de la operación de copia.         |
|`/MT:32 or 64`  |Utiliza multithreading, con 32 o 64 subprocesos.           |
|`/fft`    |Reduzca la granularidad de la marca de tiempo para cualquier sistema de archivos.        |
|`/log+:<LogFile>`  |Anexa la salida al archivo de registro existente.|

Para obtener más información acerca de estos parámetros `robocopy`, vea [Tutorial: Copia de datos a Azure Data Box Disk mediante SMB](./data-box-deploy-copy-data.md).

> [!NOTE]
> Si usa para `/copyall` copiar los datos, las ACL de origen de los directorios y archivos se transfieren a Azure Files. Si solo tenía acceso de lectura en los datos de origen y no pudo modificar los datos de origen, solo tendrá acceso de lectura en los datos de Data Box. Use `/copyall` solamente si piensa copiar todas las ACL en los directorios y archivos junto con los datos.

#### <a name="use-robocopy-to-list-copy-modify-files-on-data-box"></a>Uso de robocopy para realizar operaciones de enumeración, copia o modificación en archivos de Data Box

Estos son algunos de los escenarios comunes que usará al copiar datos mediante `robocopy`.

- **Copiar solo datos en Data Box, sin ACL de directorios ni archivos**

    Use la opción `/dcopy:DAT` para copiar solo datos, atributos y marcas de tiempo. Las ACL de directorios y archivos no se copian.

- **Copiar datos y ACL de directorios y archivos en Data Box**

    Use `/copyall` para copiar todos los datos de origen, incluidas todas las ACL de directorios y archivos.

- **Enumerar el sistema de archivos de Data Box mediante robocopy**

    Use este comando para enumerar el contenido del directorio:

    `robocopy <source-dir> NULL /l /s /xx /njh /njs /fp /B`

    Tenga en cuenta que el Explorador de archivos no permite enumerar estos archivos.
    
- **Copiar o eliminar carpetas y archivos de Data Box**

    Use este comando para copiar un único archivo:

    `robocopy <source-dir> <destination-dir> <file-name> /B`

    Use este comando para eliminar un único archivo:

    `robocopy <source-dir> <destination-dir> <file-name> /purge /B`

    En el comando anterior, `<source-dir>` no debe tener el archivo `<file-name>`. A continuación, el comando anterior sincroniza el destino con el origen, lo que da lugar a la eliminación del archivo del destino.

    Tenga en cuenta que el Explorador de archivos puede no permitirle realizar las operaciones anteriores.

Para más información, consulte [Uso de los comandos robocopy](/windows-server/administration/windows-commands/robocopy).

### <a name="linux-data-copy-tool"></a>Herramienta de copia de datos de Linux

La transferencia de metadatos en Linux es un proceso de dos pasos. En primer lugar, copie los datos de origen mediante una herramienta como `rsync`, que no copia los metadatos. Después de copiar los datos, puede copiar los metadatos mediante una herramienta como `smbcacls` o `cifsacl`. 

Los siguientes comandos de ejemplo realizan el primer paso, copiando los datos mediante `rsync`. 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Pasos siguientes

- [Copia de datos a Azure Data Box Disk mediante SMB](./data-box-deploy-copy-data.md)
