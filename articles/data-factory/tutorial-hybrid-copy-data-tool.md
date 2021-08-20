---
title: Copia de datos locales con la herramienta Copiar datos de Azure
description: Creación de una instancia de Azure Data Factory y uso de la herramienta Copiar datos para copiar datos de una base de datos de SQL Server a una instancia de Azure Blob Storage.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 07/08/2021
ms.openlocfilehash: 143fa6c8d6d0d09d48de03b6d627c996268b5260
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113665898"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copia de datos de una base de datos de SQL Server en Azure Blob Storage con la herramienta Copiar datos
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión actual](tutorial-hybrid-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este tutorial, usará Azure Portal para crear una factoría de datos. A continuación, usará la herramienta Copiar datos para crear una canalización que copia datos de una instancia de base de datos de SQL Server en una instancia de Azure Blob Storage.

> [!NOTE]
> - Si no está familiarizado con Azure Data Factory, consulte [Introducción a Data Factory](introduction.md).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>Requisitos previos
### <a name="azure-subscription"></a>Suscripción de Azure
Antes de empezar, si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Roles de Azure
Para crear instancias de Data Factory, a la cuenta de usuario que use para iniciar sesión en Azure debe se le deben asignar los roles *Colaborador* o *Propietario*, o bien debe de ser de un *administrador* de la suscripción a Azure.

Para ver los permisos que tiene en la suscripción, vaya a Azure Portal. Seleccione su nombre de usuario en la esquina superior derecha y luego seleccione **Permisos**. Si tiene acceso a varias suscripciones, elija la correspondiente. Para obtener instrucciones de ejemplo sobre cómo agregar un usuario a un rol, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 y 2017
En este tutorial, usará una base de datos de SQL Server como almacén de datos de *origen*. La canalización de la factoría de datos que crea en este tutorial copia los datos de esta base de datos de SQL Server (origen) a Blob Storage (receptor). Luego, cree una tabla denominada **emp** en la base de datos de SQL Server e inserte un par de entradas de ejemplo en la tabla.

1. Inicie SQL Server Management Studio. Si no está instalada en su máquina, vaya a [Descarga de SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Conéctese a una instancia de SQL Server con sus credenciales.

1. Cree una base de datos de ejemplo. En la vista de árbol, haga clic con el botón derecho en **Bases de datos** y, luego, seleccione **Nueva base de datos**.

1. En el cuadro de diálogo **Nueva base de datos**, escriba el nombre de la base de datos y haga clic en **Aceptar**.

1. Para crear la tabla **emp** e insertar en ella algunos datos de ejemplo, ejecute el siguiente script de consulta en la base de datos. En la vista de árbol, haga clic con el botón derecho en la base de datos que ha creado y, después, haga clic en **Nueva consulta**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Cuenta de almacenamiento de Azure
En este tutorial, use una cuenta de almacenamiento de Azure (en concreto Blob Storage) de uso general como almacén de datos de destino o receptor. Si no dispone de una cuenta de almacenamiento de uso general, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-account-create.md), donde se indica cómo crearla. La canalización de Data Factory que crea en este tutorial copia los datos de la base de datos de SQL Server (origen) a esta instancia de Blob Storage (receptor). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obtención de un nombre y una clave de cuenta de almacenamiento
En este tutorial, use el nombre y la clave de su cuenta de almacenamiento. Para obtener el nombre y la clave de la cuenta de almacenamiento, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con el nombre de usuario y la contraseña de Azure.

1. Seleccione **Todos los servicios** en el panel izquierdo. Use la palabra clave **Almacenamiento** para filtrar el resultado y, luego, seleccione **Cuentas de almacenamiento**.

    ![Búsqueda de cuenta de almacenamiento](media/doc-common-process/search-storage-account.png)

1. En la lista de cuentas de almacenamiento, filtre por su cuenta de almacenamiento, si fuera necesario. Después, seleccione su cuenta de almacenamiento.

1. En la ventana **Cuenta de almacenamiento**, seleccione **Claves de acceso**.


1. En los cuadros **Nombre de la cuenta de almacenamiento** y **key1**, copie los valores y péguelos en el Bloc de notas, u otro editor, para su uso posterior en el tutorial.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, seleccione **Crear un recurso** > **Integración** > **Data Factory**.

   ![Creación de nueva factoría de datos](./media/doc-common-process/new-azure-data-factory-menu.png)

1. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**.

   El nombre de la factoría de datos tiene que ser *único a nivel global*. Si ve el siguiente mensaje de error en el campo de nombre, cambie el nombre de la factoría de datos (por ejemplo, suNombreADFTutorialDataFactory). Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Azure Data Factory: reglas de nomenclatura](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nuevo mensaje de error de factoría de datos por nombre duplicado.":::
1. Seleccione la **suscripción** de Azure en la que quiere crear la factoría de datos.
1. Para **Grupo de recursos**, realice uno de los siguientes pasos:

   - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable.

   - Seleccione **Crear nuevo** y escriba el nombre de un grupo de recursos. 
        
     Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).
