---
title: Introducción al Explorador de Storage | Microsoft Docs
description: Comience a administrar recursos de Azure Storage con el Explorador de Storage. Descargue e instale el Explorador de Azure Storage, conéctese a una cuenta de almacenamiento o servicio, y mucho más.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 969e2d2424b9f9af1dcd921a1cebfa9407e313eb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438427"
---
# <a name="get-started-with-storage-explorer"></a>Introducción al Explorador de Storage

## <a name="overview"></a>Información general

Explorador de Microsoft Azure Storage es una aplicación independiente que facilita el trabajo con los datos de Azure Storage en Windows, macOS y Linux.

En este artículo aprenderá varias maneras de conectarse a cuentas de Azure Storage y de administrarlas.

:::image type="content" alt-text="Explorador de Microsoft Azure Storage" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Requisitos previos

# <a name="windows"></a>[Windows](#tab/windows)

El Explorador de Storage es compatible con las siguientes versiones de Windows:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

En todas las versiones de Windows, el Explorador de Storage requiere como mínimo .NET Framework 4.7.2.

# <a name="macos"></a>[macOS](#tab/macos)

El Explorador de Storage es compatible con las siguientes versiones de macOS:

* macOS 10.12 Sierra y versiones posteriores

# <a name="linux"></a>[Linux](#tab/linux)

