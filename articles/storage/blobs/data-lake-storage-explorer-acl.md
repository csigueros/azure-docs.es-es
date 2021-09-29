---
title: 'Explorador de Azure Storage: establecimiento de ACL en Azure Data Lake Storage Gen2'
description: Use el Explorador de Azure Storage para administrar listas de control de acceso (ACL) en cuentas de almacenamiento que tengan habilitado el espacio de nombres jerárquico (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: d97df947309f9ccc9886e45878cfc91ea6423b23
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590373"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Uso del Explorador de Azure Storage para administrar listas de control de acceso en Azure Data Lake Storage Gen2

En este artículo se muestra cómo usar el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para administrar listas de control de acceso (ACL) en cuentas de almacenamiento que tengan habilitado el espacio de nombres jerárquico (HNS).

El Explorador de Storage se puede usar para ver y actualizar las listas de control de acceso de directorios y archivos. La herencia de ACL ya está disponible para los nuevos elementos secundarios que se crean en un directorio primario. Pero también se puede la configuración de las listas de control de acceso de forma recursiva en los elementos secundarios existentes de un directorio primario sin tener que realizar estos cambios individualmente para cada elemento secundario.

En este artículo se muestra cómo modificar la lista de control de acceso de un archivo o directorio y cómo aplicar la configuración de la lista de control de acceso de forma recursiva a los directorios secundarios.

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

## <a name="manage-an-acl"></a>Administración de una lista de control de acceso

Haga clic con el botón derecho en el contenedor, en un directorio o en un archivo y, a continuación, haga clic en **Administrar listas de control de acceso**.  En la captura de pantalla siguiente se muestra el menú que aparece al hacer clic con el botón derecho en un directorio.

> [!div class="mx-imgBorder"]
> ![Hacer clic con el botón derecho en un directorio en el Explorador de Azure Storage](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

El cuadro de diálogo **Administrar acceso** permite administrar los permisos de propietario y del grupo de propietarios. También permite agregar usuarios y grupos nuevos a la lista de control de acceso para la que quiere administrar los permisos.

> [!div class="mx-imgBorder"]
> ![Cuadro de diálogo Administrar acceso](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Seleccione el botón **Agregar** para agregar un usuario o grupo nuevo a la lista de control de acceso. Luego, escriba la entrada de Azure Active Directory (Azure AD) correspondiente que quiere agregar a la lista y, luego, seleccione **Agregar**.  El usuario o grupo ahora aparecerá en el campo **Usuarios y grupos:** , lo que le permitirá empezar a administrar sus permisos.

> [!NOTE]
> Se trata de un procedimiento recomendado para crear un grupo de seguridad en Azure AD y mantener los permisos en el grupo en lugar de usuarios individuales. Para obtener más detalles sobre esta recomendación, así como otros procedimientos recomendados, consulte los [Modelo de control de acceso de Azure Data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

Use los controles de casilla para establecer el acceso y las ACL predeterminadas. Para obtener más información sobre la diferencia entre estos tipos de ACL, consulte [Tipos de ACL](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>Aplicación de ACL de forma recursiva

Puede aplicar entradas de ACL de forma recursiva en los elementos secundarios existentes de un directorio primario sin tener que realizar estos cambios individualmente para cada elemento secundario.

Para aplicar las entradas de ACL de forma recursiva, haga clic con el botón derecho en el contenedor o en un directorio y, a continuación, haga clic en **Propagar las listas de control de acceso**.  En la captura de pantalla siguiente se muestra el menú que aparece al hacer clic con el botón derecho en un directorio.

> [!div class="mx-imgBorder"]
> ![Hacer clic con el botón derecho en un directorio y elegir la opción Propagar control de acceso](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre el modelo de permisos de Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Modelo de control de acceso de Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)