1. En **Versión**, seleccione **V2**.
1. En **Ubicación**, seleccione la ubicación de la factoría de datos. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (por ejemplo, Azure Storage y SQL Database) y los procesos (por ejemplo, Azure HDInsight) que usa Data Factory pueden estar en otras ubicaciones o regiones.
1. Seleccione **Crear**.

1. Una vez finalizada la creación, verá la página **Data Factory** tal como se muestra en la imagen.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página principal de Azure Data Factory, con el icono Abrir Azure Data Factory Studio.":::

1. Seleccione **Abrir** en el icono **Abrir Azure Data Factory Studio** para iniciar la aplicación de interfaz de usuario de Data Factory en una pestaña independiente.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Uso de la herramienta Copy Data para crear una canalización

1. En la página principal de Azure Data Factory, seleccione **Ingerir** para iniciar la herramienta Copiar datos.

   ![Captura de pantalla que muestra la página principal de Azure Data Factory.](./media/doc-common-process/get-started-page.png)

1. En la página **Propiedades** de la herramienta Copiar datos, elija **Tarea de copia integrada** en **Tipo de tarea** y elija **Ejecutar una vez ahora** en **Cadencia de tareas o programación de tareas**. A continuación, seleccione **Siguiente**.

1. En la página **Almacén de datos de origen**, haga clic en **+ Crear una conexión nueva**.

1. En **Nueva conexión**, busque **SQL Server** y, a continuación, seleccione **Continuar**.

1. En el cuadro de diálogo **Nueva conexión (SQL Server)** , en **Nombre**, escriba **SqlServerLinkedService**. Seleccione **+Nuevo** en la opción **Connect via integration runtime** (Conectar mediante IR). Tiene que crear un entorno de ejecución de integración, descargarlo en la máquina y registrarlo en Data Factory. El entorno de ejecución de integración autohospedado copia datos entre el entorno local y la nube.

1. En el cuadro de diálogo **Configuración de Integration Runtime**, seleccione **Autohospedado**. Después, seleccione **Continuar**.

   ![Creación de tiempo de ejecución de integración](./media/tutorial-hybrid-copy-data-tool/create-self-hosted-integration-runtime.png)

1. En el cuadro de diálogo **Configuración de Integration Runtime**, en **Nombre** escriba **TutorialIntegrationRuntime**. Seleccione **Crear**.

1. En el cuadro de diálogo **Configuración de Integration Runtime**, seleccione **Haga clic aquí para iniciar la configuración rápida en este equipo**. Esta acción instala el entorno de ejecución de integración en la máquina y la registra en Data Factory. Como alternativa, puede usar la instalación manual para descargar el archivo de instalación, ejecutarlo y registrar la instancia de Integration Runtime con la clave.

