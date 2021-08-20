---
title: 'Inicio rápido: Biblioteca de recursos compartidos de archivos de Azure Storage v12: C++'
description: En este inicio rápido aprenderá a usar la biblioteca cliente de recursos compartidos de archivos de Azure Storage, versión 12, para C++ con el fin de crear un recurso compartido de archivos y un archivo. A continuación aprenderá a establecer y recuperar metadatos y a descargar el archivo en la máquina local.
author: kyle-patterson
ms.author: kylepa
ms.date: 06/22/2021
ms.service: storage
ms.subservice: files
ms.topic: quickstart
ms.openlocfilehash: 4da02f46ef793ae03a11fa4895471488f78d0db1
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894305"
---
# <a name="develop-for-azure-files-with-c"></a>Desarrollo con C++ para Azure Files

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="about-this-tutorial"></a>Acerca de este tutorial

En este tutorial aprenderá a realizar operaciones básicas en Azure Files con C++. Si es la primera vez que usa Azure Files, le resultará útil repasar los conceptos de las secciones siguientes para comprender los ejemplos. Algunos de los ejemplos tratados son:

* Crear y eliminar recursos compartidos de archivos de Azure
* Crear y eliminar directorios
* Cargar, descargar y eliminar un archivo
* Establecer y enumerar los metadatos de un archivo

> [!Note]  
> Dado que se puede acceder a Azure Files a través de SMB, es posible escribir aplicaciones sencillas que accedan al recurso compartido de archivos de Azure mediante las clases y funciones estándar de E/S de C++. En este artículo se describe cómo escribir aplicaciones que utilizan el SDK de C++ de Azure Storage, que usa la [API de REST de archivos](/rest/api/storageservices/file-service-rest-api) para comunicarse con Azure Files.


## <a name="prerequisites"></a>Requisitos previos

