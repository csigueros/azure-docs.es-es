---
title: Uso de Python para administrar datos en Azure Data Lake Storage Gen2
description: Use Python para administrar directorios y archivos en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a2db4734650f4af2f0ed67040c7f94eeda5f7c69
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453108"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Uso de Python para administrar directorios y archivos en Azure Data Lake Storage Gen2

En este artículo se explica cómo usar Python para crear y administrar directorios y archivos en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico.

Para obtener información sobre cómo obtener, establecer y actualizar las listas de control de acceso (ACL) de directorios y archivos, consulte [Uso de Python para administrar listas de control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-acl-python.md).

[Paquete (índice de paquetes de Python)](https://pypi.org/project/azure-storage-file-datalake/) | [Muestras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [Referencia de API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Asignación de Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Envíenos sus comentarios](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Una cuenta de almacenamiento que tenga habilitado un espacio de nombres jerárquico. Siga [estas](create-data-lake-storage-account.md) instrucciones para crear uno.

## <a name="set-up-your-project"></a>Configurar su proyecto

Instale la biblioteca de cliente de Azure Data Lake Storage para Python con [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Agregue estas instrucciones import al inicio del archivo de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Conexión con la cuenta

Para usar los fragmentos de código de este artículo, tiene que crear una instancia de **DataLakeServiceClient** que represente la cuenta de almacenamiento. 

### <a name="connect-by-using-an-account-key"></a>Conexión con una clave de cuenta

Es la manera más sencilla de conectarse a una cuenta. 

En este ejemplo se crea una instancia de **DataLakeServiceClient** con una clave de cuenta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Reemplace el valor de marcador de posición `storage_account_name` por el nombre de la cuenta de almacenamiento.

- Reemplace el valor de marcador de posición `storage_account_key` por la clave de acceso de la cuenta de almacenamiento.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conexión con Azure Active Directory (Azure AD)

Puede usar la [biblioteca cliente de identidad de Azure para Python](https://pypi.org/project/azure-identity/) para autenticar la aplicación con Azure AD.

En este ejemplo se crea una instancia de **DataLakeServiceClient** con un identificador de cliente, un secreto de cliente y un identificador de inquilino.  Para obtener estos valores, consulte [Adquisición de un token de Azure AD para la autorización de solicitudes desde una aplicación cliente](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para ver más ejemplos, consulte la documentación de la [biblioteca cliente de identidad de Azure para Python](https://pypi.org/project/azure-identity/).

## <a name="create-a-container"></a>Crear un contenedor

Un contenedor actúa como sistema de archivos para sus archivos. Puede crear uno llamando al método **FileSystemDataLakeServiceClient.create_file_system**.

En este ejemplo se crea un contenedor denominado `my-file-system`.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Creación de un directorio

Cree una referencia de directorio llamando al método **FileSystemClient.create_directory**.

En este ejemplo se agrega un directorio denominado `my-directory` a un contenedor. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Cambio de nombre o traslado de un directorio

Cambie el nombre de un directorio o muévalo llamando al método **DataLakeDirectoryClient.rename_directory**. Pase la ruta de acceso del directorio que busca a un parámetro. 

En este ejemplo se cambia el nombre de un subdirectorio a `my-directory-renamed`.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Eliminación de un directorio

Elimine un directorio o muévalo llamando al método **DataLakeDirectoryClient.rename_directory**.

En este ejemplo se elimina un directorio denominado `my-directory`.  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Carga de un archivo en un directorio

En primer lugar, cree una referencia de archivo en el directorio de destino creando una instancia de la clase **DataLakeFileClient**. Cargue un archivo llamando al método **DataLakeFileClient.append_data**. Asegúrese de completar la carga llamando al método **DataLakeFileClient.flush_data**.

En este ejemplo se carga un archivo de texto en un directorio denominado `my-directory`.   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Si el tamaño del archivo es grande, el código tendrá que realizar varias llamadas al método **DataLakeFileClient.append_data**. Considere la posibilidad de usar en su lugar el método **DataLakeFileClient.upload_data**. De este modo, puede cargar todo el archivo en una sola llamada. 

## <a name="upload-a-large-file-to-a-directory"></a>Carga de un archivo de gran tamaño en un directorio

Use el método **DataLakeFileClient.upload_data** para cargar archivos grandes sin tener que realizar varias llamadas al método **DataLakeFileClient.append_data**.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Descarga de un directorio 

Abra un archivo local para escritura. Luego, cree una instancia de **DataLakeFileClient** que represente al archivo que quiere descargar. Llame a **DataLakeFileClient.read_file** para leer los bytes del archivo y, a continuación, escriba esos bytes en el archivo local. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Lista del contenido del directorio

Muestre el contenido del directorio llamando al método **FileSystemClient.get_paths**, que luego enumera los resultados.

En este ejemplo se imprime la ruta de acceso de cada uno de los subdirectorios y archivos que se encuentra en un directorio denominado `my-directory`.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Consulte también

- [Documentación de referencia de API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Paquete (índice de paquetes de Python)](https://pypi.org/project/azure-storage-file-datalake/)
- [Muestras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Asignación de Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemas conocidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Envíenos sus comentarios](https://github.com/Azure/azure-sdk-for-python/issues)