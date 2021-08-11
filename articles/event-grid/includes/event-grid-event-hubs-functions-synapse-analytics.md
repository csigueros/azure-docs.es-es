---
title: Archivo de inclusión
description: archivo de inclusión
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9ae69c2a119fc29d3ba7dcd78e4238f7712ca170
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414267"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Información general de la aplicación":::

En este diagrama se muestra el flujo de trabajo de la solución que se compila en este tutorial: 

1. Los datos que se envían a un centro de eventos de Azure se capturan en una instancia de Azure Blob Storage.
2. Una vez que se completa la captura de los datos, se genera un evento y se envía a una cuadrícula de eventos de Azure. 
3. La cuadrícula de eventos desvía estos datos de evento a una aplicación de función de Azure.
4. La aplicación de función usa la dirección URL del blob de los datos del evento para recuperar el blob desde el almacenamiento. 
5. La aplicación de funciones migra los datos del blob a una instancia de Azure Synapse Analytics. 

En este artículo, podrá llevar a cabo estos pasos:

> [!div class="checklist"]
> - Implementación de la infraestructura necesaria para el tutorial
> - Publicar código en una aplicación de Functions
> - Creación de una suscripción de Event Grid 
> - Transmisión de datos de ejemplo a una instancia de Event Hubs
> - Comprobación de los datos capturados en Azure Synapse Analytics

## <a name="prerequisites"></a>Requisitos previos
Para realizar este tutorial, necesitará lo siguiente:

- Suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- [Visual Studio 2019](https://www.visualstudio.com/vs/) con cargas de trabajo para: desarrollo de escritorio de .NET, desarrollo de Azure, desarrollo web y de ASP.NET, desarrollo de Node.js y desarrollo de Python.
- Descargue el [proyecto de ejemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) en su equipo.
    - WindTurbineDataGenerator: un publicador simple que envía datos de turbina eólica de ejemplo a un centro de eventos con la función Capture habilitada
    - FunctionDWDumper : una función de Azure que recibe una notificación de Event Grid cuando se captura un archivo Avro en el blob de Azure Storage. Recibe la ruta de acceso del identificador URI del blob, lee su contenido y envía estos datos a Azure Synapse Analytics (grupo de SQL dedicado).

## <a name="deploy-the-infrastructure"></a>Implementar la infraestructura
En este paso, implementará la infraestructura requerida con una [plantilla de Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Cuando implemente la plantilla, se crearán estos recursos:

* Un centro de eventos con la característica Captura habilitada.
* Una cuenta de almacenamiento para los archivos capturados. 
* Un plan de App Service para hospedar la aplicación de función
* Una aplicación de función para procesar el evento
* Un servidor SQL Server para hospedar el almacenamiento de datos
* Azure Synapse Analytics (grupo de SQL dedicado) para el almacenamiento de los datos migrados

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Uso de la CLI de Azure para implementar la infraestructura

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
2. Seleccione el botón **Cloud Shell** que se encuentra en la parte superior.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure Portal":::
3. Verá Cloud Shell abierto en la parte inferior del explorador.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. En Cloud Shell, si ve una opción para seleccionar entre **Bash** y **PowerShell**, seleccione **Bash**. 
5. Si usa Cloud Shell por primera vez, seleccione **Crear almacenamiento** para crear una cuenta de almacenamiento. Azure Cloud Shell requiere una cuenta de Azure Storage donde almacenar algunos archivos. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Creación de almacenamiento para Cloud Shell":::
6. Espere hasta que se inicialice Cloud Shell. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell inicializado":::
1. Para crear un grupo de recursos de Azure, ejecute el comando de la CLI siguiente: 
    1. Copie y pegue este comando en la ventana Cloud Shell. Cambie el nombre del grupo de recursos y su ubicación si lo desea.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Presione **ENTRAR**. 

        Este es un ejemplo:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Para implementar todos los recursos mencionados en la sección anterior (centro de eventos, cuenta de almacenamiento, aplicación de funciones, instancia de Azure Synapse Analytics), ejecute el siguiente comando de la CLI: 
    1. Copie y pegue el comando en la ventana Cloud Shell. También puede que quiera copiar y pegar en el editor que prefiera, establecer valores y luego copiar el comando en Cloud Shell. 

        > [!IMPORTANT]
        > Especifique valores para las entidades siguientes antes de ejecutar el comando: 
        > - Nombre del grupo de recursos que creó anteriormente.
        > - Nombre del espacio de nombres del centro de eventos. 
        > - Nombre del centro de eventos. Puede dejar el valor sin modificaciones (hubdatamigration).
        > - Nombre del servidor SQL Server.
        > - Nombre del usuario y la contraseña de SQL. 
        > - Nombre de la base de datos.
        > - Nombre de la cuenta de almacenamiento. 
        > - Nombre de la aplicación de función. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Presione **ENTRAR** en la ventana Cloud Shell para ejecutar el comando. Este proceso puede tardar un poco debido a que se están creando varios recursos. En el resultado del comando, asegúrese de que no se produjo ningún error. 
1. Para cerrar Cloud Shell, seleccione el botón **Cloud Shell** en el portal (o) el botón **X** que se encuentra en la esquina superior derecha de la ventana de Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Comprobación de la creación de los recursos

1. En Azure Portal, seleccione **Grupos de recursos** en el menú de la izquierda. 
2. Para filtrar la lista de grupos de recursos, escriba el nombre del grupo de recursos en el cuadro de búsqueda. 
3. Seleccione el grupo de recursos en la lista.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Seleccionar el grupo de recursos":::
4. Confirme que ve los recursos siguientes en el grupo de recursos:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Recursos del grupo de recursos" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Creación de una tabla en Azure Synapse Analytics
Para crear una tabla en el almacenamiento de datos, ejecute el script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Para ejecutar el script, puede usar Visual Studio o el Editor de consultas en el portal. Estos pasos le muestran cómo usar el Editor de consultas: 

1. En la lista de recursos del grupo de recursos, seleccione el **grupo de SQL dedicado**. 
2. En la página **Dedicated SQL pool** (Grupo de SQL dedicado), en la sección **Tareas comunes** del menú de la izquierda, seleccione **Editor de consultas (versión preliminar)** . 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Página de Azure Synapse Analytics":::
2. Escriba el nombre de **usuario** y la **contraseña** del servidor SQL Server y seleccione **Aceptar**. Si ve un mensaje sobre cómo permitir que el cliente acceda a SQL Server, siga estos pasos:
    1. Seleccione el vínculo: **Establezca el firewall del servidor**. 
    2. En la página **Configuración de firewall**, seleccione **Agregar IP de cliente** en la barra de herramientas y, a continuación, seleccione **Guardar** en la barra de herramientas. 
    3. Seleccione **Aceptar** en el mensaje de operación correcta.
    4. Regrese a la página **Dedicated SQL pool** (Grupo de SQL dedicado) y seleccione **Editor de consultas (versión preliminar)** en el menú de la izquierda. 
    5. Escriba el **nombre de usuario** y la **contraseña** y, después, seleccione **Aceptar**. 
1. En la ventana de consulta, copie y ejecute el script SQL siguiente: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="Ejecución de la consulta SQL":::
5. Mantenga abierta esta pestaña o ventana para poder comprobar que los datos se crean al final del tutorial. 

### <a name="update-the-function-runtime-version"></a>Actualización de la versión del runtime de la función

1. Abra otra pestaña en el explorador web y vaya a [Azure Portal](https://portal.azure.com).
1. En Azure Portal, seleccione **Grupos de recursos** en el menú de la izquierda.
1. Seleccione el grupo de recursos en el que exista la aplicación de funciones. 
1. Seleccione la **aplicación de funciones** en la lista de recursos del grupo de recursos.
1. Seleccione **Configuración** en **Configuración** en el menú de la izquierda. 
1. Cambie a la pestaña **Configuración del tiempo de ejecución de la función**, en el panel derecho. 
1. Actualice la **versión del runtime** a la **versión ~3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="Actualización de la versión del runtime de la función":::
6. Seleccione **Guardar** en la barra de herramientas. 
1. En el elemento emergente de confirmación **Guardar cambios**, seleccione **Continuar**. 

## <a name="publish-the-azure-functions-app"></a>Publicar la aplicación de Azure Functions

1. Inicie Visual Studio.
2. Abra la solución **EventHubsCaptureEventGridDemo.sln** que descargó de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) como parte de los requisitos previos. Se encuentra en la carpeta `/samples/e2e/EventHubsCaptureEventGridDemo`. 
3. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **FunctionEGDWDumper** y seleccione **Publicar**.
4. Si ve la pantalla siguiente, seleccione **Iniciar**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Botón Iniciar de la sección Publicar.":::
5. En el cuadro de diálogo **Publicar**, seleccione **Azure** en **Destino** y seleccione **Siguiente**. 
6. Seleccione **Azure Function App (Windows)**  y seleccione **Siguiente**.
7. En la pestaña **Functions instance** (Instancia de Functions), seleccione su suscripción de Azure, expanda el grupo de recursos, seleccione su aplicación de funciones y, después, seleccione **Finalizar**. Si aún no lo ha hecho, es preciso que inicie sesión en su cuenta de Azure. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Selección de la aplicación de función":::
8. En la página **Publicar**, en la sección **Dependencias de servicio**, seleccione **Configurar** para **Almacenamiento**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Seleccionar el vínculo Configurar para la dependencia del servicio de almacenamiento":::
1. En la página **Configure dependency** (Configurar dependencia), siga estos pasos: 
    1. Seleccione la **cuenta de almacenamiento** que creó anteriormente y, a continuación, **Siguiente**. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Seleccionar cuenta de almacenamiento":::
    10. Especifique un nombre para la **cadena de conexión** y seleccione **Ninguno** para la opción **Save connection string** (Guardar la cadena de conexión) y, a continuación, seleccione **Siguiente**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Especificar un nombre para la cadena de conexión":::      
    1. Desactive el **archivo de código de C#** y la opción **Secrets store** (Almacén de secretos) y, después, seleccione **Finalizar**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Revisión del resumen de cambios":::
1. Cuando Visual Studio haya configurado el perfil, seleccione **Publicar**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Select publish":::
2. En la pestaña con la página **Función de Azure** abierta, seleccione **Funciones** en el menú de la izquierda. Confirme que la función **EventGridTriggerMigrateData** aparece en la lista. Si no la ve, intente publicar de nuevo desde Visual Studio y, a continuación, actualice la página en el portal. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Confirmar la creación de la función":::    

Después de publicar la función, estará listo para suscribirse al evento.

## <a name="subscribe-to-the-event"></a>Nos suscribiremos al evento.

1. En una pestaña o ventana nueva de un explorador web, vaya a [Azure Portal](https://portal.azure.com).
2. En Azure Portal, seleccione **Grupos de recursos** en el menú de la izquierda. 
3. Para filtrar la lista de grupos de recursos, escriba el nombre del grupo de recursos en el cuadro de búsqueda. 
4. Seleccione el grupo de recursos en la lista.
1. Seleccione el **espacio de nombres de Event Hubs** en la lista de recursos.
1. En la página **Espacio de nombres de Event Hubs**, seleccione **Eventos** en el menú de la izquierda y, a continuación, seleccione **+ Suscripción de eventos** en la barra de herramientas. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Agregar vínculo de suscripción de eventos en la página Eventos de un espacio de nombres de Event Hubs":::
1. En la página **Crear suscripción de eventos**, siga estos pasos:
    1. Escriba un nombre para la **suscripción a eventos**. 
    1. Escriba un nombre para el **tema del sistema**. Un tema del sistema proporciona un punto de conexión para que el remitente envíe eventos. Para más información, vea [Temas del sistema](../system-topics.md).
    1. En **Tipo de punto de conexión**, seleccione **Función de Azure**.
    1. En **Punto de conexión**, seleccione el vínculo.
    1. En la página **Seleccionar la función de Azure**, siga estos pasos si no se rellenan automáticamente.
        1. Seleccione la suscripción de Azure que tenga la función de Azure. 
        1. Seleccione el grupo de recursos para la función. 
        1. Seleccione la aplicación de funciones.
        1. Seleccione la ranura de implementación. 
        1. Seleccione la función **EventGridTriggerMigrateData**. 
    1. En la página **Seleccionar la función de Azure**, seleccione **Confirmar selección**.
    1. De nuevo, en la página **Crear suscripción de eventos**, seleccione **Crear**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Crear una suscripción de eventos mediante la función" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Compruebe que se haya creado la suscripción a eventos. Cambie a la pestaña **Suscripciones a eventos** de la página **Eventos** del espacio de nombres de Event Hubs. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Confirmar la suscripción a eventos" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Seleccione el plan de App Service (no la instancia de App Service) en la lista de recursos del grupo de recursos. 

## <a name="run-the-app-to-generate-data"></a>Ejecutar la aplicación para generar datos
Ya ha terminado de configurar el centro de eventos, el grupo de SQL dedicado (anteriormente SQL Data Warehouse), la aplicación de funciones de Azure y la suscripción a eventos. Hay que configurar algunos valores antes de ejecutar una aplicación que genere los datos del centro de eventos.

1. En Azure Portal, vaya al grupo de recursos como lo hizo anteriormente. 
2. Seleccione el espacio de nombres de Event Hubs.
3. En la página **Espacio de nombres de Event Hubs**, seleccione **Directivas de acceso compartido** en el menú de la izquierda.
4. Seleccione **RootManageSharedAccessKey** en la lista de directivas. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Página Directivas de acceso compartido para un espacio de nombres de Event Hubs":::    
1. Seleccione el botón Copiar que está junto al cuadro de texto **Cadena de conexión: clave principal**. 
1. Vuelva a la solución de Visual Studio. 
1. Haga clic con el botón derecho en el proyecto **WindTurbineDataGenerator** y seleccione **Establecer como proyecto de inicio**. 
1. En el proyecto WindTurbineDataGenerator, abra **program.cs**.
1. Reemplace `<EVENT HUBS NAMESPACE CONNECTION STRING>` por la cadena de conexión que copió en el portal. 
1. Reemplace `<EVENT HUB NAME>` por el nombre del centro de eventos. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Compile la solución. Ejecute la aplicación **WindTurbineGenerator.exe**. 
7. Después de un par de minutos, en la otra pestaña del explorador donde tiene abierta la ventana de consulta, consulte la tabla del almacenamiento de datos para obtener los datos migrados.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Resultados de la consulta](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Supervisión de la solución
Esta sección le ayuda a supervisar o solucionar problemas de la solución. 

### <a name="view-captured-data-in-the-storage-account"></a>Visualización de datos capturados en la cuenta de almacenamiento
1. Desplácese hasta el grupo de recursos y seleccione la cuenta de almacenamiento usada para capturar datos de eventos. 
1. En la página **Cuenta de almacenamiento**, seleccione **Explorador de Storage (versión preliminar)** en el menú de la izquierda.
1. Expanda **CONTENEDORES DE BLOBS** y seleccione **windturbinecapture**. 
1. Abra la carpeta con el mismo nombre que el **espacio de nombres de Event Hubs** en el panel derecho. 
1. Abra la carpeta con el mismo nombre que el centro de eventos (**hubdatamigration**). 
1. Profundice en las carpetas hasta que vea los archivos AVRO. Este es un ejemplo:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Archivo capturado en el almacenamiento" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Comprobación de que el desencadenador de Event Grid invocó la función
1. Desplácese al grupo de recursos y seleccione la aplicación de funciones. 
1. Seleccione **Funciones** en el menú de la izquierda.
1. Seleccione la función **EventGridTriggerMigrateData** de la lista. 
1. En la página **Función**, seleccione **Supervisión** en el menú de la izquierda. 
1. Seleccione **Configurar** para configurar Application Insights para que capture registros de invocación. 
1. Cree un nuevo recurso de **Application Insights** o use uno existente. 
1. Vuelva a la página **Supervisión** de la función. 
1. Confirme que la aplicación cliente (**WindTurbineDataGenerator**) que está enviando los eventos todavía se está ejecutando. Si no es así, ejecute la aplicación. 
1. Espere unos minutos (5 minutos o más) y seleccione el botón **Actualizar** para ver las invocaciones de función.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Invocaciones de función":::
1. Seleccione una invocación para ver los detalles.

    Event Grid distribuye datos del evento a los suscriptores. En el ejemplo siguiente se muestran los datos de eventos que se generan cuando el flujo de datos a través de un centro de eventos se captura en un blob. En concreto, observe que la propiedad `fileUrl` del objeto `data` apunta al blob en el almacenamiento. La aplicación de función usa esta dirección URL para recuperar el archivo de blob con los datos capturados.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Comprobación de que los datos están almacenados en el grupo de SQL dedicado
En la pestaña del explorador donde tiene abierta la ventana de consulta, consulte la tabla del grupo de SQL dedicado para obtener los datos migrados.

![Resultados de la consulta](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