- [Suscripción de Azure](https://azure.microsoft.com/free/)
- [Cuenta de Almacenamiento de Azure](../common/storage-account-create.md)
- [Compilador de C++](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg: administrador de paquetes de C y C++](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)

## <a name="setting-up"></a>Instalación

En esta sección se explica cómo preparar un proyecto para que funcione con la versión 12 de la biblioteca cliente de Azure Blob Storage para C++.

### <a name="install-the-packages"></a>Instalación de los paquetes

El comando `vcpkg install` instalará el SDK de Azure Storage Blobs para C++ y las dependencias necesarias:

```console
vcpkg.exe install azure-storage-files-shares-cpp:x64-windows
```

Para más información, visite GitHub para adquirir y crear el [SDK de Azure para C++](https://github.com/Azure/azure-sdk-for-cpp/).

### <a name="create-the-project"></a>Crear el proyecto

En Visual Studio, cree una aplicación de consola de C++ para Windows llamada *FilesShareQuickstartV12*.

:::image type="content" source="./media/quickstart-files-c-plus-plus/visual-studio-create-project.png" alt-text="Cuadro de diálogo de Visual Studio para configurar una nueva aplicación de consola de Windows en C++":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="code-examples"></a>Ejemplos de código

Estos fragmentos de código de ejemplo muestran cómo realizar las siguientes tareas con la biblioteca cliente de recursos compartidos de archivos de Azure para C++:

- [Adición de archivos de inclusión](#add-include-files)
- [Obtención de la cadena de conexión](#get-the-connection-string)
- [Creación de un recurso compartido de archivos](#create-a-files-share)
- [Carga de archivos en un recurso compartido de archivos](#upload-files-to-a-files-share)
- [Establecimiento de los metadatos de un archivo](#set-the-metadata-of-a-file)
- [Enumeración de los metadatos de un archivo](#list-the-metadata-of-a-file)
- [Descarga de archivos](#download-files)
- [Eliminar un archivo](#delete-a-file)
- [Eliminación de un recurso compartido de archivos](#delete-a-files-share)

### <a name="add-include-files"></a>Adición de archivos de inclusión

Desde el directorio del proyecto:

1. Abra el archivo de soluciones *FilesShareQuickstartV12.sln* en Visual Studio.
1. En Visual Studio, abra el archivo de origen *FilesShareQuickstartV12.cpp*.
1. Quite todo el código incluido en `main` que se haya generado automáticamente.
1. Agregue las instrucciones `#include`.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Obtención de la cadena de conexión

El código siguiente recupera la cadena de conexión de la cuenta de almacenamiento de la variable de entorno creada en [Configuración de la cadena de conexión de almacenamiento](#configure-your-storage-connection-string).

Agregue este código a `main()`:

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_ConnectionString":::

### <a name="create-a-files-share"></a>Creación de un recurso compartido de archivos

Cree una instancia de la clase [ShareClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html); para ello, llame a la función [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a7462bcad8a9144f84b0acc70735240e2). Luego, llame a [createIfNotExists](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a6432b4cc677ac03257c7bf234129ec5b) para crear el recurso compartido de archivos real en la cuenta de almacenamiento.

Agregue este código al final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_CreateFilesShare":::

### <a name="upload-files-to-a-files-share"></a>Carga de archivos en un recurso compartido de archivos

El siguiente fragmento de código:

1. Declara una cadena que contiene "Hello Azure!".
1. Obtiene una referencia a un objeto [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html) al obtener el [ShareDirectoryClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html) raíz y llamar a [GetFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html#a93545b8d82ee94f9de183c4d277059d8) en el recurso compartido de archivos de la sección [Creación de un recurso compartido de archivos](#create-a-files-share).
1. Carga la cadena en el archivo al llamar a la función [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#aa80c1f0da6e6784aa0a67cff03d128ba). Esta función crea el archivo, en caso de que no exista, o lo actualiza si existe.

Agregue este código al final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_UploadFile":::

### <a name="set-the-metadata-of-a-file"></a>Establecimiento de los metadatos de un archivo

Establezca las propiedades de los metadatos de un archivo mediante una llamada a la función [ShareFileClient.SetMetadata](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a2f5a40b71040003e41ba8495101f67bb).

Agregue este código al final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_SetFileMetadata":::

### <a name="list-the-metadata-of-a-file"></a>Enumeración de los metadatos de un archivo

Establezca las propiedades de los metadatos de un archivo mediante una llamada a la función [ShareFileClient.GetProperties](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a220017815847877cfe8f244b482fb45c). Los metadatos están en el campo `Metadata` del `Value` devuelto. Los metadatos serán un par clave-valor, similar al ejemplo de [Establecimiento de los metadatos de un archivo](#set-the-metadata-of-a-file).

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_GetFileMetadata":::

### <a name="download-files"></a>Descarga de archivos

Una vez recuperadas las propiedades del archivo en [Enumeración de los metadatos de un archivo](#list-the-metadata-of-a-file), se crea un nuevo objeto `std::vector<uint8_t>` con las propiedades del archivo cargado. Descargue el archivo que ha creado anteriormente en el nuevo objeto `std::vector<uint8_t>`, para llo, llame a la función [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a256e7b317fbf762c4f8f5023a2cd8cb9) en la clase base [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html). Por último, muestre los datos del archivo descargado.

Agregue este código al final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DownloadFile":::

### <a name="delete-a-file"></a>Eliminación de un archivo

El código siguiente elimina el archivo del recurso compartido de archivos de Azure Storage, para lo que llama a la función [ShareFileClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a86036b445dce67a96f7bf6c45f163f59).

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFile":::

### <a name="delete-a-files-share"></a>Eliminación de un recurso compartido de archivos

El código siguiente limpia los recursos que creó la aplicación; para ello, elimina todo el recurso compartido de archivos mediante [ShareClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a9915630503f1675b5f49eb9c01ca2601).

Agregue este código al final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFilesShare":::

## <a name="run-the-code"></a>Ejecución del código

Esta aplicación crea un contenedor y carga un archivo de texto en Azure Blob Storage. Luego, en el ejemplo se enumeran los blobs del contenedor, se descarga el archivo y se muestra su contenido. Por último, la aplicación elimina tanto el blob como el contenedor.

La salida de la aplicación es similar a la del ejemplo siguiente:

```output
Azure Files Shares storage v12 - C++ quickstart sample
Creating files share: sample-share
Uploading file: sample-file
Listing file metadata...
key1:value1
key2:value2
Downloaded file contents: Hello Azure!
Deleting file: sample-file
Deleting files share: sample-share
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a cargar, descargar y enumerar archivos mediante C++. También ha aprendido a crear y eliminar un recurso compartido de archivos de Azure Storage.

Para ver un ejemplo de Blob Storage para C++, siga estos pasos:

> [!div class="nextstepaction"]
> [Ejemplos de SDK del recurso compartido de archivos de Azure Storage v12 para C++](https://github.com/Azure/azure-sdk-for-cpp/tree/main/sdk/storage/azure-storage-files-shares/sample)
