---
title: 'Azure Data Factory: registro de cambios de la API de .NET'
description: Describe los cambios importantes, las adiciones de características y las correcciones de errores, entre otros, en una versión específica de la API de .NET para Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jonburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 18f77a9ed11d50083be4f0fe68cc0975b21d8997
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597595"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory: registro de cambios de la API de .NET
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. 

En este artículo se proporciona información sobre los cambios realizados en el SDK de Azure Data Factory en una versión específica. El paquete NuGet más reciente para Azure Data Factory se encuentra [aquí](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Versión 4.11.0
Incorporación de características:

* Se han agregado los siguientes tipos de servicios vinculados:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Se han agregado los siguientes tipos de conjuntos de datos:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Se han agregado los siguientes tipos de origen de copia:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>Versión 4.10.0
* Se han agregado las siguientes propiedades opcionales a TextFormat:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Se han agregado los siguientes tipos de servicios vinculados:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Se han agregado los siguientes tipos de conjuntos de datos:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Se han agregado los siguientes tipos de origen de copia:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Se ha agregado la propiedad [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) a AzureMLBatchExecutionActivity
  * Se ha habilitado el paso de varias entradas de servicio web a un experimento de Azure Machine Learning

## <a name="version-491"></a>Versión 4.9.1
### <a name="bug-fix"></a>Corrección de errores
* La autenticación basada en WebApi ya no se utiliza con [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice).

## <a name="version-490"></a>Versión 4.9.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado las propiedades [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) y [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) a CopyActivity. Para más información, consulte [Copias almacenadas provisionalmente](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Corrección de errores
* Se ha incorporado una sobrecarga del método [ActivityWindowOperationExtensions.List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions), que toma una instancia de [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters).
* [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) y [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) se han marcado como opcionales en CopySink.

## <a name="version-480"></a>Versión 4.8.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado las siguientes propiedades opcionales al tipo de actividad de copia para habilitar la optimización del rendimiento de copia:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Versión 4.7.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado los nuevos tipos StorageFormat y [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) para copiar archivos en el formato Optimized Row Columnar (ORC).
* Se han agregado las propiedades [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) y PolyBaseSettings a SqlDWSink.
  * Habilita el uso de PolyBase para copiar datos en Azure Synapse Analytics.

## <a name="version-461"></a>Versión 4.6.1
### <a name="bug-fixes"></a>Correcciones de errores
* Corrige la solicitud HTTP para enumerar las ventanas de la actividad.
  * Quita el nombre del grupo de recursos y el nombre de la factoría de datos de la carga de la solicitud.

## <a name="version-460"></a>Versión 4.6.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado las siguientes propiedades a [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties):
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Conjuntos de datos](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* Se han agregado las siguientes propiedades a [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo):
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Se han agregado los nuevos tipos [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) y [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) para definir conjuntos de datos cuyos datos están en formato JSON.

## <a name="version-450"></a>Versión 4.5.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado [operaciones de lista para la ventana de actividad](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions).
  * Se agregaron métodos para recuperar ventanas de la actividad con filtros basados en los tipos de entidad (es decir, fábricas de datos, conjuntos de datos, canalizaciones y actividades).
* Se han agregado los siguientes tipos de servicios vinculados:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice) y [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Se han agregado los siguientes tipos de conjuntos de datos:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset) y [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Se han agregado los siguientes tipos de origen de copia:     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>Versión 4.4.0
### <a name="feature-additions"></a>Incorporación de características
* El siguiente tipo de servicio vinculado se ha agregado como orígenes de datos y receptores para actividades de copia:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Para más información sobre conceptos y ejemplos, consulte [Servicio vinculado de SAS de Azure Storage](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Versión 4.3.0
### <a name="feature-additions"></a>Incorporación de características
* Los siguientes tipos de servicio vinculado se ha agregado como orígenes de datos para actividades de copia:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Para más información sobre conceptos y ejemplos, consulte [Movimiento de datos desde HDFS local mediante Azure Data Factory](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Para más información sobre conceptos y ejemplos, consulte [Movimiento de datos desde almacenes de datos ODBC mediante Azure Data Factory](data-factory-odbc-connector.md) .

## <a name="version-420"></a>Versión 4.2.0
### <a name="feature-additions"></a>Incorporación de características
* Se ha agregado el nuevo tipo de actividad siguiente: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Para más información acerca de la actividad, consulte [Actualización de modelos mediante la actividad de recursos de actualización](data-factory-azure-ml-batch-execution-activity.md).
* Se ha agregado una nueva propiedad opcional [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) a la [clase AzureMLLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice).
* Se han agregado las propiedades [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) y [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) a la clase [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient).
* Permita la configuración de los tiempos de espera para las llamadas de cliente al servicio de Factoría de datos.

## <a name="version-410"></a>Versión 4.1.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado los siguientes tipos de servicios vinculados:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Se han agregado los siguientes tipos de actividades:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Se han agregado los siguientes tipos de conjuntos de datos:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Se han agregado los siguientes tipos de origen y el receptor para la actividad de copia:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>Versión 4.0.1
### <a name="breaking-changes"></a>Últimos cambios
Las siguientes clases se han cambiado de nombre. Los nuevos nombres eran los nombres de clases originales antes de la versión 4.0.0.

| Nombre en 4.0.0 | Nombre en 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Versión 4.0.0
### <a name="breaking-changes"></a>Últimos cambios
* Las siguientes clases o interfaces se han cambiado de nombre.

| Nombre anterior | Nombre nuevo |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabla |[Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* Los métodos **List** devuelven ahora resultados paginados. Si la respuesta contiene una propiedad **NextLink** que no está vacía, la aplicación cliente debe seguir capturando la página siguiente hasta que se devuelvan todas las páginas.  Este es un ejemplo:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** devuelve solamente el resumen de una canalización, en lugar de todos los detalles. Por ejemplo, en un resumen de canalización, las actividades solo contienen el nombre y el tipo.

### <a name="feature-additions"></a>Incorporación de características
* La clase [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) admite dos nuevas propiedades, **SliceIdentifierColumnName** y **SqlWriterCleanupScript**, para permitir la copia idempotente en Azure Synapse Analytics. Consulte el artículo sobre [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) para obtener más información acerca de estas propiedades.
* Actualmente, se admite la ejecución de procedimientos almacenados en orígenes de Azure SQL Database y Azure Synapse Analytics como parte de la actividad de copia. Para ello, las clases [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) y [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) tienen las siguientes propiedades: **SqlReaderStoredProcedureName** y **StoredProcedureParameters**. Para más información sobre estas propiedades, consulte los artículos acerca de [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) y [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) en Azure.com.