---
title: Ingesta de datos de Event Grid en el Explorador de datos de Azure Synapse (versión preliminar)
description: Aprenda a ingerir (cargar) datos en el Explorador de datos de Azure Synapse desde Event Grid.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 2c472ae5950bc035612987c35e76291dc5c23f49
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485666"
---
# <a name="ingest-blobs-into-azure-synapse-data-explorer-by-subscribing-to-event-grid-notifications-preview"></a>Ingesta de blobs en el Explorador de datos de Azure Synapse mediante la suscripción a las notificaciones de Event Grid (versión preliminar)

<!-- > [!div class="op_single_selector"]
> * [One-click](one-click-ingestion-new-table.md)
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager template](data-connection-event-grid-resource-manager.md) -->

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

En este artículo, aprenderá a ingerir blobs desde la cuenta de almacenamiento en el Explorador de datos de Azure Synapse mediante una conexión de datos de Event Grid. Creará una conexión de datos de Event Grid que establece una suscripción de [Azure Event Grid](/azure/event-grid/overview). La suscripción de Event Grid enruta los eventos desde la cuenta de almacenamiento al Explorador de datos mediante un centro de eventos de Azure. A continuación, puede ver un ejemplo del flujo de datos a lo largo de todo el sistema.

Para obtener información general sobre la ingesta en el Explorador de datos desde Event Grid, consulte [Conexión a Event Grid](data-explorer-ingest-event-grid-overview.md).<!-- To create resources manually in the Azure portal, see [Manually create resources for Event Grid ingestion](ingest-data-event-grid-manual.md). -->

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Creación de una tabla de destino a la que Event Hubs enviará datos
    1. En Synapse Studio, en el panel izquierdo, seleccione **Desarrollar**.
    1. En **KQL scripts** (Scripts de KQL), seleccione **&plus;** (Agregar un recurso nuevo) > **KQL script** (Script de KQL). En el panel derecho, puede asignar un nombre al script.
    1. En el menú **Conectarse a**, seleccione *contosodataexplorer*.
    1. En el menú **Use database** (Usar base de datos), seleccione *TestDatabase*.
    1. Pegue el siguiente comando y seleccione **Ejecutar** para crear la tabla.

        ```Kusto
        .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
        ```

        > [!TIP]
        > Compruebe que la tabla se creó correctamente. En el panel izquierdo, seleccione **Datos**, elija *contosodataexplorer* en el menú Más y seleccione **Actualizar**. En *contosodataexplorer*, expanda **Tablas** y asegúrese de que la tabla *TestTable* aparece en la lista.

    1. Copie el siguiente comando en la ventana y seleccione **Ejecutar** para asignar los datos JSON entrantes a los tipos de datos y los nombres de columna de la tabla (TestTable).

        ```Kusto
        .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
        ```