1. Ejecute la aplicación descargada. Verá el estado de la configuración rápida en la ventana.

    ![Estado de la configuración rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. En el cuadro de diálogo **Nueva conexión (SQL Server)** , confirme que **TutorialIntegrationRuntime** está seleccionado en **Conectar a través de IR**. A continuación, siga estos pasos:

    a. En **Name** (Nombre), escriba **SqlServerLinkedService**.

    b. Escriba el nombre de la instancia de SQL Server en **Server name** (Nombre del servidor).

    c. Escriba el nombre de la base de datos local en **Database name** (Nombre de la base de datos).

    d. Seleccione la autenticación adecuada en **Authentication type** (Tipo de autenticación).

    e. Escriba el nombre de usuario con acceso a la instancia de SQL Server en **User name** (Nombre de usuario).

    f. Escriba la **contraseña** del usuario.

    g. Pruebe la conexión y seleccione **Crear**.

      ![Integration Runtime seleccionado](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. En la página **Almacén de datos de origen**, asegúrese de que la conexión de **SQL Server** recién creada está seleccionada en el bloque **Conexión**. A continuación, en la sección **Tablas de origen**, elija **TABLAS EXISTENTES** y seleccione la tabla **dbo.emp** en la lista, y seleccione **Siguiente**. Puede seleccionar cualquier otra tabla en función de la base de datos.

1. En la página **Aplicar filtro**, puede obtener una vista previa de los datos y ver el esquema de los datos de entrada seleccionando el botón **Vista previa de los datos**. Luego, seleccione **Siguiente**.

1. En la página **Almacén de datos de destino**, seleccione **+ Crear nueva conexión**

1. En **Nueva conexión**, busque y seleccione **Azure Blob Storage** y, después, seleccione **Continuar**.

   ![Selección de Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. En el cuadro de diálogo **Nueva conexión (Azure Blob Storage)** , realice los siguientes pasos:

   a. En **Name** (Nombre), escriba **AzureStorageLinkedService**.

   b. En **Conectar mediante IR**, seleccione **TutorialIntegrationRuntime** y seleccione **Clave de cuenta** en **Método de autenticación**.
   
   c. En **Suscripción de Azure**, seleccione la suscripción de Azure en la lista desplegable.

   d. Seleccione la cuenta de almacenamiento en la lista desplegable **Storage account name** (Nombre de la cuenta de almacenamiento).

   e. Pruebe la conexión y seleccione **Crear**.

1. En el cuadro de diálogo **Almacén de datos de destino**, asegúrese de que la conexión de **Azure Blob Storage** recién creada está seleccionada en el bloque **Conexión**. A continuación, en **Ruta de acceso de carpeta**, escriba **adftutorial/fromonprem**. El contenedor **adftutorial** se creó como parte de los requisitos previos. Si no existe la carpeta de salida (en este caso **fromonprem**), Data Factory la crea automáticamente. También puede usar el botón **Browse** (Examinar) para examinar Blob Storage y sus contenedores o carpetas. Si no especifica ningún valor en **nombre de archivo**, de forma predeterminada se usará el nombre del origen (en este caso **dbo.emp**).

   :::image type="content" source="./media/tutorial-hybrid-copy-data-tool/destination-data-store.png" alt-text="Captura de pantalla que muestra la configuración de la página &quot;Almacén de datos de destino&quot;.":::

1. En el cuadro de diálogo **File format settings** (Configuración de formato de archivo), seleccione **Next** (Siguiente).

1. En el cuadro de diálogo **Configuración**, en **Nombre de tarea**, escriba **CopyFromOnPremSqlToAzureBlobPipeline** y, a continuación, seleccione **Siguiente**. La herramienta Copy Data crea una canalización con el nombre que especificó para este campo.

1. En el cuadro de diálogo **Summary** (Resumen), revise los valores de configuración y seleccione **Next** (Siguiente).

1. En la página **Implementación**, seleccione **Supervisión** para supervisar la canalización (tarea). 

1. Cuando la ejecución de la canalización se complete, podrá ver el estado de la canalización que ha creado. 

1. En la página "Ejecuciones de canalización", seleccione **Actualizar** para actualizar la lista. Seleccione el vínculo en **Nombre de canalización** para ver los detalles de la ejecución de actividad o volver a ejecutar la canalización. 

    :::image type="content" source="./media/tutorial-hybrid-copy-data-tool/pipeline-runs.png" alt-text="Captura de pantalla que muestra la página &quot;Ejecuciones de canalización&quot;.":::

1. En la página "Ejecuciones de actividad", seleccione el vínculo **Detalles** (icono de gafas) en la columna **Nombre de actividad** para obtener más detalles sobre la operación de copia. Para volver a la página "Ejecuciones de canalización", seleccione el vínculo **Todas las ejecuciones de la canalización** en el menú de la ruta de navegación. Para actualizar la vista, seleccione **Refresh** (Actualizar).

    :::image type="content" source="./media/tutorial-hybrid-copy-data-tool/activity-details.png" alt-text="Captura de pantalla que muestra los detalles de actividad.":::

1. Confirme que ve un archivo de salida en la carpeta **fromonprem** del contenedor **adftutorial**.

1. Seleccione la pestaña **Author** (Crear) de la izquierda para cambiar al modo de edición. Con el editor puede actualizar los servicios vinculados, los conjuntos de datos y las canalizaciones creados mediante la herramienta. Seleccione **Code** (Código) para ver el código JSON asociado con la entrada abierta en el editor. Para más información sobre cómo editar estas entidades en la interfaz de usuario de Data Factory, consulte [la versión de Azure Portal de este tutorial](tutorial-copy-data-portal.md).

    :::image type="content" source="./media/tutorial-hybrid-copy-data-tool/author-tab.png" alt-text="Captura de pantalla que muestra la pestaña Creador.":::

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia datos de la base de datos de SQL Server en Blob Storage. Ha aprendido a:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

Para ver una lista de los almacenes de datos compatibles con Data Factory, consulte los [almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).

Para informarse acerca de cómo copiar datos de forma masiva de un origen a un destino, pase al tutorial siguiente:

> [!div class="nextstepaction"]
>[Copiar datos de forma masiva](tutorial-bulk-copy-portal.md)
