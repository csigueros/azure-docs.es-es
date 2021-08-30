---
title: Supervisión y depuración con las conclusiones en Azure Cosmos DB
description: Use las métricas de Azure Cosmos DB para depurar problemas comunes y supervisar la base de datos.
ms.author: esarroyo
author: StefArroyo
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/14/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e85fa72288ab31fd8e61fcda731debb3517057f
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393387"
---
# <a name="monitor-and-debug-with-insights-in-azure-cosmos-db"></a>Supervisión y depuración con las conclusiones en Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB proporciona conclusiones sobre rendimiento, almacenamiento, coherencia, disponibilidad y latencia. En Azure Portal se proporciona una vista agregada de estas métricas. También puede ver las métricas de Azure Cosmos DB desde la API de Azure Monitor. Los valores de la dimensión para las métricas, como el nombre del contenedor, no distinguen entre mayúsculas y minúsculas. Por lo tanto, debe usar una comparación sin distinción entre mayúsculas y minúsculas al realizar comparaciones de cadenas en estos valores de dimensión. Para obtener información sobre cómo ver las métricas desde Azure Monitor, consulte el artículo [Obtener métricas desde Azure Monitor](./monitor-cosmos-db.md).

Este artículo lo guía a través de casos de uso comunes y cómo se pueden utilizar las conclusiones de Azure Cosmos DB para analizar y depurar estos problemas. De manera predeterminada, las conclusiones de las métricas se recopila cada cinco minutos y se mantiene durante siete días.

## <a name="view-insights-from-azure-portal"></a>Visualización de la información desde Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya hasta la cuenta de Azure Cosmos DB.

