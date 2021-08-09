---
title: Creación de un panel en tiempo real con Azure Cosmos DB, Azure Analysis Services y Power BI
description: Aprenda a crear un panel meteorológico en directo en Power BI con Azure Cosmos DB y Azure Analysis Services.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 07da25320cded38a037e6e557ff55ebfaeb3a2df
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111959176"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Creación de un panel en tiempo real mediante Azure Cosmos DB y Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En este artículo se describen los pasos necesarios para crear un panel meteorológico en directo en Power BI con el conector OLTP de Azure Cosmos DB y Azure Analysis Services. En el panel de Power BI se mostrarán los gráficos para mostrar información casi en tiempo real sobre la temperatura y las precipitaciones de una región.

Otra opción es crear informes casi en tiempo real mediante [Azure Synapse Link para Azure Cosmos DB](synapse-link.md). Con Azure Synapse Link, puede conectarse a Power BI para analizar los datos de Azure Cosmos DB, sin ningún impacto en el rendimiento ni en el costo de las cargas de trabajo transaccionales y sin canalizaciones de ETL. Puede usar los modos [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) o [importación](/power-bi/connect-data/service-dataset-modes-understand#import-mode). Para más información, haga clic [aquí](synapse-link-power-bi.md).


## <a name="reporting-scenarios"></a>Escenarios de informes

Hay varias maneras de configurar paneles de informes en los datos almacenados en Azure Cosmos DB. En función de los requisitos de obsolescencia y del tamaño de los datos, en la tabla siguiente se describe la configuración de informes para cada escenario:


|Escenario |Configurar |
|---------|---------|
|1. Generación de informes en tiempo real sobre grandes conjuntos de datos con agregados     | **Opción 1**: [Power BI y Azure Synapse Link con DirectQuery](./synapse-link-power-bi.md)<br />  **Option 2**: [Power BI y conector de Spark con DirectQuery + Azure Databricks + Conector de Spark de Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)<br />  **Opción 3**: Power BI y conector de Azure Analysis Services con DirectQuery + Azure Analysis Services + Azure Databricks + Conector de Spark de Cosmos DB     |
|2. Generación de informes en tiempo real sobre grandes conjuntos de datos (>= 10 GB)    |  **Opción 1**: [Power BI y Azure Synapse Link con DirectQuery](./synapse-link-power-bi.md)<br />  **Opción 2**: [Power BI y conector de Azure Analysis Services con DirectQuery + Azure Analysis Services](create-real-time-weather-dashboard-powerbi.md)       |
|3. Generación de informes ad hoc sobre grandes conjuntos de datos (< 10 GB)     |  [Conector de Azure Cosmos DB con Power BI con modo de importación y actualización incremental](create-real-time-weather-dashboard-powerbi.md)       |
|4. Generación de informes ad hoc con actualización periódica   |  [Conector de Azure Cosmos DB con Power BI con el modo de importación (actualización periódica programada)](powerbi-visualize.md)       |
|5. Generación de informes ad hoc (sin actualización)    |  [Conector de Azure Cosmos DB con Power BI con el modo de importación](powerbi-visualize.md)       |


Los escenarios 4 y 5 se pueden configurar fácilmente mediante el [conector de Power BI con Azure Cosmos DB](powerbi-visualize.md). En este artículo se describen las siguientes configuraciones para los escenarios 2 (opción 2) y 3.

### <a name="power-bi-with-incremental-refresh"></a>Power BI con actualización incremental

Power BI tiene un modo en el que se puede configurar la actualización incremental. Este modo elimina la necesidad de crear y administrar particiones de Azure Analysis Services. La actualización incremental puede configurarse para filtrar únicamente las actualizaciones más recientes de grandes conjuntos de datos. Sin embargo, este modo solo funciona con el servicio Power BI Premium que tiene una limitación del conjunto de datos de 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Conector de Azure Analysis de Power BI + Azure Analysis Services

Azure Analysis Services proporciona una plataforma completamente administrada como un servicio que hospeda modelos de datos en la nube de nivel empresarial. Los conjuntos de datos masivos se pueden cargar de Azure Cosmos DB en Azure Analysis Services. Para evitar consultar constantemente el conjunto de datos completo, los conjuntos de datos se pueden subdividir en particiones de Azure Analysis Services, que se pueden actualizar de forma independiente en distintas frecuencias.

## <a name="power-bi-incremental-refresh"></a>Actualización incremental de Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingesta de datos meteorológicos en Azure Cosmos DB

Configure una canalización de ingesta para cargar [datos meteorológicos](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation) en Azure Cosmos DB. Puede configurar un trabajo de [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) para cargar periódicamente los datos meteorológicos más recientes en Azure Cosmos DB mediante el receptor de Cosmos DB y de origen HTTP.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Conexión de Power BI con Azure Cosmos DB

1. **Conectar la cuenta de Azure Cosmos con Power BI**: abra Power BI Desktop y use el conector de Azure Cosmos DB para seleccionar la base de datos y el contenedor correctos.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Conector de Power BI de Azure Cosmos DB":::

1. **Configurar actualización incremental**: siga los pasos descritos en el artículo [Actualización incremental con Power BI](/power-bi/service-premium-incremental-refresh) para configurar la actualización incremental para el conjunto de datos. Agregue los parámetros **RangeStart** y **RangeEnd**, tal y como se muestra en la siguiente captura de pantalla:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Configuración de parámetros de intervalo":::

   Como el conjunto de datos tiene una columna de fecha que tiene formato de texto, los parámetros **RangeStart** y **RangeEnd** se deben transformar para usar el siguiente filtro. En el panel **Editor avanzado**, modifique la consulta y agregue el siguiente texto para filtrar las filas en función de los parámetros RangeStart y RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   En función de la columna y el tipo de datos que se encuentren en el conjunto de datos de origen, puede cambiar los campos RangeStart y RangeEnd en consecuencia.

   
   |Propiedad  |Tipo de datos  |Filter  |
   |---------|---------|---------|
   |_ts        |   Numeric      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) y [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Fecha (por ejemplo:- 2019-08-19)     |   String      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") y [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Fecha (por ejemplo:- 2019-08-11 12:00:00)   |  String       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") y [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **Definir la directiva de actualización**: defina la directiva de actualización; para ello, vaya a la pestaña **Actualización incremental** en el menú **contextual** de la tabla. Establezca la directiva de actualización para que se actualice **cada día** y almacene los datos del último mes.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Definición de la directiva de actualización":::

   Omita la advertencia que indica *que la consulta M no se puede confirmar que se vaya a incorporar*. El conector de Azure Cosmos DB incorpora las consultas de filtro.

1. **Cargar los datos y generar los informes**: mediante el uso de los datos cargados anteriormente, cree los gráficos para informar de la temperatura y las precipitaciones.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Carga de datos y generación de informe":::

1. **Publicar el informe en Power BI Premium**: como la actualización incremental es una característica solo Premium, el cuadro de diálogo Publicar únicamente permite seleccionar un área de trabajo en la funcionalidad Premium. La primera actualización puede tardar más tiempo en importar los datos históricos. Las actualizaciones de datos posteriores son mucho más rápidas porque utilizan la actualización incremental.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Conector de Azure Analysis de Power BI + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingesta de datos meteorológicos en Azure Cosmos DB 

Configure una canalización de ingesta para cargar [datos meteorológicos](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation) en Azure Cosmos DB. Puede configurar un trabajo de Azure Data Factory (ADF) para cargar periódicamente los datos meteorológicos más recientes en Azure Cosmos DB mediante el receptor de Cosmos DB y de origen HTTP.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Conexión de Azure Analysis Services con la cuenta de Azure Cosmos

1. **Cree un nuevo clúster de Azure Analysis Services** - [Cree una instancia de Azure Analysis Services](../analysis-services/analysis-services-create-server.md) en la misma región que la cuenta de Azure Cosmos y el clúster de Databricks.

1. **Cree un nuevo proyecto tabular de Analysis Services en Visual Studio** -  [Instale SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) y cree un proyecto tabular de Analysis Services en Visual Studio.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Creación de un proyecto de Azure Analysis Services":::

   Elija la instancia del **área de trabajo integrada** y establezca el nivel de compatibilidad en **SQL Server 2017/Azure Analysis Services (1400)** .

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Diseñador del modelo tabular de Azure Analysis Services":::

1. **Agregue el origen de datos de Azure Cosmos DB**: vaya a **Modelos**> **Orígenes de datos** > **Origen de datos nuevo** y agregue el origen de datos de Azure Cosmos DB, tal como se muestra en la siguiente captura de pantalla:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Adición del origen de datos de Cosmos DB":::

   Conéctese a Azure Cosmos DB proporcionando el **URI de la cuenta**, el **nombre de la base de datos** y el **nombre del contenedor**. Ahora puede ver que los datos del contenedor de Azure Cosmos se importan en Power BI.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Previsualización de datos de Azure Cosmos DB":::

1. **Construir el modelo de Analysis Services**: abra el editor de consultas y realice las operaciones necesarias para optimizar el conjunto de datos cargado:

   * Extraer solo las columnas relacionadas con el tiempo meteorológico (temperatura y precipitaciones)

   * Extraer la información mensual de la tabla. Estos datos son útiles en la creación de particiones, tal y como se describe en la sección siguiente.

   * Convertir las columnas de temperatura en número

   La expresión M resultante es la siguiente:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Además, cambie el tipo de datos de las columnas de temperatura a Decimal para asegurarse de que estos valores se pueden trazar en Power BI.

1. **Crear particiones de Azure Analysis**: cree particiones en Azure Analysis Services para dividir el conjunto de datos en particiones lógicas que se pueden actualizar de forma independiente y a diferentes frecuencias. En este ejemplo, se van a crear dos particiones que dividen el conjunto de datos en los datos del mes más reciente y en todo lo demás.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Creación de particiones de Analysis Services":::

   Cree las dos particiones siguientes en Azure Analysis Services:

   * **Latest Month** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")` (Último mes)
   * **Historical** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")` (Historial)

1. **Implementar el modelo en Azure Analysis Server**: haga clic con el botón derecho en el proyecto de Azure Analysis Services y elija **Implementar**. Agregue el nombre del servidor en el panel de **propiedades de Servidor de implementación**.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Implementación del modelo de Azure Analysis Services":::

1. **Configurar actualizaciones y combinaciones de particiones**: Azure Analysis Services permite el procesamiento independiente de las particiones. Puesto que deseamos que la partición del **mes más reciente** se actualice constantemente con los datos más recientes, establezca el intervalo de actualización en cinco minutos. Puede actualizar los datos mediante la [API REST](../analysis-services/analysis-services-async-refresh.md), [automatización de Azure](../analysis-services/analysis-services-refresh-azure-automation.md) o con una [aplicación lógica](../analysis-services/analysis-services-refresh-logic-app.md). No es necesario actualizar los datos de la partición histórica. Además, debe escribir código para consolidar la partición del mes más reciente en la partición del historial y crear una nueva partición del mes más reciente.

## <a name="connect-power-bi-to-analysis-services"></a>Conexión de Power BI con Analysis Services

1. **Conectar a Azure Analysis Server mediante el conector de bases de datos de Azure Analysis Services**: elija el **modo activo** y conéctese a la instancia de Azure Analysis Services, tal como se muestra en la siguiente captura de pantalla:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Obtención de datos de Azure Analysis Services":::

1. **Cargar los datos y generar informes**: mediante el uso de los datos cargados anteriormente, cree los gráficos para informar de la temperatura y las precipitaciones. Dado que está creando una conexión activa, las consultas se deben ejecutar en los datos del modelo de Azure Analysis Services que ha implementado en el paso anterior. Los gráficos de temperatura se actualizarán en un plazo de cinco minutos después de que los nuevos datos se carguen en Azure Cosmos DB.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Carga de los datos y generación de informes":::

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre Power BI, consulte [Introducción a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Conexión de Qlik Sense a Azure Cosmos DB y visualización de los datos](visualize-qlik-sense.md)