* Cree [una cuenta de almacenamiento](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* La suscripción de notificación de Event Grid se puede establecer en cuentas de Azure Storage para `BlobStorage`, `StorageV2` o [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction).

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-an-event-grid-data-connection"></a>Creación de una conexión de datos de Event Grid

Ahora, conecte la cuenta de almacenamiento al Explorador de datos para que los datos que fluyen hacia el almacenamiento se transmitan a la tabla de prueba. Esta conexión se puede crear en Azure Portal en el Explorador de datos.

1. En el grupo del Explorador de datos que creó, seleccione **Bases de datos** > **TestDatabase**.

    :::image type="content" source="../media/ingest-data-event-grid/select-test-database.png" alt-text="Seleccionar la base de datos de prueba.":::

1. Seleccione **Conexiones de datos** y **Agregar la conexión de datos**.

    :::image type="content" source="../media/ingest-data-event-grid/event-hub-connection.png" alt-text="Seleccione Ingesta de datos y Agregar la conexión de datos.":::

#### <a name="data-connection---basics-tab"></a>Conexión de datos: pestaña Básico

1. Seleccione el tipo de conexión: **Blob Storage**.

1. Rellene el formulario con la siguiente información:

    :::image type="content" source="../media/ingest-data-event-grid/data-connection-basics.png" alt-text="Rellene el formulario de Event Grid con los datos básicos de conexión.":::

    |**Configuración** | **Valor sugerido** | **Descripción del campo**|
    |---|---|---|
    | Nombre de la conexión de datos | *test-grid-connection* | Nombre de la conexión que quiere crear en el Explorador de datos.|
    | Suscripción de la cuenta de almacenamiento | Su Id. de suscripción | El identificador de la suscripción en la que reside la cuenta de almacenamiento.|
    | Cuenta de almacenamiento | *gridteststorage1* | Nombre de la cuenta de almacenamiento que creó anteriormente.|
    | Tipo de evento | *Blob creado* o *Blob con el nombre cambiado* | Tipo de evento que desencadena la ingesta. El evento *Blob con el nombre cambiado* solo se admite para el almacenamiento de ADLSv2. Estos son los tipos que se admiten: Microsoft.Storage.BlobCreated o Microsoft.Storage.BlobRenamed. |
    | Creación de recursos | *Automático* | Defina si quiere que el Explorador de datos cree una suscripción de Event Grid, un espacio de nombres de un centro de eventos y un centro de eventos automáticamente. <!-- To create resources manually, see [Manually create resources for Event Grid ingestion](ingest-data-event-grid-manual.md) -->|

1. Seleccione **Configuración del filtro** si desea realizar un seguimiento de determinados asuntos. Establezca los filtros para las notificaciones de la manera siguiente:
    * El campo **prefijo** es el prefijo *literal* del asunto. Como el patrón que se aplica es *startswith* (comienza por), puede abarcar varios contenedores, carpetas o blobs. No se permiten comodines.
        * Para definir un filtro en el contenedor de blobs, el campo *debe* establecerse de la siguiente manera: *`/blobServices/default/containers/[container prefix]`* .
        * Para definir un filtro en un prefijo de blob (o en una carpeta en Azure Data Lake Gen2), el campo *debe* establecerse de la siguiente manera: *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
    * El campo **sufijo** es el sufijo *literal* del blob. No se permiten comodines.
    * El campo **Distinguir mayúsculas de minúsculas** indica si los filtros de prefijos y sufijos distinguen mayúsculas de minúsculas.
    * Para más información sobre el filtrado de eventos, consulte [Eventos de Blob Storage](/azure/storage/blobs/storage-blob-event-overview#filtering-events).

    :::image type="content" source="../media/ingest-data-event-grid/filter-settings.png" alt-text="Configuración del filtro de Event Grid.":::

1. Seleccione **Siguiente: Propiedades de ingesta**.

> [!NOTE]
> Se recomienda actualizar la conexión de datos para utilizar identidades administradas para acceder a la cuenta de almacenamiento en cuanto la opción esté disponible para el clúster.

#### <a name="data-connection---ingest-properties-tab"></a>Conexión de datos: pestaña Propiedades de la ingesta

1. Rellene el formulario con la siguiente información. Los nombres de tabla y de asignación distinguen mayúsculas de minúsculas:

   :::image type="content" source="../media/ingest-data-event-grid/data-connection-ingest-properties.png" alt-text="Revisar y crear propiedades de ingesta de tablas y asignaciones.":::

    Propiedades de ingesta:

     **Configuración** | **Valor sugerido** | **Descripción del campo**
    |---|---|---|
    | Nombre de la tabla | *TestTable* | La tabla que creó en **TestDatabase**. |
    | Formato de datos | *JSON* | Los formatos admitidos son Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO, RAW y W3CLOG. Las opciones de compresión admitidas son Zip y Gzip. |
    | Asignación | *TestMapping* | La asignación que creó en **TestDatabase**, que asigna los datos JSON entrantes a los nombres de columnas y tipos de datos de **TestTable**.|
    | Configuración avanzada | *Mis datos tienen encabezados* | Ignora los encabezados. Se admite para archivos de tipo *SV.|

   > [!NOTE]
   > No es necesario especificar todos los **valores de configuración de enrutamiento predeterminados**. También se aceptan configuraciones parciales.
1. Seleccione **Siguiente: Revisar y crear**.

#### <a name="data-connection---review--create-tab"></a>Conexión de datos: pestaña Revisar y crear

1. Revise los recursos que se crearon automáticamente y seleccione **Crear**.

    :::image type="content" source="../media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="Revise y cree una conexión de datos para Event Grid.":::

### <a name="deployment"></a>Implementación

Espere hasta que se complete la implementación. Si se produce un error en la implementación, seleccione **Detalles de la operación** junto a la fase con errores para más información sobre el motivo del error. Seleccione **Volver a implementar** para intentar implementar los recursos de nuevo. Puede modificar los parámetros antes de la implementación.

:::image type="content" source="../media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="Implemente recursos de Event Grid.":::

## <a name="generate-sample-data"></a>Generación de datos de ejemplo

Ahora que el Explorador de datos y la cuenta de almacenamiento están conectados, puede crear datos de ejemplo.

### <a name="upload-blob-to-the-storage-container"></a>Carga de un blob en el contenedor de almacenamiento

Vamos a trabajar con un pequeño script de shell que emite algunos comandos básicos de la CLI de Azure para interactuar con recursos de Azure Storage. El script hace las siguientes acciones:

1. Crea un contenedor nuevo en su cuenta de almacenamiento.
1. Carga un archivo existente (en forma de blob) en ese contenedor.
1. Enumera los blobs del contenedor.

Puede usar [Azure Cloud Shell](/azure/cloud-shell/overview) para ejecutar el script directamente en el portal.

Guarde los datos en un archivo y cárguelo con este script:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
    #!/bin/bash
    ### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Para lograr el mejor rendimiento de la ingesta, se deben comunicar los tamaño *sin comprimir* de los blobs comprimidos enviados para la ingesta. Dado que las notificaciones de Event Grid solo contienen detalles básicos, debe comunicarse explícitamente la información de tamaño. La información de tamaño sin comprimir se puede proporcionar estableciendo la propiedad `rawSizeBytes` en los metadatos del blob con el tamaño de los datos *sin comprimir* expresado en bytes.

### <a name="rename-blob"></a>Cambio del nombre del blob

Si va a ingerir datos desde el almacenamiento de ADLSv2 y ha definido *Blob renamed* (Blob con nombre cambiado) como tipo de evento para la conexión de datos, el desencadenador para la ingesta de blobs es el cambio de nombre del blob. Para cambiar el nombre de un blob, vaya al blob en Azure Portal, haga clic con el botón derecho en el blob y seleccione **Cambiar nombre**:

   :::image type="content" source="../media/ingest-data-event-grid/rename-blob-in-the-portal.png" alt-text="Cambio del nombre de un blob en Azure Portal.":::

### <a name="ingestion-properties"></a>Propiedades de la ingesta

Puede especificar las [propiedades de la ingesta](data-explorer-ingest-event-grid-overview.md#ingestion-properties) de blobs mediante los metadatos del blob.

> [!NOTE]
> El Explorador de datos no eliminará los blobs con posterioridad a la ingesta.
> Conserve los blobs de tres a cinco días.
> Use [Administración del ciclo de vida de Azure Blob Storage](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) para administrar la eliminación de blobs.

## <a name="review-the-data-flow"></a>Revisión del flujo de datos

> [!NOTE]
> El Explorador de datos tiene una directiva de agregación (procesamiento por lotes) para la ingesta de datos diseñada para optimizar dicho proceso.
> De forma predeterminada, la directiva se configura en 5 minutos.
> Si es necesario, podrá modificar la directiva más adelante. En este artículo puede esperar una latencia de unos minutos.

1. En Azure Portal, en Event Grid, verá el pico de actividad cuando la aplicación se está ejecutando.

    :::image type="content" source="../media/ingest-data-event-grid/event-grid-graph.png" alt-text="Gráfico de actividad para Event Grid.":::

1. Ejecute la siguiente consulta en la base de datos de prueba para comprobar cuántos mensajes se han enviado a la base de datos hasta el momento.

    ```kusto
    TestTable
    | count
    ```

1. Para ver el contenido de los mensajes, ejecute la siguiente consulta en la base de datos de prueba.

    ```kusto
    TestTable
    ```

    El conjunto de resultados debe tener un aspecto similar al de la siguiente imagen:

    :::image type="content" source="../media/ingest-data-event-grid/table-result.png" alt-text="Conjunto de resultados del mensaje para Event Grid.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no planea usar volver a usar Event Grid, elimine la suscripción de Event Grid, el espacio de nombres del centro de eventos y el centro de eventos que se crearon automáticamente, con el fin de no incurrir en costos no deseados.

1. En Azure Portal, vaya al menú de la izquierda y seleccione **Todos los recursos**.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="Seleccione todos los recursos para la limpieza de Event Grid.":::

1. Busque el espacio de nombres de Event Hubs y seleccione **Eliminar** para eliminarlo:

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-find-event-hub-namespace-delete.png" alt-text="Eliminar espacio de nombres de Event Hubs.":::

1. En el formulario Eliminar recursos, confirme la eliminación del espacio de nombres y los recursos de Event Hubs.

1. Vaya a la cuenta de almacenamiento. En el menú de la izquierda, seleccione **Eventos**:

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="Seleccionar los eventos que desea eliminar de Event Grid.":::

1. Debajo del gráfico, seleccione la suscripción de Event Grid y, a continuación, seleccione **Eliminar** para eliminarla:

    :::image type="content" source="../media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="Elimine la suscripción de Event Grid.":::

1. Para eliminar la conexión de datos de Event Grid, vaya al clúster del Explorador de datos. En el menú de la izquierda, seleccione **Bases de datos**.

1. Seleccione la base de datos **TestDatabase**:

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="Seleccionar la base de datos para limpiar los recursos.":::

1. En el menú izquierdo, seleccione **Ingesta de datos**:

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="Seleccionar ingesta de datos para eliminar los recursos.":::

1. Seleccione la conexión de datos *test-grid-connection* y, a continuación, seleccione **Eliminar** para eliminarla.

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con Data Explorer](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos de Data Explorer](../data-explorer-monitor-pools.md)
