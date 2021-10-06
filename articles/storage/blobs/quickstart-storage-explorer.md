---
title: 'Inicio rápido: Creación de un blob con el Explorador de Azure Storage'
titleSuffix: Azure Storage
description: Aprenda a usar el Explorador de Azure Storage para crear un contenedor y un blob, descargar el blob en el equipo local y ver todos los blobs del contenedor.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: tamram
ms.openlocfilehash: abf75bb39610deb54b72002cde3dd8e28131ed94
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839135"
---
# <a name="quickstart-use-azure-storage-explorer-to-create-a-blob"></a>Inicio rápido: Uso del Explorador de Azure Storage para crear un blob

En esta guía de inicio rápido, aprenderá a usar el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para crear un contenedor y un blob. A continuación, aprenderá a descargar el blob en un equipo local y a ver todos los blobs en un contenedor. También aprenderá a crear una instantánea de un blob, a administrar directivas de acceso al contenedor y a crear una firma de acceso compartido.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Esta guía de inicio rápido requiere que se instale el Explorador de Azure Storage. Para instalar el Explorador de Azure Storage para Windows, Macintosh o Linux, consulte [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="log-in-to-storage-explorer"></a>Inicie sesión en el Explorador de Microsoft Azure Storage

La primera vez, se muestra la ventana **Explorador de Microsoft Azure Storage: Conectar**. El Explorador de Microsoft Azure Storage proporciona varias maneras de conectar con las cuentas de almacenamiento. En la tabla siguiente se enumeran las distintas maneras en que puede conectarse:

|Tarea|Propósito|
|---|---|
|Agregar una cuenta de Azure | Le redirige a la página de inicio de sesión de su organización para autenticarlo en Azure. |
|Usar una cadena de conexión o un identificador URI de firma de acceso compartido | Puede utilizarse para tener acceso directamente a un contenedor o a la cuenta de almacenamiento con un token de SAS o una cadena de conexión compartida. |
|Usar un nombre y clave de la cuenta de almacenamiento| Use el nombre y la clave de la cuenta de almacenamiento para conectarse a Azure Storage.|

Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y haga clic en **Iniciar sesión**. Siga las indicaciones de la pantalla para registrarse en su cuenta de Azure.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-connect.png" alt-text="Captura de pantalla que muestra la ventana de conexión del Explorador de Microsoft Azure Storage":::

Una vez que el Explorador de Storage finaliza la conexión, muestra la pestaña **Explorador**. Esta vista proporciona una visión general de todas las cuentas de Azure Storage, así como del almacenamiento local que se configuró mediante el [emulador de almacenamiento Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), las cuentas de [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) o los entornos de [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-main-page.png" alt-text="Captura de pantalla que muestra la página principal del Explorador de Storage principal":::

## <a name="create-a-container"></a>Creación de un contenedor

Para crear un contenedor, expanda la cuenta de almacenamiento que creó en el paso anterior. Seleccione **Contenedor de blobs**, haga clic con el botón derecho y seleccione **Crear contenedor de blobs**. Escriba el nombre del contenedor de blobs. Para ver una lista de reglas y restricciones en la nomenclatura de contenedores de blobs, consulte la sección [Crear un contenedor](storage-quickstart-blobs-dotnet.md#create-a-container). Cuando haya finalizado, presione **Entrar** para crear el contenedor de blobs. Una vez que el contenedor de blobs se haya creado correctamente, se mostrará en la carpeta **Contenedores de blobs** de la cuenta de almacenamiento seleccionada.

## <a name="upload-blobs-to-the-container"></a>Carga de blobs al contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los archivos VHD utilizados para respaldar VM IaaS son blobs en páginas. Los blobs en anexos se utilizan para el registro, por ejemplo, cuando desea escribir en un archivo y luego sigue agregando más información. La mayoría de los archivos almacenados en Blob Storage son blobs en bloques.

En la cinta de opciones de contenedor, seleccione **Cargar**. Esta operación da la opción de cargar un archivo o una carpeta.

Elija los archivos o carpetas para cargar. Seleccione el **tipo de blob**. Opciones aceptables son **Anexar**, **Paginar** o **Bloquear** blob.

Si carga un archivo .vhd o .vhdx, elija **Upload .vhd/.vhdx files as page blobs (recommended)** [Cargar archivos.vhd/.vhdx como blobs de páginas (recomendado)].

En el campo **Cargar en carpeta (opcional)** , un nombre de carpeta para almacenar los archivos o carpetas en una carpeta bajo el contenedor. Si no se elige ninguna carpeta, los archivos se cargan directamente en el contenedor.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-upload-blob.png" alt-text="Explorador de Microsoft Azure Storage: Carga de un blob":::

Cuando se selecciona **Aceptar**, los archivos seleccionados se ponen en cola para cargar y se cargan de uno en uno. Una vez finalizada la carga, los resultados se muestran en la ventana **Actividades**.

## <a name="view-blobs-in-a-container"></a>Visualización de los blobs de un contenedor

En la aplicación **Explorador de Azure Storage**, seleccione un contenedor en una cuenta de almacenamiento. El panel principal muestra una lista de los blobs en el contenedor seleccionado.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-list-blobs.png" alt-text="Captura de pantalla que muestra cómo seleccionar un contenedor en el Explorador de Microsoft Azure Storage":::

## <a name="download-blobs"></a>Descargar blobs

Para descargar los blobs mediante el **Explorador de Microsoft Azure Storage**, con un blob seleccionado, seleccione **Descargar** desde la cinta de opciones. Se abre un cuadro de diálogo de archivo que permite escribir un nombre de archivo. Seleccione **Guardar** para iniciar la descarga de un blob en la ubicación local.

## <a name="manage-snapshots"></a>Administración de instantáneas

El Explorador de Microsoft Azure Storage permite tomar y administrar [instantáneas](./snapshots-overview.md) de los blobs. Para tomar una instantánea de un blob, haga clic con el botón derecho en el blob y seleccione **Create Snapshot** (Crear instantánea). Para ver las instantáneas de un blob, haga clic con el botón derecho en el blob y seleccione **Manage Snapshots** (Administrar instantáneas). Una lista de las instantáneas para el blob se muestran en la pestaña actual.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-snapshots.png" alt-text="Captura de pantalla que muestra cómo tomar y administrar instantáneas de blobs":::

## <a name="generate-a-shared-access-signature"></a>Generación de una firma de acceso compartido

Puede usar el Explorador de Storage para generar firmas de acceso compartido (SAS). Haga clic con el botón derecho en una cuenta de almacenamiento, contenedor o blob, y elija **Get Shared Access Signature...**  (Obtener firma de acceso compartido). Elija la hora de inicio y de expiración, y los permisos para la dirección URL de la firma de acceso compartido y seleccione **Crear**. El Explorador de Storage genera el token de SAS con los parámetros especificados y lo muestra para copiarlo.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-shared-access-signature.png" alt-text="Captura de pantalla que muestra cómo generar una SAS":::

Al crear una SAS para una cuenta de almacenamiento, el Explorador de Storage genera una SAS de cuenta. Para más información sobre la SAS de cuenta, consulte [Creación de una SAS de cuenta](/rest/api/storageservices/create-account-sas).

Al crear una SAS para un contenedor o un blob, el Explorador de Storage genera una SAS de servicio. Para más información sobre la SAS de servicio, consulte [Creación de una SAS de servicio](/rest/api/storageservices/create-service-sas).

> [!NOTE]
> Cuando se crea una SAS con el Explorador de Storage, la SAS siempre se asigna con la clave de la cuenta de almacenamiento. El Explorador de Storage no admite actualmente la creación de una SAS de delegación de usuario, que es una SAS que está firmada con credenciales de Azure AD.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido aprendió a transferir archivos entre un disco local y Azure Blob Storage mediante el **Explorador de Microsoft Azure Storage**. Para más información sobre cómo trabajar con Blob Storage, continúe con la introducción a Blob Storage.

> [!div class="nextstepaction"]
> [Introducción a Azure Blob Storage](./storage-blobs-introduction.md)