---
title: Información general de la ingesta de datos del Explorador de datos de Azure Synapse (versión preliminar)
description: Obtenga información acerca de las diferentes maneras que puede usar para la ingesta (carga) de datos en el Explorador de datos de Azure Synapse.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 739867e66bf111d19226ea99198d4d5efec0eeb5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478139"
---
# <a name="azure-synapse-data-explorer-data-ingestion-overview-preview"></a>Información general de la ingesta de datos del Explorador de datos de Azure Synapse (versión preliminar)

La ingesta de datos es el proceso que se usa para cargar los registros de datos de uno o varios orígenes para importar datos en una tabla en un grupo del Explorador de datos de Azure Synapse. Una vez ingeridos, los datos están disponibles para su consulta.

El servicio de administración de datos del Explorador de datos de Azure Synapse, que es el responsable de la ingesta de datos, implementa el siguiente proceso:

- Extrae los datos por lotes o en streaming de un origen externo y lee las solicitudes de una cola de Azure pendiente.
- El procesamiento por lotes de los datos que fluyen en la misma base de datos y tabla se optimiza para mejorar el rendimiento de la ingesta.
- Se validan los datos iniciales y el formato se convierte cuando sea necesario.
- Una posterior manipulación de los datos incluye hacer coincidir los esquemas, así como organizar, indexar, codificar y comprimir los datos.
- Los datos se conservan en el almacenamiento de acuerdo con la directiva de retención establecida.
- Los datos ingeridos se confirman en el motor, donde están disponibles para su consulta.

## <a name="supported-data-formats-properties-and-permissions"></a>Formatos de datos compatibles, propiedades y permisos

* **[Formatos de datos compatibles](data-explorer-ingest-data-supported-formats.md)**

* **[Propiedades de la ingesta](data-explorer-ingest-data-properties.md)** : las propiedades que afectan a la forma en que se van a ingerir los datos (por ejemplo, etiquetado, asignación u hora de creación).

* **Permisos**: Para ingerir datos, el proceso necesita [permisos de nivel de agente de ingesta de bases de datos](/azure/data-explorer/kusto/management/access-control/role-based-authorization?context=/azure/synapse-analytics/context/context). Otras acciones, como la consulta, pueden requerir permisos de administrador de base de datos, usuario de base de datos o administrador de tabla.

## <a name="batching-vs-streaming-ingestions"></a>Ingesta de procesamiento por lotes frente a ingesta de streaming

* La ingesta de procesamiento por lotes realiza el procesamiento por lotes de los datos y está optimizada para lograr un alto rendimiento de la ingesta. Este método es el tipo de ingesta preferido y de mayor rendimiento. Los datos se procesan por lotes en función de las propiedades de la ingesta. Se combinan y optimizan pequeños lotes de datos para agilizar los resultados de la consulta. La directiva de [procesamiento por lotes de la ingesta](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) se puede establecer en bases de datos o en tablas. De forma predeterminada, el valor máximo del procesamiento por lotes es de 5 minutos, 1000 elementos o un tamaño total de 1 GB.  El límite de tamaño de los datos para un comando de ingesta por lotes es de 4 GB.

* [Ingesta en streaming](data-explorer-ingest-data-streaming.md) es la ingesta de datos en curso desde un origen de streaming. La ingesta de streaming permite una latencia casi en tiempo real para pequeños conjuntos pequeños de datos por tabla. En un principio, los datos se ingieren en el almacén de filas y posteriormente se mueven a las extensiones del almacén de columnas.

## <a name="ingestion-methods-and-tools"></a>Métodos y herramientas de ingesta

El Explorador de datos de Azure Synapse admite varios métodos de ingesta, cada uno con sus propios escenarios de destino. Estos métodos incluyen herramientas de ingesta, conectores y complementos para diversos servicios, canalizaciones administradas, ingesta mediante programación mediante distintos SDK y acceso directo a la ingesta.