1. Puede ver las métricas de la cuenta en el panel **Métricas** o en el panel **Conclusiones (versión preliminar**.

   * **Métricas:** en este panel se proporcionan métricas numéricas que se recopilan a intervalos periódicos y que describen algún aspecto de un sistema en un momento determinado. Por ejemplo, puede consultar y supervisar la [métrica de latencia del lado servidor](monitor-server-side-latency.md), la [métrica de uso de unidades de solicitud normalizadas](monitor-normalized-request-units.md), entre otras.

   * **Conclusiones (versión preliminar):** en este panel se proporciona una experiencia de supervisión personalizada para Azure Cosmos DB. Utilizan las mismas métricas y registros que se recopilan en Azure Monitor y muestran una vista agregada correspondiente a la cuenta.

1. Abra el panel **Conclusiones (versión preliminar)** . De manera predeterminada, el panel Conclusiones muestra las métricas de rendimiento, solicitudes, almacenamiento, disponibilidad, latencia, sistema y administración de cuentas para cada contenedor de la cuenta. Puede seleccionar el **Intervalo de tiempo**, la **Base de datos** y el **Contenedor** cuyas conclusiones desea consultar. La pestaña **Información general** muestra el uso de RU/s, el uso de datos, el uso de índices, las solicitudes limitadas y el consumo normalizado de RU/s para la base de datos y el contenedor seleccionados.

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Métricas de rendimiento de Cosmos DB en Azure Portal" lightbox="./media/use-metrics/performance-metrics.png" :::

1. Las métricas siguientes están disponibles en el panel **Conclusiones**:

   * **Rendimiento**: esta pestaña muestra el número total de unidades de solicitud consumidas o con error (código de respuesta 429) debido a que se superó la capacidad de almacenamiento o rendimiento que se aprovisionó para el contenedor.

   * **Solicitudes**: esta pestaña muestra el número total de solicitudes procesadas por código de estado, por tipo de operación y el recuento de las solicitudes con error (código de respuesta 429). Se producirá un error en las solicitudes cuando se supere la capacidad de almacenamiento o rendimiento aprovisionada para el contenedor.

   * **Almacenamiento**: esta pestaña muestra el tamaño de los datos y el uso de índices durante el período seleccionado.

   * **Disponibilidad**: esta pestaña muestra el porcentaje de solicitudes que se completan correctamente respecto de las solicitudes totales por hora. La tasa de éxito se define mediante los SLA de Azure Cosmos DB.

   * **Latencia**: esta pestaña muestra la latencia de lectura y escritura que observa Azure Cosmos DB en la región donde funciona su cuenta. Puede visualizar la latencia entre regiones para una cuenta de replicación geográfica. También puede consultar la latencia de lado servidor por distintas operaciones. Esta métrica no representa la latencia de solicitud de un extremo a otro.

   * **Sistema**: esta pestaña muestra cuántas solicitudes de metadatos procesa la partición principal. También ayuda a identificar las solicitudes limitadas.

   * **Administración de cuentas**: esta pestaña muestra las métricas correspondientes a actividades de administración de cuentas como la creación de una cuenta, su eliminación, actualizaciones de claves, configuración de replicación y red.

En las siguientes secciones se explican escenarios comunes donde puede usar las métricas de Azure Cosmos DB.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Descripción de cuántas solicitudes se realizan correctamente o causan errores

Para empezar, vaya a [Azure Portal](https://portal.azure.com) y navegue a la hoja **Conclusiones**. En esta hoja, abra la pestaña **Solicitudes**. Verá un gráfico con el total de solicitudes segmentado por código de estado y tipo de operación. Para más información sobre los códigos de estado HTTP, consulte [HTTP Status Codes for Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) (Códigos de estado HTTP para Azure Cosmos DB).

El código de estado de error más común es 429 (limitación de tasa/limitación). Este error significa que las solicitudes a Azure Cosmos DB superan el rendimiento aprovisionado. La solución más común para este problema consiste en [escalar verticalmente las RU](./set-throughput.md) para la colección dada.

:::image type="content" source="media/use-metrics/request-count.png" alt-text="Número de solicitudes por minuto" lightbox= "media/use-metrics/request-count.png":::

## <a name="determine-the-throughput-consumption-by-a-partition-key-range"></a>Determinación del consumo de rendimiento por un intervalo de claves de partición

Tener una buena cardinalidad de las claves de partición es esencial para cualquier aplicación escalable. A fin de determinar la distribución de rendimiento de cualquier contenedor particionado dividido en identificadores de intervalos de claves de partición, vaya al panel **Conclusiones (versión preliminar)** . Abra la pestaña **Rendimiento**. En el gráfico se muestra el consumo normalizado de RU/s en intervalos de claves de partición distintos.

:::image type="content" source="media/use-metrics/throughput-consumption-partition-key-range.png" alt-text="Consumo de rendimiento normalizado por identificadores de intervalos de claves de partición" lightbox="media/use-metrics/throughput-consumption-partition-key-range.png":::

Este gráfico le permite identificar si hay una partición activa. Una distribución de rendimiento desigual puede provocar particiones *activas*, lo que pueden dar lugar a solicitudes limitadas y la necesidad de volver a crear particiones. Después de identificar qué clave de partición está causando el sesgo en la distribución, puede que tenga que volver a crear particiones en el contenedor con una clave de partición más distribuida. Para más información sobre la creación de particiones en Azure Cosmos DB, consulte [Partición y escalado en Azure Cosmos DB](./partitioning-overview.md).

## <a name="determine-the-data-and-index-usage"></a>Determinación del uso de índices y los datos

Es importante determinar la distribución de almacenamiento de cualquier contenedor con particiones por uso de datos, uso de índices y uso de documentos. Puede minimizar el uso de índices, maximizar el uso de datos y optimizar las consultas. Para obtener esta información, vaya al panel **Conclusiones (versión preliminar)** y abra la pestaña **Almacenamiento**:

:::image type="content" source="media/use-metrics/data-index-consumption.png" alt-text="Consumo de datos, índices y documentos" lightbox="media/use-metrics/data-index-consumption.png" :::

## <a name="compare-data-size-against-index-size"></a>Comparación del tamaño de los datos con el tamaño de índice

En Azure Cosmos DB, el almacenamiento total consumido es la combinación del tamaño de los datos y del tamaño del índice. Normalmente, el tamaño del índice es una fracción del tamaño de los datos. Para obtener más información, consulte el artículo [Tamaño de índice](index-policy.md#index-size). En la hoja Métricas de [Azure Portal](https://portal.azure.com), la pestaña Almacenamiento muestra el desglose de consumo de almacenamiento en función de los datos y el índice.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Si desea conservar el espacio de índice, puede ajustar la [directiva de indexación](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Depuración de por qué mis consultas se ejecutan lentamente

En los SDK de la API de SQL, Azure Cosmos DB proporciona estadísticas de ejecución de consultas.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* proporciona información detallada sobre cuánto tardó cada componente de la consulta en la ejecución. La causa principal más común para consultas de larga ejecución son los análisis, lo que significa que la consulta no pudo aprovechar los índices. Este problema puede resolverse con una mejor condición de filtro.

## <a name="next-steps"></a>Pasos siguientes

Ha aprendido a supervisar y depurar problemas con las métricas proporcionadas en Azure Portal. Si quiere obtener más información acerca de cómo mejorar el rendimiento de bases de datos, lea los artículos siguientes:

* Para obtener información sobre cómo ver las métricas desde Azure Monitor, consulte el artículo [Obtener métricas desde Azure Monitor](./monitor-cosmos-db.md). 
* [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md)
* [Sugerencias de rendimiento para Azure Cosmos DB](performance-tips.md)