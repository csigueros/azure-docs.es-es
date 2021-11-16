---
title: Configuración de la ingesta de streaming en el grupo de Data Explorer de Azure Synapse
description: Descubra cómo configurar el grupo de Data Explorer de Azure Synapse y empezar a cargar datos con la ingesta de streaming.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: d7fdc238adda294a570b7656d62368a5d4feae9b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478268"
---
# <a name="configure-streaming-ingestion-on-your-azure-synapse-data-explorer-pool-preview"></a>Configuración de la ingesta de streaming en el grupo de Data Explorer de Azure Synapse (versión preliminar)

La ingesta de streaming es útil para cargar datos cuando necesite una latencia baja entre la ingesta y la consulta. Considere la posibilidad de usar la ingesta de streaming en los escenarios siguientes:

- Se requiere una latencia de menos de un segundo.
- Para optimizar el procesamiento operativo de muchas tablas donde el flujo de datos a cada tabla es relativamente pequeño (pocos registros por segundo), pero el volumen de ingesta de datos global es alto (miles de registros por segundo).

Si el flujo de datos en cada tabla es alto (más de 4 GB por hora), considere la posibilidad de usar la [ingesta por lotes](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context).

Para más información sobre los distintos métodos de ingesta, consulte la [información general de ingesta de datos](data-explorer-ingest-data-overview.md).

## <a name="choose-the-appropriate-streaming-ingestion-type"></a>Selección del tipo de ingesta de streaming adecuado

Se admiten dos tipos de ingesta de streaming:

| Tipo de ingesta | Descripción |
| -- | -- |
| **Event Hubs** o **IoT Hub** | Los centros de conectividad se configuran como orígenes de datos de streaming de tablas.<br />Para más información al respecto, consulte [**Centro de eventos**](data-explorer-ingest-event-hub-overview.md). <!--  or [**IoT Hub**](ingest-data-iot-hub.md) data ingestion methods --> |
| **Ingesta personalizada** | La ingesta personalizada requiere que escriba una aplicación que use una de las [bibliotecas cliente](/azure/data-explorer/kusto/api/client-libraries?context=/azure/synapse-analytics/context/context) de Data Explorer de Azure Synapse.<br />Use la información de este tema para configurar la ingesta personalizada. También puede resultar útil la [aplicación de ejemplo de ingesta de streaming de C#](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample). |

Use la tabla siguiente para ayudarle a elegir el tipo de ingesta adecuado para su entorno:

|Criterio|Event Hub o IoT Hub|Ingesta personalizada|
|---------|---------|---------|
|Retraso de datos entre el inicio de la ingesta y los datos disponibles para la consulta | Retraso más largo | Retraso más corto |
|Sobrecarga de desarrollo | Configuración rápida y sencilla, sin sobrecarga de desarrollo | Alta sobrecarga de desarrollo para crear una aplicación que ingiera los datos, controle los errores y garantice la coherencia de los datos |

> [!NOTE]
> La ingesta de datos de un centro de eventos en grupos de Data Explorer no funcionará si el área de trabajo de Synapse usa una red virtual administrada con la protección de filtración de datos habilitada.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Obtenga los puntos de conexión de consulta e ingesta de datos.
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="performance-and-operational-considerations"></a>Consideraciones operativas y de rendimiento

Los principales colaboradores que pueden afectar a la ingesta de streaming son:

- **Especificación de proceso**: las escalas de rendimiento y capacidad de la ingesta de streaming con grupos de Data Explorer de mayor tamaño. El número de solicitudes de ingesta simultáneas está limitado a seis por núcleo. Por ejemplo, para el tipo de carga de trabajo de 16 núcleos, como la optimizada para proceso (grande) y la optimizada para almacenamiento (grande), la carga máxima admitida es de 96 solicitudes de ingesta simultáneas. Para el tipo de carga de trabajo de dos núcleos, como la optimizada para proceso (muy pequeña), la carga máxima admitida es de 12 solicitudes de ingesta simultáneas.
- **Límite de tamaño de los datos**: el límite del tamaño de los datos para una solicitud de ingesta de streaming es de 4 MB.
- **Actualizaciones de esquema**: las actualizaciones de esquema, como la creación y modificación de tablas y asignaciones de ingesta, pueden tardar hasta cinco minutos en el servicio de ingesta de streaming. Para más información, consulte [Ingesta de streaming y cambios de esquema](/azure/data-explorer/kusto/management/data-ingestion/streaming-ingestion-schema-changes?context=/azure/synapse-analytics/context/context).
- **Capacidad de SSD**: cuando se habilita la ingesta de streaming en un grupo de Data Explorer, incluso cuando los datos no se ingieren a través de streaming, se usa parte del disco SSD local de las máquinas del grupo de Data Explorer para los datos de ingesta de streaming y se reduce el almacenamiento disponible para la caché activa.

## <a name="enable-streaming-ingestion-on-your-data-explorer-pool"></a>Habilitación de la ingesta de streaming en el grupo de Data Explorer

Para poder usar la ingesta de streaming, debe habilitar la funcionalidad en el grupo de Data Explorer y definir una [directiva de ingesta de streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context). Puede habilitar la funcionalidad al [crear el grupo de Data Explorer](#enable-streaming-ingestion-while-creating-a-new-data-explorer-pool) o [agregarla a un grupo de Data Explorer existente](#enable-streaming-ingestion-on-an-existing-data-explorer-pool).

> [!WARNING]
> Revise las [limitaciones](#limitations) antes de habilitar la ingesta de streaming.

### <a name="enable-streaming-ingestion-while-creating-a-new-data-explorer-pool"></a>Habilitación de la ingesta de streaming al crear un grupo de Data Explorer

Puede habilitar la ingesta de streaming al crear un grupo de Data Explorer mediante Azure Synapse Studio o Azure Portal.

#### <a name="studio"></a>[Estudio](#tab/azure-studio)

Al crear un grupo de Data Explorer con los pasos descritos en [Creación de un grupo de Data Explorer mediante Synapse Studio](../data-explorer-create-pool-studio.md#create-a-new-data-explorer-pool), en la pestaña **Configuración adicional**, seleccione **Ingesta de streaming** > **Habilitado**.

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-studio.png" alt-text="Habilitación de la ingesta de streaming al crear un grupo de Data Explorer en Data Explorer de Azure Synapse.":::

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Al crear un grupo de Data Explorer con los pasos descritos en [Creación de un grupo de Data Explorer mediante Azure Portal](../data-explorer-create-pool-portal.md#create-a-new-data-explorer-pool), en la pestaña **Configuración adicional**, seleccione **Ingesta de streaming** > **Habilitado**.

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-portal.png" alt-text="Habilitación de la ingesta de streaming al crear un grupo de Data Explorer en Data Explorer de Azure Synapse.":::

---

### <a name="enable-streaming-ingestion-on-an-existing-data-explorer-pool"></a>Habilitación de la ingesta de streaming en un grupo de Data Explorer existente

Si tiene un grupo de Data Explorer, puede habilitar la ingesta de streaming mediante Azure Portal.

1. En Azure Portal, vaya al grupo de Data Explorer.
1. En **Configuración**, seleccione **Configuraciones**.
1. En el panel **Configuraciones**, seleccione **Activado** para habilitar la **ingesta de streaming**.
1. Seleccione **Guardar**.

### <a name="create-a-target-table-and-define-the-policy"></a>Creación de una tabla de destino y definición de la directiva

Cree una tabla para recibir los datos de ingesta de streaming y defina su directiva relacionada mediante Azure Synapse Studio o Azure Portal.

#### <a name="studio"></a>[Estudio](#tab/azure-studio)

1. En Synapse Studio, en el panel izquierdo, seleccione **Desarrollar**.
1. En **KQL scripts** (Scripts de KQL), seleccione **&plus;** (Agregar un recurso nuevo) > **KQL script** (Script de KQL). En el panel derecho, puede asignar un nombre al script.
1. En el menú **Conectarse a**, seleccione *contosodataexplorer*.
1. En el menú **Use database** (Usar base de datos), seleccione *TestDatabase*.
1. Pegue el siguiente comando y seleccione **Ejecutar** para crear la tabla.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. Copie uno de los siguientes comandos en el **panel Consulta** y seleccione **Ejecutar**. Esto define la [directiva de ingesta de streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) en la tabla que ha creado o en la base de datos que contiene la tabla.

    > [!TIP]
    > Una directiva que se define en el nivel de base de datos se aplica a todas las tablas existentes y futuras de la base de datos.

    - Para definir la directiva en la tabla que ha creado, use:

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - Para definir la directiva en la base de datos que contiene la tabla que ha creado, use:

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. En el portal de Azure Synapse Analytics, vaya a su grupo de Data Explorer.
1. Seleccione **Consulta**.
1. Para crear la tabla que recibirá los datos mediante ingesta de streaming, copie el siguiente comando en el panel **Consulta** y seleccione **Ejecutar**.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. Copie uno de los siguientes comandos en el **panel Consulta** y seleccione **Ejecutar**. Esto define la [directiva de ingesta de streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) en la tabla que ha creado o en la base de datos que contiene la tabla.

    > [!TIP]
    > Una directiva que se define en el nivel de base de datos se aplica a todas las tablas existentes y futuras de la base de datos.

    - Para definir la directiva en la tabla que ha creado, use:

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - Para definir la directiva en la base de datos que contiene la tabla que ha creado, use:

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

---

## <a name="create-a-streaming-ingestion-application-to-ingest-data-to-your-data-explorer-pool"></a>Creación de una aplicación de ingesta de streaming para ingerir datos en el grupo de Data Explorer

Cree la aplicación para ingerir datos en el grupo de Data Explorer con el lenguaje que prefiera. Para la variable *poolPath*, use el punto de conexión de consulta que anotó en [Requisitos previos](#prerequisites).

### <a name="c"></a>[C#](#tab/csharp)

```csharp
using Kusto.Data;
using Kusto.Ingest;
using System.IO;
using Kusto.Data.Common;

namespace StreamingIngestion
{
    class Program
    {
        static void Main(string[] args)
        {
            string poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
            string appId = "<appId>";
            string appKey = "<appKey>";
            string appTenant = "<appTenant>";
            string dbName = "<dbName>";
            string tableName = "<tableName>";

            // Create Kusto connection string with App Authentication
            var csb =
                new KustoConnectionStringBuilder(poolPath)
                    .WithAadApplicationKeyAuthentication(
                        applicationClientId: appId,
                        applicationKey: appKey,
                        authority: appTenant
                    );

            // Create a disposable client that will execute the ingestion
            using (IKustoIngestClient client = KustoIngestFactory.CreateStreamingIngestClient(csb))
            {
                // Initialize client properties
                var ingestionProperties =
                    new KustoIngestionProperties(
                        databaseName: dbName,
                        tableName: tableName
                    );

                // Ingest from a compressed file
                var fileStream = File.Open("MyFile.gz", FileMode.Open);
                // Create source options
                var sourceOptions = new StreamSourceOptions()
                {
                    CompressionType = DataSourceCompressionType.GZip,
                };
                // Ingest from stream
                var status = client.IngestFromStreamAsync(fileStream, ingestionProperties, sourceOptions).GetAwaiter().GetResult();
            }
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
from Azure Synapse Analytics.kusto.data import KustoConnectionStringBuilder

from Azure Synapse Analytics.kusto.ingest import (
    IngestionProperties,
    DataFormat,
    KustoStreamingIngestClient
)

poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
appId = "<appId>"
appKey = "<appKey>"
appTenant = "<appTenant>"
dbName = "<dbName>"
tableName = "<tableName>"

csb = KustoConnectionStringBuilder.with_aad_application_key_authentication(
    poolPath,
    appId,
    appKey,
    appTenant
)
client = KustoStreamingIngestClient(csb)

ingestionProperties = IngestionProperties(
    database=dbName,
    table=tableName,
    data_format=DataFormat.CSV
)

# Ingest from file
# Automatically detects gz format
client.ingest_from_file("MyFile.gz", ingestion_properties=ingestionProperties) 
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Load modules using ES6 import statements:
import { DataFormat, IngestionProperties, StreamingIngestClient } from "azure-kusto-ingest";
import { KustoConnectionStringBuilder } from "azure-kusto-data";

// For earlier version, load modules using require statements:
// const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
// const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
// const {DataFormat} = require("azure-kusto-ingest").IngestionPropertiesEnums;
// const StreamingIngestClient = require("azure-kusto-ingest").StreamingIngestClient;

const poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
const appId = "<appId>";
const appKey = "<appKey>";
const appTenant = "<appTenant>";
const dbName = "<dbName>";
const tableName = "<tableName>";
const mappingName = "<mappingName>"; // Required for JSON formatted files

const ingestionProperties = new IngestionProperties({
    database: dbName, // Your database
    table: tableName, // Your table
    format: DataFormat.JSON,
    ingestionMappingReference: mappingName
});

// Initialize client with engine endpoint
const client = new StreamingIngestClient(
    KustoConnectionStringBuilder.withAadApplicationKeyAuthentication(
        poolPath,
        appId,
        appKey,
        appTenant
    ),
    ingestionProperties
);

// Automatically detects gz format
await client.ingestFromFile("MyFile.gz", ingestionProperties);
```

### <a name="go"></a>[Go](#tab/go)

```go
import (
    "context"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto/ingest"
    "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
)

func ingest() {
    poolPath := "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
    appId := "<appId>"
    appKey := "<appKey>"
    appTenant := "<appTenant>"
    dbName := "<dbName>"
    tableName := "<tableName>"
    mappingName := "<mappingName>" // Optional, can be nil

    // Creates a Kusto Authorizer using your client identity, secret, and tenant identity.
    // You may also uses other forms of authorization, see GoDoc > Authorization type.
    // auth package is: "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
    authorizer := kusto.Authorization{
        Config: auth.NewClientCredentialsConfig(appId, appKey, appTenant),
    }

    // Create a client
    client, err := kusto.New(poolPath, authorizer)
    if err != nil {
        panic("add error handling")
    }

    // Create an ingestion instance
    // Pass the client, the name of the database, and the name of table you wish to ingest into.
    in, err := ingest.New(client, dbName, tableName)
    if err != nil {
        panic("add error handling")
    }

    // Go currently only supports streaming from a byte array with a maximum size of 4 MB.
    jsonEncodedData := []byte("{\"a\":  1, \"b\":  10}\n{\"a\":  2, \"b\":  20}")

    // Ingestion from a stream commits blocks of fully formed data encodes (JSON, AVRO, ...) into Kusto:
    if err := in.Stream(context.Background(), jsonEncodedData, ingest.JSON, mappingName); err != nil {
        panic("add error handling")
    }
}
```

### <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.Azure Synapse Analytics.kusto.data.auth.ConnectionStringBuilder;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClient;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClientFactory;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestionProperties;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.result.OperationStatus;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.CompressionType;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.StreamSourceInfo;
import java.io.FileInputStream;
import java.io.InputStream;

public class FileIngestion {
    public static void main(String[] args) throws Exception {
        String poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
        String appId = "<appId>";
        String appKey = "<appKey>";
        String appTenant = "<appTenant>";
        String dbName = "<dbName>";
        String tableName = "<tableName>";

        // Build connection string and initialize
        ConnectionStringBuilder csb =
            ConnectionStringBuilder.createWithAadApplicationCredentials(
                poolPath,
                appId,
                appKey,
                appTenant
            );

        // Initialize client and its properties
        IngestClient client = IngestClientFactory.createClient(csb);
        IngestionProperties ingestionProperties =
            new IngestionProperties(
                dbName,
                tableName
            );

        // Ingest from a compressed file
        // Create Source info
        InputStream zipInputStream = new FileInputStream("MyFile.gz");
        StreamSourceInfo zipStreamSourceInfo = new StreamSourceInfo(zipInputStream);
        // If the data is compressed
        zipStreamSourceInfo.setCompressionType(CompressionType.gz);
        // Ingest from stream
        OperationStatus status = client.ingestFromStream(zipStreamSourceInfo, ingestionProperties).getIngestionStatusCollection().get(0).status;
    }
}
```

---

## <a name="disable-streaming-ingestion-on-your-data-explorer-pool"></a>Deshabilitación de la ingesta de streaming en el grupo de Data Explorer

> [!WARNING]
> La deshabilitación de la ingesta de streaming puede tardar unas horas.

Antes de deshabilitar la ingesta de streaming en el grupo de Data Explorer, quite la [directiva de ingesta de streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) de todas las tablas y bases de datos pertinentes. La eliminación de la directiva de ingesta de streaming desencadena la reorganización de los datos dentro del grupo de Data Explorer. Los datos de ingesta de streaming se trasladan desde el almacenamiento inicial hasta el almacenamiento permanente en el almacén de columnas (extensiones o particiones). Este proceso puede tardar entre unos segundos y algunas horas, en función de la cantidad de datos existentes en el almacenamiento inicial.

### <a name="drop-the-streaming-ingestion-policy"></a>Eliminación de la directiva de ingesta de streaming

Puede quitar la directiva de ingesta de streaming mediante Azure Synapse Studio o Azure Portal.

#### <a name="studio"></a>[Estudio](#tab/azure-studio)

1. En Synapse Studio, en el panel izquierdo, seleccione **Desarrollar**.
1. En **KQL scripts** (Scripts de KQL), seleccione **&plus;** (Agregar un recurso nuevo) > **KQL script** (Script de KQL). En el panel derecho, puede asignar un nombre al script.
1. En el menú **Conectarse a**, seleccione *contosodataexplorer*.
1. En el menú **Use database** (Usar base de datos), seleccione *TestDatabase*.
1. Pegue el siguiente comando y seleccione **Ejecutar** para crear la tabla.

    ```kusto
    .delete table TestTable policy streamingingestion
    ```

1. En Azure Portal, vaya al grupo de Data Explorer.
1. En **Configuración**, seleccione **Configuraciones**.
1. En el panel **Configuraciones**, seleccione **Activado** para habilitar la **ingesta de streaming**.
1. Seleccione **Guardar**.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. En Azure Portal, vaya al grupo de Data Explorer y seleccione **Consulta**.
1. Para quitar la directiva de ingesta de streaming de la tabla, copie el siguiente comando en el **panel Consulta** y seleccione **Ejecutar**.

    ```Kusto
    .delete table TestTable policy streamingingestion
    ```

1. En **Configuración**, seleccione **Configuraciones**.
1. En el panel **Configuraciones**, seleccione **Desactivado** para deshabilitar la **ingesta de streaming**.
1. Seleccione **Guardar**.

---

## <a name="limitations"></a>Limitaciones

- No se admiten los [cursores de base de datos](/azure/data-explorer/kusto/management/databasecursor?context=/azure/synapse-analytics/context/context) en una base de datos si ella o cualquiera de sus tablas tienen la [directiva de ingesta de streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) definida y habilitada.
- La [asignación de datos](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) debe haberse [creado con anterioridad](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) para su uso en la ingesta de streaming. Las solicitudes individuales de ingesta de streaming no acomodan asignaciones de datos insertadas.
- No se pueden establecer [etiquetas de extensión](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging) en los datos de ingesta de streaming.
- [Directiva de actualización](/azure/data-explorer/kusto/management/updatepolicy?context=/azure/synapse-analytics/context/context). La directiva de actualización solo puede hacer referencia a los datos que se acaban de ingerir en la tabla de origen y no a otros datos o tablas de la base de datos.
- Si la ingesta de streaming se usa en cualquiera de las tablas de la base de datos, esta base de datos no se puede usar como principal con las bases de datos secundarias ni como proveedor de datos para Data Share de Azure Synapse Analytics.

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con Data Explorer](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos de Data Explorer](../data-explorer-monitor-pools.md)