### <a name="ingestion-using-managed-pipelines"></a>Ingesta mediante canalizaciones administradas

Para aquellas organizaciones que deseen que sea un servicio externo el que realice la administración (límites, reintentos, supervisiones, alertas, etc.), es probable que un conector sea la solución más adecuada. La ingesta en cola es apropiada para grandes volúmenes de datos. El Explorador de datos de Azure Synapse admite las siguientes instancias de Azure Pipelines:

<!-- * **[Event Grid](https://azure.microsoft.com/services/event-grid/)**: A pipeline that listens to Azure storage, and updates Azure Synapse Data Explorer to pull information when subscribed events occur. For more information, see [Ingest Azure Blobs into Azure Synapse Data Explorer](ingest-data-event-grid.md). -->

* **[Centro de eventos](https://azure.microsoft.com/services/event-hubs/)** : una canalización que transfiere eventos de los servicios al Explorador de datos de Azure Synapse. Para más información, consulte [Ingesta de datos del centro de eventos en el Explorador de datos de Azure Synapse](data-explorer-ingest-event-hub-overview.md).

<!-- * **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)**: A pipeline that is used for the transfer of data from supported IoT devices to Azure Synapse Data Explorer. For more information, see [Ingest from IoT Hub](ingest-data-iot-hub.md). -->

* **Canalizaciones de Synapse**: un servicio de integración de datos totalmente administrado para cargas de trabajo analíticas en [canalizaciones de Synapse](/azure/data-factory/copy-activity-overview?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) se conecta con más de 90 orígenes admitidos para proporcionar una transferencia de datos eficaz y resistente. Las canalizaciones de Synapse preparan, transforman y enriquecen los datos para proporcionar información que se puede supervisar de varias formas. Este servicio se puede usar como solución de un solo uso, en una escala de tiempo periódica o desencadenada por eventos específicos.

<!-- ### Ingestion using connectors and plugins

* **Logstash plugin**, see [Ingest data from Logstash to Azure Synapse Data Explorer](ingest-data-logstash.md).

* **Kafka connector**, see [Ingest data from Kafka into Azure Synapse Data Explorer](ingest-data-kafka.md).

* **[:::no-loc text="Power Automate":::](https://flow.microsoft.com/)**: An automated workflow pipeline to Azure Synapse Data Explorer. :::no-loc text="Power Automate"::: can be used to execute a query and do preset actions using the query results as a trigger. See [Azure Synapse Data Explorer connector to :::no-loc text="Power Automate"::: (Preview)](flow.md).

* **Apache Spark connector**: An open-source project that can run on any Spark cluster. It implements data source and data sink for moving data across Azure Synapse Data Explorer and Spark clusters. You can build fast and scalable applications targeting data-driven scenarios. See [Azure Synapse Data Explorer Connector for Apache Spark](spark-connector.md). -->

### <a name="programmatic-ingestion-using-sdks"></a>Ingesta mediante programación mediante SDK

El Explorador de datos de Azure Synapse proporciona SDK que pueden usarse para la consulta e ingesta de datos. La ingesta mediante programación está optimizada para reducir los costos de ingesta (COG), minimizando las transacciones de almacenamiento durante y después del proceso de ingesta.

Antes de empezar, siga estos pasos para obtener los puntos de conexión del grupo del Explorador de datos para configurar la ingesta mediante programación.

[!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

**SDK y proyectos de código abierto disponibles**

* [SDK de Python](/azure/data-explorer/kusto/api/python/kusto-python-client-library?context=/azure/synapse-analytics/context/context)

* [SDK de .NET](/azure/data-explorer/kusto/api/netfx/about-the-sdk?context=/azure/synapse-analytics/context/context)

* [SDK de Java](/azure/data-explorer/kusto/api/java/kusto-java-client-library?context=/azure/synapse-analytics/context/context)

* [SDK de Node](/azure/data-explorer/kusto/api/node/kusto-node-client-library?context=/azure/synapse-analytics/context/context)

* [REST API](/azure/data-explorer/kusto/api/netfx/kusto-ingest-client-rest?context=/azure/synapse-analytics/context/context)

* [SDK de GO](/azure/data-explorer/kusto/api/golang/kusto-golang-client-library?context=/azure/synapse-analytics/context/context)

### <a name="tools"></a>Herramientas

* **[Ingesta con un solo clic](data-explorer-ingest-data-one-click.md)** : permite ingerir datos rápidamente mediante la creación y ajuste de tablas a partir de una amplia gama de tipos de origen. La ingesta con un solo clic sugiere tablas y estructuras de asignación automáticamente en función del origen de datos del Explorador de datos de Azure Synapse. La ingesta con un solo clic se puede usar para la ingesta puntual, o bien para definir una ingesta continua a través de Event Grid en el contenedor en el que se han ingerido los datos.

<!-- * **[LightIngest](lightingest.md)**: A command-line utility for ad-hoc data ingestion into Azure Synapse Data Explorer. The utility can pull source data from a local folder or from an Azure blob storage container. -->

### <a name="kusto-query-language-ingest-control-commands"></a>Comandos de control de ingesta del lenguaje de consulta de Kusto

Hay varios métodos por los que los datos se pueden ingerir directamente al motor mediante los comandos del lenguaje de consulta de Kusto (KQL). Dado que este método omite los servicios de Administración de datos, solo es adecuado para la exploración y la creación de prototipos. No se debe usar en escenarios de producción o de gran volumen.

  * **Ingesta insertada**:  se envía un comando de control [.ingest inline](/azure/data-explorer/kusto/management/data-ingestion/ingest-inline?context=/azure/synapse-analytics/context/context) al motor y los datos que se van a ingerir forman parte del propio texto del comando. Este método está pensado para la realización de pruebas improvisadas.

  * **Ingesta desde consulta**: se envía un comando de control [.set, .append, .set-or-append o .set-or-replace](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-query?context=/azure/synapse-analytics/context/context) al motor y los datos se especifican indirectamente como los resultados de una consulta o un comando.

  * **Ingesta desde almacenamiento (extracción)** : se envía un comando de control [.ingest into](/data-explorer/kusto/management/data-ingestion/ingest-from-storage?context=/azure/synapse-analytics/context/context) al motor con los datos almacenados en algún almacenamiento externo (por ejemplo, Azure Blob Storage) al que el motor puede acceder y al que el comando señala.

Para ver un ejemplo del uso de comandos de control de ingesta, consulte [Análisis con el Explorador de datos](../../get-started-analyze-data-explorer.md).

<!-- ## Comparing ingestion methods and tools

| Ingestion name | Data type | Maximum file size | Streaming, batching, direct | Most common scenarios | Considerations |
| --- | --- | --- | --- | --- | --- |
| [**One click ingestion**](ingest-data-one-click.md) | *sv, JSON | 1 GB uncompressed (see note)| Batching to container, local file and blob in direct ingestion | One-off, create table schema, definition of continuous ingestion with event grid, bulk ingestion with container (up to 10,000 blobs) | 10,000 blobs are randomly selected from container|
| [**LightIngest**](lightingest.md) | All formats supported | 1 GB uncompressed (see note) | Batching via DM or direct ingestion to engine |  Data migration, historical data with adjusted ingestion timestamps, bulk ingestion (no size restriction)| Case-sensitive, space-sensitive |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX to Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory (ADF)**](./data-factory-integration.md) | [Supported data formats](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | unlimited *(per ADF restrictions) | Batching or per ADF trigger | Supports formats that are usually unsupported, large files, can copy from over 90 sources, from on perm to cloud | This method takes relatively more time until data is ingested. ADF uploads all data to memory and then begins ingestion. |
|[ **Power Automate**](./flow.md) | | | | Ingestion commands as part of flow| Must have high-performing response time |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [Supported data formats](ingest-data-iot-hub-overview.md#data-format)  | N/A | Batching, streaming | IoT messages, IoT events, IoT properties | |
| [**Event Hub**](ingest-data-event-hub-overview.md) | [Supported data formats](ingest-data-event-hub-overview.md#data-format) | N/A | Batching, streaming | Messages, events | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [Supported data formats](ingest-data-event-grid-overview.md#data-format) | 1 GB uncompressed | Batching | Continuous ingestion from Azure storage, external data in Azure storage | Ingestion can be triggered by blob renaming or blob creation actions. |
| [**.NET SDK**](./net-sdk-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Python**](python-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Node.js**](node-ingest-data.md) | All formats supported | 1 GB uncompressed (see note | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct| Write your own code according to organizational needs |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |

> [!Note]
> When referenced in the above table, ingestion supports a maximum file size of 4 GB. The recommendation is to ingest files between 100 MB and 1 GB. -->

## <a name="ingestion-process"></a>Proceso de ingesta

Una vez que haya elegido el método de ingesta que más se ajuste a sus necesidades, siga estos pasos:

1. **Establecimiento de una directiva de retención**

    Los datos ingeridos en una tabla del Explorador de datos de Azure Synapse están sujetos a la directiva de retención vigente de la tabla. Salvo que la directiva de retención vigente se establezca explícitamente en una tabla, deriva de la directiva de retención de la base de datos. La retención activa es una función del tamaño del clúster y de la directiva de retención. Si el espacio disponible es insuficiente para la cantidad de datos que se ingieren se obligará a realizar una retención esporádica de los primeros datos.

    Asegúrese de que la directiva de retención de la base de datos se ajusta a sus necesidades. Si no es así, anúlela explícitamente en el nivel de tabla. Para más información, consulte [Directiva de retención](/azure/data-explorer/kusto/management/retentionpolicy?context=/azure/synapse-analytics/context/context).

1. **de una tabla**

    Para poder ingerir datos, es preciso crear una tabla con antelación. Use una de las siguientes opciones:

    * Cree una tabla con un comando. Para ver un ejemplo del uso del comando de creación de una tabla, consulte [Análisis con el Explorador de datos](../../get-started-analyze-data-explorer.md).

    * Cree una tabla con [ingesta con un solo clic](data-explorer-ingest-data-one-click.md).

    > [!Note]
    > Si un registro está incompleto o un campo no se puede analizar como tipo el de datos necesarios, las columnas de tabla correspondientes se rellenará con valores nulos.

1. **Creación de la asignación de esquemas**

    La [asignación de esquemas](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) ayuda a enlazar los campos de datos de origen a las columnas de la tabla de destino. La asignación permite tomar datos de distintos orígenes en la misma tabla, en función de los atributos definidos. Se admiten diferentes tipos de asignaciones, tanto orientadas a filas (CSV, JSON y AVRO) como orientadas a columnas (Parquet). En la mayoría de los métodos, las asignaciones también se pueden [crear previamente en la tabla](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) y hacer referencia a ellas desde el parámetro de comando de ingesta.

1. **Establecimiento de una directiva de actualización** (opcional)

   Algunas de las asignaciones de formato de datos (Parquet, JSON y Avro) admiten transformaciones sencillas y útiles en el momento de la ingesta. Si el escenario requiere un procesamiento más complejo en el momento de la ingesta, use la directiva de actualización, lo que permite el procesamiento ligero mediante los comandos del lenguaje de consulta de Kusto. La directiva de actualización ejecuta automáticamente extracciones y transformaciones en los datos ingeridos en la tabla original e ingiere los datos resultantes en una o varias tablas de destino. Establezca la [directiva de actualización](/azure/data-explorer/kusto/management/update-policy?context=/azure/synapse-analytics/context/context).



## <a name="next-steps"></a>Pasos siguientes

- [Formatos de datos compatibles](data-explorer-ingest-data-supported-formats.md)
- [Propiedades de la ingesta que se admiten](data-explorer-ingest-data-properties.md)