El Explorador de Storage está disponible en [Snap Store](https://snapcraft.io/storage-explorer) para las distribuciones más comunes de Linux. Para esta instalación, se recomienda Snap Store. El complemento de Explorador de Storage instala todas sus dependencias y actualizaciones cuando se publican nuevas versiones en Snap Store.

Para saber cuáles son las distribuciones admitidas, consulte la [`snapd`página de instalación](https://snapcraft.io/docs/installing-snapd).

El Explorador de Storage requiere que se use un administrador de contraseñas. Por consiguiente, es posible que tenga que conectarse a un administrador de contraseñas manualmente. Puede conectar el Explorador de Storage al administrador de contraseñas del sistema mediante la ejecución del siguiente comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

El Explorador de Storage también se puede descargar como *.tar.gz*. Si usa *.tar.gz*, debe instalar las dependencias manualmente. Las siguientes distribuciones de Linux admiten la instalación mediante *.tar.gz*:

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

La instalación mediante *.tar.gz* podría funcionar en otras distribuciones, pero las únicas oficialmente compatibles son las que aparecen en la lista.

Para más información sobre la instalación del Explorador de Storage en Linux, consulte el apartado acerca de las [dependencias de Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) en la guía para la solución de problemas del Explorador de Azure Storage.

---

## <a name="download-and-install"></a>Descargar e instalar

Para descargar e instalar el Explorador de Storage, consulte [Explorador de Azure Storage](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Conexión a una cuenta de almacenamiento o servicio

El Explorador de Storage proporciona varias maneras de conectar con los recursos de Azure:

* [Iniciar sesión en Azure para tener acceso a las suscripciones y sus recursos](#sign-in-to-azure)
* [Asociar a un recurso de Azure Storage individual](#attach-to-an-individual-resource)
* [Adjuntar a un recurso de CosmosDB](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

> [!NOTE]
> Para obtener acceso total a los recursos tras el inicio de sesión, el Explorador de Storage requiere permisos de administración (Azure Resource Manager) y en el nivel de datos. Esto significa que necesita permisos de Azure Active Directory (Azure AD) que proporcionen acceso a la cuenta de almacenamiento, los contenedores de la cuenta y los datos de los contenedores. Si solo tiene permisos en la capa de datos, considere la posibilidad de elegir la opción **Sign in using Azure Active Directory (Azure AD)** [Iniciar sesión con Azure Active Directory (Azure AD)] al asociar a un recurso. Para más información sobre los permisos específicos que requiere el Explorador de Storage, consulte la [guía para la solución de problemas del Explorador de Azure Storage](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. En el Explorador de Storage, seleccione **Ver** > **Administración de cuentas**, o bien seleccione el botón **Administrar cuentas**.

    :::image type="content" alt-text="Administración de cuentas" source ="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-manage-accounts.png":::

1. En **ADMINISTRACIÓN DE CUENTAS** ahora se muestran todas las cuentas de Azure en las que ha iniciado sesión. Para conectarse a otra cuenta, seleccione **Agregar una cuenta**.

1. Se abre el cuadro de diálogo **Conectar a Azure Storage**. En el panel **Seleccionar recurso**, elija **Suscripción**.

    :::image type="content" alt-text="Cuadro de diálogo Conectar" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-dialog.png":::

1. En el panel **Seleccionar entorno de Azure**, elija un entorno de Azure en el que iniciar sesión. Puede iniciar sesión en Azure global, en una nube nacional o en una instancia de Azure Stack. Luego, seleccione **Siguiente**.

    :::image type="content" alt-text="Opción para iniciar sesión" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Para más información sobre Azure Stack, consulte [Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento](/azure-stack/user/azure-stack-storage-connect-se).

1. El Explorador de Storage abrirá una página web para que inicie sesión.

1. Después de iniciar sesión correctamente con una cuenta de Azure, tanto la cuenta como las suscripciones de Azure asociadas con esa cuenta aparecen en **ADMINISTRACIÓN DE CUENTAS**. Elija las suscripciones de Azure con las que desea trabajar y luego seleccione **Aplicar**.

    :::image type="content" alt-text="Selección de suscripciones de Azure" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-account-panel.png":::

1. **EXPLORADOR** muestra las cuentas de almacenamiento asociadas a las suscripciones de Azure seleccionadas.

    :::image type="content" alt-text="Suscripciones de Azure seleccionadas" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Asociación a un recurso individual

El Explorador de Storage le permite conectarse a recursos individuales, como un contenedor Azure Data Lake Storage Gen2, mediante distintos métodos de autenticación. Algunos métodos de autenticación solo se admiten para determinados tipos de recursos.

| Tipo de recurso    | Azure AD | Nombre y clave de la cuenta | Firma de acceso compartido (SAS)  | Público (anónimo) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Cuentas de almacenamiento | Sí      | Sí                  | Sí (cadena de conexión o dirección URL) | No                 |
| Contenedores de blobs  | Sí      | No                   | Sí (URL)                      | Sí                |
| Contenedores de segunda generación  | Sí      | No                   | Sí (URL)                      | Sí                |
| Directorios de segunda generación | Sí      | No                   | Sí (URL)                      | Sí                |
| Recursos compartidos de archivos      | No       | No                   | Sí (URL)                      | No                 |
| Colas           | Sí      | No                   | Sí (URL)                      | No                 |
| Tablas           | No       | No                   | Sí (URL)                      | No                 |
 
El Explorador de Storage también puede conectarse a un [emulador de almacenamiento local](#local-storage-emulator) mediante los puertos configurados del emulador.

Para conectarse a un recurso individual, seleccione el botón **Conectar** en la barra de herramientas de la izquierda. A continuación, siga las instrucciones del tipo de recurso al que desea conectarse.

:::image type="content" alt-text="Opción de conexión a Almacenamiento de Azure" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-button.png":::

Si se agrega correctamente una conexión a una cuenta de almacenamiento, aparecerá un nuevo nodo de árbol en **Local y asociado** > **Cuentas de almacenamiento**.

En el caso de otros tipos de recursos, se agrega un nuevo nodo en **Local y asociado** > **Cuentas de almacenamiento** >  **(Contenedores asociados)** . El nodo aparecerá bajo un nodo de grupo coincidente con su tipo. Por ejemplo, una nueva conexión a un contenedor de Azure Data Lake Storage Gen2 aparecerá en **Contenedores de blobs**.

Si el Explorador de Storage no ha podido agregar la conexión o si no puede acceder a los datos después de agregar correctamente la conexión, consulte la [guía de solución de problemas del Explorador de Azure Storage](./storage/common/storage-explorer-troubleshooting.md).

En las secciones siguientes se describen los distintos métodos de autenticación que puede usar para conectarse a recursos individuales.

#### <a name="azure-ad"></a>Azure AD

El Explorador de Storage puede usar su cuenta de Azure para conectarse a los siguientes tipos de recursos:
* Contenedores de blobs
* Contenedores de Azure Data Lake Storage Gen2
* Directorios de Azure Data Lake Storage Gen2
* Colas
 
Azure AD es la opción preferida si tiene acceso de capa de datos al recurso, pero no tiene acceso de capa de administración.

1. Inicie sesión en al menos una cuenta de Azure siguiendo los [pasos descritos anteriormente](#sign-in-to-azure).
1. En el panel **Seleccionar recurso** del cuadro de diálogo **Conectar a Azure Storage**, seleccione **Contenedor de blobs**, **Contenedor de ADLS Gen2** o **Cola**.
1. Seleccione **Sign in using Azure Active Directory (Azure AD)** [Iniciar sesión con Azure Active Directory (Azure AD)] y, a continuación, **Siguiente**.
1. Seleccione una cuenta de Azure y un inquilino. La cuenta y el inquilino deben tener acceso al recurso de Storage al que desea asociarse. Seleccione **Next** (Siguiente).
1. Escriba un nombre para mostrar para la conexión y la dirección URL del recurso. Seleccione **Next** (Siguiente).
1. Revise la información de conexión en el panel de **Resumen**. Si la información de la conexión es correcta, seleccione **Conectar**.

#### <a name="account-name-and-key"></a>Nombre y clave de la cuenta

El Explorador de Storage puede conectarse a una cuenta de almacenamiento mediante el nombre y la clave de la cuenta de almacenamiento.

Puede encontrar las claves de su cuenta en [Azure Portal](https://portal.azure.com). Abra la página de la cuenta de almacenamiento y seleccione **Configuración** > **Claves de acceso**.

1. En el panel **Seleccionar recurso** del cuadro de diálogo **Conectar a Azure Storage**, seleccione **Cuenta de almacenamiento**.
1. Seleccione **Account name and key** (Nombre y clave de la cuenta) y, a continuación, seleccione **Siguiente**.
1. Escriba un nombre para mostrar para la conexión, el nombre de la cuenta y una de las claves de cuenta. Seleccione el entorno de Azure adecuado. Seleccione **Next** (Siguiente).
1. Revise la información de conexión en el panel de **Resumen**. Si la información de la conexión es correcta, seleccione **Conectar**.

#### <a name="shared-access-signature-sas-connection-string"></a>Cadena de conexión de la firma de acceso compartido (SAS)

El Explorador de Storage puede conectarse a una cuenta de almacenamiento mediante una cadena de conexión con una firma de acceso compartido (SAS). La cadena de conexión de SAS tiene el siguiente aspecto:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. En el panel **Seleccionar recurso** del cuadro de diálogo **Conectar a Azure Storage**, seleccione **Cuenta de almacenamiento**.
1. Seleccione **Firma de acceso compartido (SAS)** y, a continuación, **Siguiente**.
1. Escriba un nombre para mostrar para la conexión y la cadena de conexión de SAS de la cuenta de almacenamiento. Seleccione **Next** (Siguiente).
1. Revise la información de conexión en el panel de **Resumen**. Si la información de la conexión es correcta, seleccione **Conectar**.

#### <a name="shared-access-signature-sas-url"></a>URL de la firma de acceso compartido (SAS)

El Explorador de Storage puede conectarse a los siguientes tipos de recursos mediante un URI de SAS:
* Contenedor de blobs
* Contenedor o directorio de Azure Data Lake Storage Gen2
* Recurso compartido de archivos
* Cola
* Tabla

Un identificador URI de SAS tiene el siguiente aspecto:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. En el panel **Seleccionar recurso** del cuadro de diálogo **Conectar a Azure Storage**, seleccione el recurso al que desea conectarse.
1. Seleccione **Firma de acceso compartido (SAS)** y, a continuación, **Siguiente**.
1. Escriba un nombre para mostrar para la conexión y el URI de SAS para el recurso. Seleccione **Next** (Siguiente).
1. Revise la información de conexión en el panel de **Resumen**. Si la información de la conexión es correcta, seleccione **Conectar**.

#### <a name="local-storage-emulator"></a>Emulador de almacenamiento local

El Explorador de Storage puede conectarse a un emulador de Azure Storage. En la actualidad se admiten dos emuladores:

* [Emulador de Azure Storage](storage/common/storage-use-emulator.md) (solo Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS o Linux)

Si el emulador escucha en los puertos predeterminados, puede usar el nodo **Local y asociado** > **Cuentas de almacenamiento** > **Emulator - Default Ports** (Emulador: Puertos predeterminados) para acceder rápidamente al emulador.

Si quiere usar otro nombre para la conexión o si el emulador no se está ejecutando en los puertos predeterminados:

1. Inicie el emulador.

   > [!IMPORTANT]
   > Explorador de Storage no inicia automáticamente el emulador. Debe iniciarlo manualmente.

1. En el panel **Seleccionar recurso** del cuadro de diálogo **Conectar a Azure Storage**, seleccione **Emulador de almacenamiento local**.
1. Escriba un nombre para mostrar para la conexión y el número de puerto de cada servicio emulado que desee usar. Si no desea usar un servicio, deje en blanco el puerto correspondiente. Seleccione **Next** (Siguiente).
1. Revise la información de conexión en el panel de **Resumen**. Si la información de la conexión es correcta, seleccione **Conectar**.

### <a name="connect-to-azure-cosmos-db"></a>Conexión a Azure Cosmos DB

El Explorador de Storage también admite la conexión a recursos de Azure Cosmos DB.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conexión a una cuenta de Azure Cosmos DB mediante una cadena de conexión

En lugar de administrar cuentas de Azure Cosmos DB mediante una suscripción de Azure, puede conectarse a Azure Cosmos DB mediante una cadena de conexión. Para conectarse, siga estos pasos:

1. En **EXPLORADOR**, expanda **Local y asociado**, haga clic con el botón derecho en **Cuentas de Azure Cosmos DB** y seleccione **Conectar a Azure Cosmos DB**.

    ![Conectarse a Azure Cosmos DB mediante una cadena de conexión][21]

1. Seleccione la API de Azure Cosmos DB, escriba los datos de la **cadena de conexión** y, a continuación, seleccione **Aceptar** para conectarse a la cuenta de Azure Cosmos DB. Para más información sobre cómo recuperar la cadena de conexión, consulte [Administración de una cuenta de Azure Cosmos](./cosmos-db/how-to-manage-database-account.md).

    ![Cadena de conexión][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Conexión a Azure Data Lake Store mediante el URI

Puede acceder a un recurso que no esté en su suscripción. Necesita que un usuario que tenga acceso a ese recurso le proporcione el identificador URI del recurso. Después de iniciar sesión, conéctese a Data Lake Store mediante el identificador URI. Para conectarse, siga estos pasos:

1. En **EXPLORADOR**, expanda **Local y asociado**.

1. Haga clic con el botón derecho en **Data Lake Storage Gen1** y seleccione **Conectar a Data Lake Storage Gen1**.

    ![Menú contextual Connect to Data Lake Store (Conectar a Data Lake Store)](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Escriba el identificador URI y seleccione **Aceptar**. Data Lake Store aparece en **Data Lake Storage**.

    ![Resultado de Connect to Data Lake Store (Conectar a Data Lake Store)](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

En este ejemplo se usa Data Lake Storage Gen1. Azure Data Lake Storage Gen2 ya está disponible. Para más información, consulte [¿Qué es Azure Data Lake Storage Gen1?](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generación de una firma de acceso compartido en el Explorador de Storage<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Firma de acceso compartido en el nivel de cuenta

1. Haga clic con el botón derecho en la cuenta de Storage que quiere compartir y seleccione **Obtener firma de acceso compartido**.

    ![Opción de menú contextual Obtener firma de acceso compartido][14]

1. En **Firma de acceso compartido**, especifique el período y los permisos que desee para la cuenta y seleccione **Crear**.

    ![Obtener una firma de acceso compartido][15]

1. Copie la **cadena de conexión** o la **cadena de consulta** sin formato en el Portapapeles.

### <a name="service-level-shared-access-signature"></a>Firma de acceso compartido en el nivel de servicio

Puede obtener una firma de acceso compartido en el nivel de servicio. Para más información, consulte [Obtención de la SAS para un contenedor de blobs](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Búsqueda de cuentas de almacenamiento

Para buscar un recurso de almacenamiento, puede buscar en el panel **EXPLORADOR**.

A medida que escribe texto en el cuadro de búsqueda, el Explorador de Storage muestra todos los recursos que coinciden con el valor de búsqueda especificado hasta ese punto. En este ejemplo se muestra la búsqueda de **puntos de conexión**:

![Búsqueda de cuenta de almacenamiento][23]

> [!NOTE]
> Para agilizar la búsqueda, use **Administración de cuentas** para anular la selección de las suscripciones que no contengan el elemento que busca. También puede hacer clic con el botón derecho en un nodo y seleccionar **Buscar desde aquí** para comenzar a buscar desde un nodo específico.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de recursos de Azure Blob Storage con el Explorador de Storage](vs-azure-tools-storage-explorer-blobs.md)
* [Uso de datos con el Explorador de Azure Storage](./cosmos-db/storage-explorer.md)
* [Administración de recursos de Azure Data Lake Store en el Explorador de Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
