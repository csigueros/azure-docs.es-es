---
title: Uso del Explorador de Azure Storage con Azure Data Lake Storage Gen2
description: Use el Explorador de Azure Storage para administrar directorios y listas de control de acceso (ACL) de archivos y directorios en cuentas de almacenamiento que tengan habilitado el espacio de nombres jerárquico (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5307a56ba2384f9e0294634530823238fd903859
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766756"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Uso del Explorador de Azure Storage para administrar directorios y archivos en Azure Data Lake Storage Gen2

En este artículo se explica cómo usar el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para crear y administrar directorios y archivos en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico (HNS).

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Una cuenta de almacenamiento que tenga habilitado el espacio de nombres jerárquico (HNS). Siga [estas](../common/storage-account-create.md) instrucciones para crear uno.

- Explorador de Azure Storage instalado en el equipo local. Para instalar el Explorador de Azure Storage para Windows, Macintosh o Linux, consulte [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Al trabajar con Azure Data Lake Storage Gen2, el Explorador de Storage hace uso de los [puntos de conexión](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) de Blob (blob) y Data Lake Storage Gen2 (DFS). Si el acceso a Azure Data Lake Storage Gen2 se configura mediante puntos de conexión privados, asegúrese de que se crean dos puntos de conexión privados para la cuenta de almacenamiento: uno con el subrecurso de destino `blob` y el otro con el subrecurso de destino `dfs`.

## <a name="sign-in-to-storage-explorer"></a>Inicio de sesión en el Explorador de Storage

Cuando se inicia por primera vez el Explorador de Storage, aparece la ventana **Explorador de Microsoft Azure Storage - Conectar**. Si bien el Explorador de Storage proporciona varias formas de conectarse a las cuentas de almacenamiento, actualmente solo se admite una para administrar las ACL.

|Tarea|Propósito|
|---|---|
|Agregar una cuenta de Azure | Le redirige a la página de inicio de sesión de su organización para autenticarlo en Azure. Actualmente, es el único método de autenticación compatible si quiere administrar y establecer las ACL.|
|Usar una cadena de conexión o un identificador URI de firma de acceso compartido | Puede utilizarse para tener acceso directamente a un contenedor o a la cuenta de almacenamiento con un token de SAS o una cadena de conexión compartida. |
|Usar un nombre y clave de la cuenta de almacenamiento| Use el nombre y la clave de la cuenta de almacenamiento para conectarse a Azure Storage.|

Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y haga clic en **Iniciar sesión**. Siga las indicaciones de la pantalla para registrarse en su cuenta de Azure.

![Captura de pantalla que muestra el Explorador de Microsoft Azure Storage y resalta la opción Add an Azure Account (Agregar una cuenta de Azure) y el botón Sign-in (Iniciar sesión).](media/quickstart-storage-explorer/storage-explorer-connect.png)

Cuando se completa la conexión, el Explorador de Microsoft Azure Storage se carga y se muestra la pestaña **Explorador**. Esta vista proporciona una visión general de todas las cuentas de Azure Storage, así como del almacenamiento local que se configuró mediante el [emulador de almacenamiento Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), las cuentas de [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) o los entornos de [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Explorador de Microsoft Azure Storage: ventana Conectar](media/quickstart-storage-explorer/storage-explorer-main-page.png)

## <a name="create-a-container"></a>Crear un contenedor

Un contenedor contiene directorios y archivos. Para crear uno, expanda la cuenta de almacenamiento que creó en el paso anterior. Seleccione **Contenedores de blob**, haga clic con el botón derecho y seleccione **Crear contenedor de blobs**. Escriba el nombre del contenedor. Para ver una lista de reglas y restricciones en la nomenclatura de contenedores, consulte la sección [Crear un contenedor](storage-quickstart-blobs-dotnet.md#create-a-container). Cuando haya finalizado, presione **Entrar** para crear el contenedor. Una vez que el contenedor se ha creado correctamente, se muestra en la carpeta **Contenedores de blobs** de la cuenta de almacenamiento seleccionada.

![Explorador de Microsoft Azure Storage: creación de un contenedor](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Creación de un directorio

Para crear un directorio, seleccione el contenedor que creó en el paso anterior. En la cinta de opciones del contenedor, elija el botón **Nueva carpeta**. Escriba el nombre del directorio. Cuando haya finalizado, presione **Entrar** para crear el directorio. Cuando el directorio se ha creado correctamente, aparece en la ventana del editor.

![Explorador de Microsoft Azure Storage: creación de un directorio](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Carga de blobs en el directorio

En la cinta de opciones del directorio, elija el botón **Cargar**. Esta operación da la opción de cargar un archivo o una carpeta.

Elija los archivos o carpetas para cargar.

![Explorador de Microsoft Azure Storage: Carga de un blob](media/data-lake-storage-explorer/upload-file.png)

Cuando se selecciona **Aceptar**, los archivos seleccionados se ponen en cola para cargar y se cargan de uno en uno. Una vez finalizada la carga, los resultados se muestran en la ventana **Actividades**.

## <a name="view-blobs-in-a-directory"></a>Visualización de blobs en un directorio

En la aplicación **Explorador de Azure Storage**, seleccione un directorio en una cuenta de almacenamiento. El panel principal muestra una lista de los blobs en el directorio seleccionado.

![Explorador de Microsoft Azure Storage: enumeración de los blobs de un directorio](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Descargar blobs

Para descargar los archivos mediante el **Explorador de Azure Storage**, con un archivo seleccionado, seleccione **Descargar** desde la cinta de opciones. Se abre un cuadro de diálogo de archivo que permite escribir un nombre de archivo. Seleccione **Guardar** para iniciar la descarga de un archivo en la ubicación local.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a administrar permisos de archivos y directorios mediante la configuración de listas de control de acceso (ACL).

> [!div class="nextstepaction"]
> [Uso del Explorador de Azure Storage para administrar listas de control de acceso en Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
