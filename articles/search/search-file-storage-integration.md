---
title: Indexación de datos de Azure Files (versión preliminar)
titleSuffix: Azure Cognitive Search
description: Configure un indexador de Azure Files para automatizar la indexación de recursos compartidos de archivos en Azure Cognitive Search.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 37f33bb17b48f482f27a449871acc7256713c100
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157559"
---
# <a name="index-data-from-azure-files"></a>Indexación de datos de Azure Files

> [!IMPORTANT] 
> Azure Files se encuentra actualmente en versión preliminar pública según los [Términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use una [API REST en versión preliminar (2021-04-30-preview o posterior)](search-api-preview.md) para indexar el contenido. Actualmente hay compatibilidad limitada con el portal y no la hay con .NET SDK.

En este artículo se examina el flujo de trabajo básico para extraer contenido y metadatos de recursos compartidos de archivos de Azure y enviarlos a un índice de búsqueda en Azure Cognitive Search. En el índice resultante se pueden realizar consultas mediante la búsqueda de texto completo.

> [!NOTE]
> ¿Conoce el flujo de trabajo y su composición? El siguiente paso es [configurar un indexador de archivos](#configure).

## <a name="functionality"></a>Funcionalidad

Un indexador en Azure Cognitive Search es un rastreador que extrae metadatos y datos que permiten búsquedas de un origen de datos. El indexador de Azure Files se conectará al recurso compartido de archivos de Azure y a los archivos de índice. El indexador proporciona la funcionalidad siguiente:

+ Indexación de contenido de un recurso compartido de archivos de Azure.
+ El indexador admitirá la indexación incremental, lo que significa que identificará qué contenido del recurso compartido de archivos de Azure ha cambiado y solo indexará aquel que se haya actualizado en futuras ejecuciones de indexación. Por ejemplo, si el indexador indexa originalmente 5 archivos PDF, se actualiza 1; luego, si se vuelve a ejecutar el indexador, solo se indexará el PDF que se actualizó.
+ El texto y las imágenes normalizadas se extraen de forma predeterminada de los archivos que se indexan. Opcionalmente, se puede agregar un conjunto de aptitudes a la canalización para enriquecer aún más el contenido. Puede encontrar más información sobre los conjuntos de aptitudes en el artículo [Conceptos sobre los conjuntos de aptitudes en Azure Cognitive Search](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Formatos de documento admitidos

El indexador de Azure Files de Azure Cognitive Search puede extraer texto de los siguientes formatos de documento:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="required-resources"></a>Recursos necesarios

Se necesitan tanto Azure Cognitive Search como [Azure Files](https://azure.microsoft.com/services/storage/files/). Dentro de Azure Files, necesita un recurso compartido de archivos que proporciona contenido de origen.

> [!NOTE]
> Para indexar un recurso compartido de archivos, este debe admitir el acceso mediante la [API REST del plano de datos de archivos](/rest/api/storageservices/file-service-rest-api). [Los recursos compartidos NFS](../storage/files/files-nfs-protocol.md#support-for-azure-storage-features) no admiten la API REST del plano de datos de archivos y no se pueden usar con los indexadores de Azure Cognitive Search. [Los recursos compartidos SMB](../storage/files/files-smb-protocol.md) admiten la API REST del plano de datos de archivos y se pueden usar con los indexadores de Azure Cognitive Search.

<a name="configure"></a>

## <a name="configuring-a-file-indexer"></a>Configuración de un indexador de archivos

Los indexadores de archivos de Azure comparten muchas opciones de configuración comunes con los [indexadores de blobs de Azure](search-howto-indexing-azure-blob-storage.md). Por ejemplo, los indexadores de archivos de Azure admiten la [generación de varios documentos de búsqueda a partir de un único archivo](search-howto-index-one-to-many-blobs.md), [archivos de texto sin formato](search-howto-index-plaintext-blobs.md), [archivos JSON](search-howto-index-json-blobs.md) y [archivos cifrados](search-howto-index-encrypted-blobs.md). También se aplican muchas de las mismas [opciones de configuración](search-howto-indexing-azure-blob-storage.md). A continuación se resaltan las diferencias más importantes.

## <a name="data-source-definitions"></a>Definiciones de origen de datos

La diferencia principal entre un indexador de archivos y cualquier otro indexador es la definición de origen de datos que se asigna al indexador. La definición del origen de datos especifica el tipo de origen de datos ("type": "azurefile"), así como otras propiedades de autenticación y conexión con el contenido que se va a indexar.

Una definición de origen de datos de archivo es similar al ejemplo siguiente:

```http
{
    "name" : "my-file-datasource",
    "type" : "azurefile",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-file", "query" : "<optional-directory-name>" }
}
```

La propiedad `"credentials"` puede ser una cadena de conexión, como se muestra en el ejemplo anterior, o uno de los enfoques alternativos descritos en la sección siguiente. La propiedad `"container"` proporciona el recurso compartido de archivos dentro de Azure Storage y `"query"` se usa para especificar una subcarpeta en el recurso compartido. Para obtener más información sobre las definiciones de origen de datos, vea [Creación de un origen de datos (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Credenciales

Puede proporcionar las credenciales para el recurso compartido de archivos de una de estas maneras:

**Cadena de conexión de identidad administrada**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Esta cadena de conexión no requiere una clave de cuenta, pero es preciso seguir las instrucciones de [Configuración de una conexión a una cuenta de Azure Storage mediante una identidad administrada](search-howto-managed-identities-storage.md).

**Cadena de conexión de la cuenta de almacenamiento de acceso total**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Puede obtener la cadena de conexión en Azure Portal si va al panel de la cuenta de almacenamiento > Configuración > Claves (para las cuentas de almacenamiento del modelo clásico) o Seguridad y redes > Claves de acceso (para las cuentas de almacenamiento de Azure Resource Manager).

Cadena de conexión de la **firma de acceso compartido de la cuenta de almacenamiento** (SAS): `{ "connectionString" : "BlobEndpoint=https://<your account>.file.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&sp=rl&sr=s;" }`

La firma de acceso compartido debe tener permisos de enumeración y lectura sobre los recursos compartidos de archivos.

**Firma de acceso compartido de contenedor**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.file.core.windows.net/<share name>?sv=2016-05-31&sr=s&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

La firma de acceso compartido debe tener permisos de enumeración y lectura sobre el recurso compartido de archivos. Para más información sobre las firmas de acceso compartido de almacenamiento, consulte [Uso de firmas de acceso compartido (SAS)](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Si usa credenciales SAS, deberá actualizar las credenciales del origen de datos periódicamente con firmas renovadas para evitar que caduquen. Si las credenciales SAS expiran, el indexador produce un error con un mensaje parecido a este: "Las credenciales proporcionadas en la cadena de conexión no son válidas o han expirado".

## <a name="indexing-file-metadata"></a>Indexación de metadatos de archivos

Una práctica común que permite ordenar fácilmente archivos con cualquier tipo de contenido consiste en indexar los metadatos personalizados y las propiedades de sistema de cada uno de los archivos. De esta manera, la información de todos los archivos se indexa con independencia del tipo de documento y se almacena en un índice en el servicio de búsqueda. Con el nuevo índice, puede continuar con la ordenación, filtrado y búsqueda por facetas en todo el contenido de File Storage.

Las propiedades de metadatos de archivos estándar se pueden extraer en los campos que se indican a continuación. El indexador de archivos crea automáticamente asignaciones de campo internas para estas propiedades de metadatos de archivos. Todavía tiene que agregar los campos que quiere usar para la definición de índice, pero puede omitir la creación de asignaciones de campos en el indexador.

+ **metadata_storage_name** (`Edm.String`): nombre del archivo. Por ejemplo, si tiene un archivo /my-share/my-folder/subfolder/resume.pdf, el valor de este campo es `resume.pdf`.
+ **metadata_storage_path** (`Edm.String`): URI completo del archivo, incluida la cuenta de almacenamiento. Por ejemplo: `https://myaccount.file.core.windows.net/my-share/my-folder/subfolder/resume.pdf`
+ **metadata_storage_content_type** (`Edm.String`): tipo de contenido tal como especifica el código que usó para cargar el archivo. Por ejemplo, `application/octet-stream`.
+ **metadata_storage_last_modified** (`Edm.DateTimeOffset`): última marca de tiempo modificada del archivo. Azure Cognitive Search emplea esta marca de tiempo para identificar los archivos modificados a fin de evitar volver a indexar todo después de la indexación inicial.
+ **metadata_storage_size** (`Edm.Int64`): tamaño del archivo en bytes.
+ **metadata_storage_content_md5** (`Edm.String`): hash MD5 del contenido del archivo, si está disponible.
+ **metadata_storage_sas_token** (`Edm.String`): un token de SAS temporal que pueden usar las [aptitudes personalizadas](cognitive-search-custom-skill-interface.md) para obtener acceso al archivo. Este token no se debe almacenar para su uso posterior, ya que podría expirar.

## <a name="index-by-file-type"></a>Índice por tipo de archivo

Puede controlar qué documentos se indexan y cuáles se omiten.

### <a name="include-documents-having-specific-file-extensions"></a>Inclusión de los documentos que tienen extensiones de archivo concretas

Puede indexar solo los documentos que tengan las extensiones de nombre de archivo que especifique mediante el parámetro de configuración del indexador `indexedFileNameExtensions`. El valor es una cadena que contiene una lista separada por comas de extensiones de archivo (con un punto inicial). Por ejemplo, para indexar solamente los documentos .PDF y .DOCX, haga lo siguiente:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Exclusión de los documentos que tienen extensiones de archivo concretas

Puede excluir de la indexación documentos que tengan extensiones de nombre de archivo específicas mediante el parámetro de configuración `excludedFileNameExtensions`. El valor es una cadena que contiene una lista separada por comas de extensiones de archivo (con un punto inicial). Por ejemplo, para indexar todo el contenido excepto el que tenga las extensiones .PNG y .JPEG, haga lo siguiente:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Si los dos parámetros `indexedFileNameExtensions` y `excludedFileNameExtensions` existen, Azure Cognitive Search mira primero en `indexedFileNameExtensions` y, luego, en `excludedFileNameExtensions`. Esto significa que si la misma extensión de archivo está presente en las dos listas, se excluirá de la indexación.

<a name="deleted-files"></a>

## <a name="detecting-deleted-files"></a>Detección de archivos eliminados

Después de crear un índice de búsqueda inicial, es posible que desee que los trabajos posteriores del indexador solo recojan documentos nuevos y modificados. En el caso del contenido de búsqueda cuyo origen sea Azure File Storage, la detección de cambios se produce automáticamente cuando se usa una programación para desencadenar la indexación. De forma predeterminada, el servicio reindexa solo los archivos modificados, tal como lo determina la marca de tiempo `LastModified` del archivo. A diferencia de otros orígenes de datos compatibles con indexadores de búsqueda, los archivos siempre tienen una marca de tiempo, lo que elimina la necesidad de configurar manualmente una directiva de detección de cambios.

Aunque la detección de cambios es una obviedad, la detección de eliminaciones no lo es. Si quiere detectar archivos eliminados, asegúrese de usar un enfoque de "eliminación temporal". Si elimina los archivos directamente, los documentos correspondientes no se quitarán del índice de búsqueda.

## <a name="soft-delete-using-custom-metadata"></a>Eliminación temporal con metadatos personalizados

Este método usa los metadatos de un archivo para determinar si un documento de búsqueda se debe eliminar del índice. Este método requiere dos acciones independientes, la eliminación del documento de búsqueda del índice, seguida de la eliminación de archivos en Azure Storage.

Hay pasos que se deben seguir tanto en File Storage como en Cognitive Search, pero no hay otras dependencias de características. Esta funcionalidad se admite en las API con disponibilidad general.

1. Agregue un par clave-valor de metadatos personalizado al archivo para indicar a Azure Cognitive Search que se ha eliminado de forma lógica.

1. Configurar una directiva de detección de columnas de eliminación temporal en el origen de datos. Por ejemplo, la siguiente directiva considera que un archivo se va a eliminar si tiene una propiedad de metadatos `IsDeleted` con el valor `true`:

    ```http
    PUT https://[service name].search.windows.net/datasources/file-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "file-datasource",
        "type" : "azurefile",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-share", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Una vez que el indexador ha procesado el blob y ha eliminado el documento del índice, puede eliminar el blob de Azure Blob Storage.

### <a name="reindexing-undeleted-files-using-custom-metadata"></a>Reindexación de archivos recuperados (mediante metadatos personalizados)

Después de que un indexador procese un archivo eliminado y quite el documento de búsqueda correspondiente del índice, no volverá a visitar ese archivo si lo restaura más tarde si la marca de tiempo del `LastModified` del archivo es anterior a la última ejecución del indexador.

Si quiere volver a indexar ese documento, cambie el valor de `"softDeleteMarkerValue" : "false"` del archivo y vuelva a ejecutar el indexador.

## <a name="see-also"></a>Consulte también

+ [Indexadores de Azure Cognitive Search](search-indexer-overview.md)
+ [¿Qué es Azure Files?](../storage/files/storage-files-introduction.md)
