---
title: Administración de recursos de Azure Cosmos DB mediante el Explorador de Azure Storage
description: Obtenga información sobre cómo conectarse a Azure Cosmos DB y administrar sus recursos con el Explorador de Azure Storage.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: 447ebaac46235d551236b0b354acbacb67df0d07
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397146"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Administración de recursos de Azure Cosmos DB mediante el Explorador de Azure Storage
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Puede usar el Explorador de Azure Storage para conectarse a Azure Cosmos DB. Le permite conectarse a cuentas de Azure Cosmos DB hospedadas en Azure y nubes independientes desde Windows, macOS o Linux.

Ahora puede usar la misma herramienta para administrar las diferentes entidades de Azure en un solo lugar. Puede administrar entidades de Azure Cosmos DB, manipular datos, actualizar procedimientos y desencadenadores almacenados, además de otras entidades de Azure como los blobs y las colas de Storage. Actualmente, el Explorador de Azure Storage admite cuentas de Cosmos que estén configuradas para las API de SQL, MongoDB, Graph y Table.

> [!NOTE]
> La integración de Azure Cosmos DB con Explorador de Storage ha quedado en desuso. Cualquier funcionalidad existente no se quitará durante un año como mínimo a partir de esta versión. En su lugar, debe usar [Azure Portal](https://portal.azure.com/), la [aplicación de escritorio de Azure Portal](https://portal.azure.com/App/Download) o el [Explorador de Azure Cosmos DB](data-explorer.md) independiente. Las opciones alternativas contienen muchas características nuevas que actualmente no se admiten en Explorador de Storage.

## <a name="prerequisites"></a>Requisitos previos

Una cuenta de Cosmos con las API de SQL o Azure Cosmos DB para MongoDB. Si no tiene una cuenta, puede crear una gratis en Azure Portal. Consulte [Azure Cosmos DB: Compilación de una aplicación web de SQL API con .NET y Azure Portal](create-sql-api-dotnet.md) para más información.

## <a name="installation"></a>Instalación

Para instalar los bits más recientes del Explorador de Azure Storage, consulte [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Se admiten las versiones de Windows, Linux y macOS.

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure

1. Después de instalar el **Explorador de Azure Storage**, seleccione el icono de **complemento** en el panel izquierdo.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Captura de pantalla que muestra el icono de complemento en el panel izquierdo.":::

1. Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y seleccione **Iniciar sesión**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Captura de pantalla de la ventana Conectar a Azure Storage que muestra el botón de radio Agregar una cuenta de Azure seleccionado y el menú desplegable Entorno de Azure.":::

1. En el cuadro de diálogo **Inicio de sesión de Azure**, seleccione **Iniciar sesión** y luego escriba las credenciales de Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Captura de pantalla de la ventana Iniciar sesión que muestra dónde escribir sus credenciales para su suscripción de Azure.":::

1. Seleccione la suscripción en la lista y luego seleccione **Aplicar**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Captura de pantalla del panel Administración de cuentas, que muestra una lista de suscripciones y el botón Aplicar.":::

    El panel del Explorador se actualiza y muestra las cuentas de la suscripción seleccionada.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Captura de pantalla del panel Explorador, actualizado para mostrar las cuentas de la suscripción seleccionada.":::

    La **cuenta de Cosmos DB** está conectada a su suscripción de Azure.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Uso de una cadena de conexión para conectarse a Azure Cosmos DB

Puede usar una cadena de conexión para conectarse a una instancia de Azure Cosmos DB. Este método solo es compatible con Table API y la API de SQL. Siga estos pasos para conectarse con una cadena de conexión:

1. Busque **Local and Attached** (Locales y conectados) en el árbol de la izquierda, haga clic con el botón derecho en **Cuentas de Cosmos DB** y seleccione **Conectarse a Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Captura de pantalla que muestra el menú desplegable después de hacer clic con el botón derecho, con la conexión a Azure Cosmos DB resaltada.":::

2. En la ventana **Conectarse a Cosmos DB**:
   1. Seleccione la API en el menú desplegable.
   1. Pegue la cadena de conexión en el cuadro **Cadena de conexión**. Para más información sobre cómo recuperar la cadena de conexión principal, consulte [Obtención de la cadena de conexión](manage-with-powershell.md#list-keys).
   1. Especifique una **Etiqueta de cuenta** y luego seleccione **siguiente** para comprobar el resumen.
   1. Seleccione **Conectar** para conectar la cuenta de Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Captura de pantalla de la ventana Conectarse a Cosmos DB, donde se muestra el menú desplegable API, el cuadro Cadena de conexión y el cuadro Etiqueta de cuenta.":::

> [!NOTE]
> Si el Explorador de Azure Storage muestra que la cadena de conexión de Azure Cosmos DB tiene un formato no válido, asegúrese de que la cadena de conexión tenga un punto y coma (`;`) al final. Un ejemplo de una cadena de conexión de Azure Cosmos DB válida sería: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Uso de un emulador local para conectarse a Azure Cosmos DB

Siga estos pasos para conectarse a Azure Cosmos DB con un emulador. Este método solo admite cuentas de SQL.

1. Instale Emulador de Cosmos DB y ábralo. Para más información sobre cómo instalar el emulador, consulte [Emulador de Cosmos DB](./local-emulator.md).

1. Busque **Local and Attached** (Locales y conectados) en el árbol de la izquierda, haga clic con el botón derecho en **Cuentas de Cosmos DB** y seleccione **Conectarse a Emulador de Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Captura de pantalla que muestra el menú que aparece después de hacer clic con el botón derecho, con la opción Conectarse a Emulador de Cosmos DB resaltada.":::

1. En la ventana **Conectarse a Cosmos DB**:
   1. Pegue la cadena de conexión en el cuadro **Cadena de conexión**. Para más información sobre cómo recuperar la cadena de conexión primaria, vea [Obtener la cadena de conexión](manage-with-powershell.md#list-keys).
   1. Especifique una **Etiqueta de cuenta** y luego seleccione **siguiente** para comprobar el resumen.
   1. Seleccione **Conectar** para conectar la cuenta de Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Captura de pantalla de la ventana Conectarse a Cosmos DB, donde se muestra el cuadro Cadena de conexión y el cuadro Etiqueta de cuenta.":::

## <a name="azure-cosmos-db-resource-management"></a>Administración de recursos de Azure Cosmos DB

Use las siguientes operaciones para administrar una cuenta de Azure Cosmos DB:

* Abrir la cuenta en Azure Portal.
* Agregar el recurso a la lista de acceso rápido.
* Buscar y actualizar recursos.
* Crear y eliminar bases de datos.
* Crear y eliminar colecciones.
* Crear, editar, eliminar y filtrar documentos.
* Administrar procedimientos almacenados, desencadenadores y funciones definidas por el usuario.

### <a name="quick-access-tasks"></a>Tareas de acceso rápido

Puede hacer clic con el botón derecho en una suscripción en el panel Explorador para realizar muchas tareas de acción rápida, por ejemplo:

* Haga clic con el botón derecho en una cuenta o una base de datos de Azure Cosmos DB y seleccione **Abrir en el portal** para administrar el recurso en el explorador en Azure Portal.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Captura de pantalla que muestra el menú que aparece después de hacer clic con el botón derecho, con la opción Abrir en el portal resaltada.":::

* Haga clic con el botón derecho en una cuenta, una base de datos o una colección de Azure Cosmos DB y seleccione **Agregar a Acceso rápido** para agregarla al menú de acceso rápido.

* **Buscar desde aquí** permite realizar búsquedas de palabras clave en la ruta de acceso seleccionada.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Captura de pantalla que muestra el cuadro de búsqueda resaltado.":::

### <a name="database-and-collection-management"></a>Administración de bases de datos y recopilaciones

#### <a name="create-a-database"></a>Crear una base de datos

1. Haga clic con el botón derecho en la cuenta de Azure Cosmos DB y seleccione **Crear base de datos**.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Captura de pantalla que muestra el menú que aparece después de hacer clic con el botón derecho, con la opción Crear base de datos resaltada.":::

1. Escriba el nombre de la base de datos y presione **Entrar** para finalizar.

#### <a name="delete-a-database"></a>Eliminación de una base de datos

1. Haga clic con el botón derecho en la base de datos y seleccione **Eliminar base de datos**. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Captura de pantalla que muestra el menú que aparece después de hacer clic con el botón derecho, con la opción Eliminar base de datos resaltada.":::

1. Seleccione **Sí** en la ventana emergente. Se elimina el nodo de la base de datos y la cuenta de Azure Cosmos DB se actualiza automáticamente.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Captura de pantalla de la ventana de confirmación con el botón Sí resaltado.":::

#### <a name="create-a-collection"></a>Creación de una colección

1. Haga clic con el botón derecho en la base de datos y seleccione **Crear colección**.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Captura de pantalla que muestra el menú que aparece después de hacer clic con el botón derecho, con la opción Crear colección resaltada.":::

1. En la ventana Crear colección, escriba la información solicitada, como **Id. de colección** y **Capacidad de almacenamiento**, etc. Seleccione **Aceptar** para finalizar.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Captura de pantalla de la ventana Crear colección que muestra el cuadro Id. de colección y los botones de Capacidad de almacenamiento.":::

1. Seleccione **Sin límite** para poder especificar una clave de partición y, después, seleccione **Aceptar** para finalizar.

   > [!NOTE]
   > Si se usa una clave de partición al crear una colección, una vez completada la creación no se podrá cambiar el valor de la clave de partición de la colección.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Captura de pantalla de la ventana Crear colección, que muestra la opción Sin límite seleccionada como Capacidad de almacenamiento y el cuadro Clave de partición resaltado.":::

#### <a name="delete-a-collection"></a>Eliminación de una colección

- Haga clic con el botón derecho en la colección, seleccione **Eliminar colección** y luego seleccione **Sí** en la ventana emergente.

    Se elimina el nodo de colección y la base de datos se actualiza automáticamente.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Captura de pantalla que muestra el menú que aparece después de hacer clic con el botón derecho, con la opción Eliminar colección resaltada.":::

### <a name="document-management"></a>Administración de documentos

#### <a name="create-and-modify-documents"></a>Crear y modificar documentos

- Abra **Documentos** en el panel izquierdo, seleccione **Nuevo documento**, edite el contenido en el panel derecho y seleccione **Guardar**.
- También puede actualizar un documento existente y luego seleccionar **Guardar**. Para descartar los cambios, seleccione **Descartar**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Captura de pantalla que muestra la opción Documentos resaltada en el panel izquierdo. En el panel derecho están resaltadas las opciones Nuevo documento, Guardar y Descartar.":::

#### <a name="delete-a-document"></a>Eliminar un documento

* Haga clic en el botón **Eliminar** para eliminar el documento seleccionado.

#### <a name="query-for-documents"></a>Consulta de documentos

* Para editar el filtro de documento, escriba una [Consulta SQL](./sql-query-getting-started.md) y seleccione **Aplicar**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Captura de pantalla del panel derecho que muestra los botones Filtrar y Aplicar, el número de identificación y el cuadro consulta resaltados.":::

### <a name="graph-management"></a>Administración de gráficos

#### <a name="create-and-modify-a-vertex"></a>Creación y modificación de un vértice

* Para crear un vértice, abra **Grafo** en el panel izquierdo, seleccione **Nuevo vértice**, edite el contenido y seleccione **Aceptar**.
* Para modificar un vértice existente, seleccione el icono del lápiz del panel derecho.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Captura de pantalla que muestra la opción Grafo seleccionada en el panel izquierdo y la opción Nuevo vértice y el icono de lápiz resaltados en el panel derecho.":::

#### <a name="delete-a-graph"></a>Eliminación de un grafo

* Para eliminar un vértice, seleccione el icono de la papelera que junto al nombre.

#### <a name="filter-for-graph"></a>Filtro de grafos

* Para editar el filtro de grafos, escriba una [consulta de Gremlin](gremlin-support.md) y seleccione **Aplicar filtro**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Captura de pantalla que muestra la opción Grafo seleccionada en el panel izquierdo y la opción Aplicar filtro y el cuadro consulta resaltados en el panel derecho.":::

### <a name="table-management"></a>Administración de tablas

#### <a name="create-and-modify-a-table"></a>Cree y modifique una tabla

* Para crear una tabla:
   1. En el panel izquierdo, abra **Entidades** y seleccione **Agregar**.
   1. En el cuadro de diálogo **Agregar entidad**, edite el contenido.
   1. Seleccione el botón **Agregar propiedad** para agregar una propiedad.
   1. Seleccione **Insertar**.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Captura de pantalla que muestra la opción Entidades resaltada en el panel izquierdo y las opciones Agregar, Editar, Agregar propiedad e Insertar resaltadas en el panel derecho.":::

* Para modificar una tabla, seleccione **Editar**, modifique el contenido y seleccione **Actualizar**.

   

#### <a name="import-and-export-table"></a>Importación y exportación de una tabla

* Para importarla, seleccione el botón **Importar** y elija una tabla existente.
* Para exportarla, seleccione el botón **Exportar** y elija un destino.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Captura de pantalla que muestra los botones Importar y Exportar resaltados en el panel derecho.":::

#### <a name="delete-entities"></a>Eliminación de entidades

* Seleccione las entidades y, luego, el botón **Eliminar**.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Captura de pantalla que muestra el botón Eliminar resaltado en el panel derecho y una ventana emergente de confirmación con el valor Sí resaltado.":::

#### <a name="query-a-table"></a>Consultar una tabla

- Seleccione el botón **Consulta**, especifique una condición de consulta y seleccione el botón **Ejecutar consulta**. Para cerrar el panel de consulta, seleccione el botón **Cerrar consulta**.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Captura de pantalla del panel derecho que muestra el botón Ejecutar consulta y el botón Cerrar consulta resaltados.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Administrar procedimientos almacenados, desencadenadores y UDF

* Para crear un procedimiento almacenado:
  1. En el árbol de la izquierda, haga clic con el botón derecho en **Procedimientos almacenados** y seleccione **Crear procedimiento almacenado**.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Captura de pantalla del panel izquierdo que muestra el menú que aparece después de hacer clic con el botón derecho, con la opción Crear procedimiento almacenado resaltada.":::
  
  1. Escriba un nombre en el lado izquierdo, escriba los scripts de procedimiento almacenado en el panel derecho y seleccione **Crear**.
  
* Para modificar un procedimiento almacenado existente, haga doble clic en el procedimiento, realice la actualización y seleccione **Actualizar** para guardar. También puede seleccionar **Descartar** para cancelar el cambio.

* Las operaciones para **Desencadenadores** y **UDF** son similares a las de **Procedimientos almacenados**.

## <a name="troubleshooting"></a>Solución de problemas

A continuación se muestran soluciones a problemas comunes que surgen al usar Azure Cosmos DB en el Explorador de Storage.

### <a name="sign-in-issues"></a>Problemas de inicio de sesión

En primer lugar, reinicie la aplicación para ver si se corrige el problema. Si el problema persiste, continúe con la solución de problemas.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificado autofirmado de cadena de certificados

Existen varias razones por las que puede ver este error, las dos más comunes son:

* Está detrás de un *proxy transparente*. Alguien, como el departamento de TI, intercepta el tráfico HTTPS, lo descifra y luego lo cifra mediante un certificado autofirmado.

* Está ejecutando software, como software antivirus. El software inserta un certificado TLS/SSL autofirmado en los mensajes HTTPS que se reciben.

Cuando el Explorador de Storage encuentra un certificado autofirmado, no sabe si el mensaje HTTPS que recibe se ha alterado. Pero si tiene una copia del certificado autofirmado, puede indicar al Explorador de Storage que confíe en él. Si no está seguro de quién insertó el certificado, puede seguir estos pasos para intentar averiguarlo:

1. Instale OpenSSL:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): Cualquiera de las versiones ligeras es correcta.
     - En macOS y Linux: debe estar incluido en el sistema operativo.

1. Ejecute OpenSSL:
    * Windows: Vaya al directorio de instalación y, luego, en **/bin/** , haga doble clic en **openssl.exe**.
    * Mac y Linux: Ejecute **openssl** desde un terminal.
1. Ejecute `s_client -showcerts -connect microsoft.com:443`.
1. Busque certificados autofirmados. Si no está seguro de cuáles son autofirmados, busque cualquier lugar en el que el asunto ("s:") y el emisor ("i:") sean el mismo.
1. Si encuentra certificados autofirmados, copie y pegue todo el contenido desde **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** (inclusive) en un nuevo archivo .CER para cada uno de los certificados.
1. Abra el Explorador de Storage y vaya a **Editar** > **Certificados SSL** > **Importar certificados**. Use el selector de archivos para buscar, seleccionar y abrir los archivos .CER que creó.

Si no encuentra ningún certificado autofirmado, puede enviar comentarios para obtener más ayuda.

#### <a name="unable-to-retrieve-subscriptions"></a>No se pueden recuperar las suscripciones

Si no puede recuperar las suscripciones después de iniciar sesión, pruebe estas sugerencias:

* Compruebe que la cuenta tiene acceso a las suscripciones. Para ello, inicie sesión en [Azure Portal](https://portal.azure.com/).
* Asegúrese de que ha iniciado sesión en el entorno correcto:
  * [Azure](https://portal.azure.com/)
  * [Azure en China](https://portal.azure.cn/)
  * [Azure Alemania](https://portal.microsoftazure.de/)
  * [Azure US Government](https://portal.azure.us/)
  * Entorno personalizado/Azure Stack
* Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage.
* Quite la cuenta y agréguela de nuevo.
* Intente eliminar los siguientes archivos del directorio de inicio (como: C:\Users\ContosoUser) y, después, intente volver a agregar la cuenta:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Presione la tecla F12 para abrir la consola del desarrollador. Observe la consola para ver los mensajes de error al iniciar sesión.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Captura de pantalla de la consola de herramientas de desarrollo, que muestra la opción Consola resaltada.":::

#### <a name="unable-to-see-the-authentication-page"></a>No se puede ver la página de autenticación

Si no puede ver la página de autenticación:

* En función de la velocidad de la conexión, la página de inicio de sesión puede tardar unos minutos en cargarse. Espere al menos un minuto antes de cerrar el cuadro de diálogo de autenticación.
* Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage.
* En la consola de herramientas de desarrollo (F12), observe las respuestas para ver si puede encontrar alguna pista para saber por qué no funciona la autenticación.

#### <a name="cant-remove-an-account"></a>No se puede quitar una cuenta

Si no puede quitar una cuenta, o si el vínculo para volver a autenticar no hace nada:

* Intente eliminar los siguientes archivos del directorio de inicio y vuelva a agregar la cuenta:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Si desea quitar recursos de Storage conectados a SAS, elimine:
  * Carpeta %AppData%/StorageExplorer para Windows
  * /Users/<su_nombre>/Library/Application SUpport/StorageExplorer para macOS
  * ~/.config/StorageExplorer para Linux
  
  > [!NOTE]
  > Si elimina estos archivos, **tendrá que volver a escribir todas sus credenciales**.

### <a name="httphttps-proxy-issue"></a>Problema de proxy HTTP/HTTPS

No se pueden mostrar los nodos de Azure Cosmos DB en el árbol de la izquierda cuando se configura un proxy HTTP/HTTPS en ASE. Puede usar el Explorador de datos de Azure Cosmos DB en Azure Portal como solución alternativa.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema de nodo "Development" (Desarrollo) en nodo "Local and Attached" (Locales y conectados)

No se produce ninguna respuesta después de seleccionar el nodo **Desarrollo** en el nodo **Local and Attached** (Locales y conectados) en el árbol de la izquierda. Este comportamiento es normal.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Captura de pantalla que muestra el nodo Desarrollo seleccionado.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Error al conectar una cuenta de Azure Cosmos DB en el nodo **Local and Attached** (Locales y conectados)

Si ve el siguiente error después de conectar una cuenta de Azure Cosmos DB en nodo **Local and Attached** (Locales y conectados), asegúrese de que usa la cadena de conexión correcta.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Captura de pantalla de la ventana emergente del error No se han podido recuperar los recursos secundarios, que indica getaddrinfo ENOTFOUND.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Error al expandir el nodo de Azure Cosmos DB

Es posible que vea el siguiente error al intentar expandir los nodos del árbol de la izquierda.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Captura de pantalla de la ventana emergente del error No se han podido recuperar los recursos secundarios, que indica que no se puede conectar a esta cuenta de Cosmos DB.":::

Pruebe estas sugerencias:

* Compruebe si el aprovisionamiento de la cuenta de Azure Cosmos DB está en curso. Inténtelo de nuevo cuando la cuenta se haya creado correctamente.
* Si la cuenta se encuentra en los nodos **Acceso rápido** o **Local and Attached** (Locales y conectados), compruebe si la cuenta se ha eliminado. Si es así, tiene que quitar manualmente el nodo.

## <a name="next-steps"></a>Pasos siguientes

* Vídeo de información acerca del uso de Azure Cosmos DB en el Explorador de Azure Storage: [Uso de Azure Cosmos DB en el Explorador de Azure Storage](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Puede ver más información acerca del Explorador de Storage y conectarse a más servicios en [Introducción al Explorador de Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).
