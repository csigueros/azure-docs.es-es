---
title: Requisitos del almacenamiento de blobs de Microsoft Azure Data Box | Microsoft Docs
description: Obtenga información sobre las versiones compatibles para las API, SDK y bibliotecas de cliente para el almacenamiento de blobs de Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: 4c62c1e854e55b8817724c78bc325badc703bc27
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250662"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisitos de almacenamiento de blobs de Azure Data Box

En este artículo se enumeran las versiones de las API de Azure, las bibliotecas de cliente de Azure y las herramientas admitidas con el almacenamiento de blobs de Data Box. El almacenamiento de blobs de Azure Data Box proporciona la funcionalidad de administración de blobs con una semántica coherente con Azure. En este artículo también se resumen las diferencias conocidas entre el almacenamiento de blobs de Azure Data Box y los servicios de Azure Storage.

Se recomienda que revise cuidadosamente la información antes de conectarse al almacenamiento de blobs de Azure Data Box y que luego vuelva a ella si es necesario.


## <a name="storage-differences"></a>Diferencias de almacenamiento

|     Característica                                             |     Azure Storage                                     |     Almacenamiento de blobs de Azure Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure Files                                   |    Se admiten recursos compartidos de archivos SMB y NFS basados en la nube               |    No compatible      |
|    Cifrado de servicio para datos en reposo                  |    Cifrado de AES de 256 bits                             |    Cifrado de AES de 256 bits |
|    Tipo de cuenta de almacenamiento                                 |    Cuentas de Azure Blob Storage y de uso general    |    Solo de uso general v1|
|    Nombre de blob                                            |    1 024 caracteres (2 048 bytes)                     |    880 caracteres (1 760 bytes)|
|    Tamaño máximo del blob en bloques                              |    4,75 TiB (100 MB x 50 000 bloques)                   |    4,75 TiB (100 MB x 50 000 bloques) para Azure Data Box v 3.0 y versiones posteriores.|
|    Tamaño máximo del blob en páginas                               |    8 TiB                                               |    1 TiB                   |
|    Tamaño de página de blob en página                                  |    512 bytes                                          |    4 KiB                   |

## <a name="supported-api-versions"></a>Versiones de API admitidas

Se admiten las siguientes versiones de las API del servicio Azure Storage con Blob Storage de Azure Data Box.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3.0 y posteriores

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Bibliotecas de clientes de Azure compatibles

Para el almacenamiento en blobs de Azure Data Box, hay requisitos de bibliotecas de cliente y de sufijos de puntos de conexión que son específicos. Los puntos de conexión del Blob Storage de Azure Data Box no tienen una paridad completa con la última versión de la API de REST de Azure Blob Storage. Para más información, vea [las versiones compatibles con Azure Data Box 3.0 y versiones posteriores](#supported-api-versions). En lo que respecta a las bibliotecas de cliente de almacenamiento, debe conocer cuál es la versión compatible con la API REST.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3.0 y posteriores

Las siguientes versiones de la biblioteca de cliente de Azure son compatibles con Blob Storage de Azure Data Box.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>Instalación del cliente PHP a través de compositor: actual

Para instalar a través de compositor: (usaremos blob como ejemplo).
1. Cree un archivo llamado composer.json en la raíz del proyecto con el siguiente código:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Descargue `composer.phar` a la raíz del proyecto.

3. Ejecute: php composer.phar install.

### <a name="endpoint-declaration"></a>Declaración de punto de conexión

En el SDK de Blob Storage de Data Box, el sufijo del punto de conexión, `<device serial number>.microsoftdatabox.com`, identifica el dominio de Data Box. Para obtener más información sobre el punto de conexión de servicio de blob, vaya a [Conexión mediante Blob Storage de Azure Data Box](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Ejemplos

### <a name="net"></a>.NET

Para el almacenamiento en blobs de Azure Data Box, se especifica el sufijo de punto de conexión en el archivo `app.config`:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Para el almacenamiento en blobs de Azure Data Box, se especifica el sufijo de punto de conexión en la configuración de la cadena de conexión:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Para el almacenamiento en blobs de Azure Data Box, se especifica el sufijo de punto de conexión en la instancia de declaración:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Para el almacenamiento en blobs de Azure Data Box, se especifica el sufijo de punto de conexión en la configuración de la cadena de conexión:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Para el almacenamiento en blobs de Azure Data Box, se especifica el sufijo de punto de conexión en la configuración de la cadena de conexión:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para el almacenamiento en blobs de Azure Data Box, se especifica el sufijo de punto de conexión en la instancia de declaración:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Para el almacenamiento en blobs de Azure Data Box, se especifica el sufijo de punto de conexión en la configuración de la cadena de conexión:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de Azure Data Box](data-box-deploy-ordered.md)
