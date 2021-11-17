---
title: Almacenamiento y análisis del historial de consultas en Azure Synapse Analytics
description: El análisis del historial de consultas es una de las necesidades fundamentales de los ingenieros de datos. Azure Synapse Analytics admite cuatro formas principales de analizar el historial y el rendimiento de las consultas. Estas son Almacén de consultas, DMV, Azure Log Analytics y Azure Data Explorer.
author: mariyaali
ms.author: mariyaali
ms.reviewer: wiassaf
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/28/2021
ms.custom: template-concept
ms.openlocfilehash: 866ca9439b5fc06f1e869cef6eef4c4adf979558
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063912"
---
# <a name="historical-query-storage-and-analysis-in-azure-synapse-analytics"></a>Almacenamiento y análisis del historial de consultas en Azure Synapse Analytics

El análisis del historial de consultas es una de las necesidades fundamentales de los ingenieros de datos. Azure Synapse Analytics admite cuatro formas principales de analizar el historial y el rendimiento de las consultas. Estas son Almacén de consultas, DMV, Azure Log Analytics y Azure Data Explorer. 

En este artículo se muestra cómo usar cada una de estas opciones para sus necesidades. Revise los casos de uso relacionados con el análisis del historial de consultas y el mejor método para cada uno.

| **Necesidad del cliente** |  **Almacén de consultas** |  **DMV**    | **Azure Log Analytics** | **Azure Data Explorer** |
|------------- | --- | ----- | ------------- |-------------------|
|**Solución lista para su uso** | Necesita habilitación | :heavy_check_mark: | Servicio adicional necesario |    Servicio adicional necesario|
|**Períodos de análisis más largos** | 30 días |    Hasta 10 000 filas de historial     | Personalizable | Personalizable|
|**Disponibilidad de métricas fundamentales** |    Limitado    | :heavy_check_mark: |    Limitado    | Personalizable|
|**Uso de SQL para el análisis** | :heavy_check_mark: | :heavy_check_mark:| KQL necesario | La compatibilidad con SQL es limitada|
|||||

## <a name="query-store"></a>Almacén de consultas

La característica Almacén de consultas ofrece datos detallados sobre el rendimiento y la elección del plan de consultas. Esta característica simplifica la solución de problemas de rendimiento al permitirle encontrar rápidamente las diferencias de rendimiento provocadas por cambios en los planes de consulta. 

Almacén de consultas no está habilitado de forma predeterminada para nuevas bases de datos de Azure Synapse Analytics. Para permitir que Almacén de consultas ejecute el siguiente comando T-SQL:

```sql
ALTER DATABASE <database_name>
SET QUERY_STORE = ON;
```

Por ejemplo:

```sql
ALTER DATABASE [SQLPOOL1]
SET QUERY_STORE = ON;
```

Para ejecutar tareas relacionadas con la auditoría y solución de problemas de rendimiento busque las últimas consultas ejecutadas, el número de ejecuciones, las consultas de más larga duración, las consultas con un número máximo de clientes potenciales físicos. Consulte [Supervisar el rendimiento mediante el almacén de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#performance) para ver consultas de ejemplo.

Ventajas:
* Umbral de 30 días para el almacenamiento de los datos de consulta.
* Los datos se pueden usar en la misma herramienta en la que se ejecuta la consulta.

Limitación conocida:
* Los escenarios de análisis están limitados en Almacén de consultas para Azure Synapse en comparación con el uso de DMV.

## <a name="dmvs"></a>DMV

Las vistas de administración dinámica (DMV) son muy útiles cuando se trata de recopilar información sobre los tiempos de espera de las consultas, los planes de ejecución, la memoria, etc. Se recomienda encarecidamente etiquetar la consulta de interés para realizar un seguimiento posterior. Por ejemplo:

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query');
```

Para más información sobre cómo etiquetar las consultas en Azure Synapse SQL, consulte [Uso de etiquetas de consulta en SQL de Synapse](develop-label.md).

Para más información sobre el uso de DMV para supervisar la carga de trabajo de Azure Synapse Analytics, consulte [Supervisión de la carga de trabajo del grupo de SQL dedicado mediante DMV](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context). Para obtener documentación sobre las vistas de catálogo específicas de Azure Synapse Analytics, consulte [Vistas de catálogo de Azure Synapse Analytics](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views).

Ventajas:
* Los datos se pueden usar en la misma herramienta de consulta.
* Las DMV proporcionan muchas opciones de análisis.

Limitaciones conocidas:
* Las DMV están limitadas a 10 000 filas de entradas históricas. 
* Las vistas se restablecen cuando se pausa o reanuda el grupo.

## <a name="log-analytics"></a>Log Analytics
Las áreas de trabajo de Log Analytics se pueden crear fácilmente en Azure Portal. Para más instrucciones sobre cómo conectar Synapse con Log Analytics, consulte [Supervisión de la carga de trabajo: Azure Portal](../sql-data-warehouse/sql-data-warehouse-monitor-workload-portal.md).

Al igual que Azure Data Explorer, Log Analytics usa el lenguaje de consulta Kusto (KQL). Para más información sobre la sintaxis de Kusto, consulte [Introducción a las consultas Kusto](/data-explorer/kusto/query/index.md). 

Junto con el período de retención configurable, puede elegir el área de trabajo a la que se va a dirigir específicamente para realizar consultas en Log Analytics. Log Analytics ofrece la flexibilidad necesaria para almacenar datos y ejecutar y guardar consultas.

Ventajas:
* Azure Log Analytics tiene una directiva de retención de registros personalizable

Limitaciones conocidas:
* El uso de KQL se agrega a la curva de aprendizaje.
* Las vistas limitadas se pueden registrar de forma predefinida.

## <a name="azure-data-explorer-adx"></a>Azure Data Explorer (ADX)

Azure Data Explorer (ADX) es un servicio de exploración de datos líder. Este servicio se puede usar para analizar consultas históricas de Azure Synapse Analytics. Para configurar una canalización de Azure Data Factory (ADF) para copiar y almacenar registros en ADX, consulte [Copia de datos a o desde Azure Data Explorer](/data-factory/connector-azure-data-explorer.md). En ADX, puede ejecutar consultas Kusto de rendimiento para analizar los registros. Puede combinar otras estrategias aquí, por ejemplo, para consultar y cargar la salida de DMV en ADX mediante ADF.
  
Ventajas:
* ADX proporciona una directiva de retención de registros personalizable.
* Ejecución de consultas con rendimiento en una gran cantidad de datos, especialmente las consultas que implican la búsqueda de cadenas.

Limitación conocida:
* El uso de KQL se agrega a la curva de aprendizaje.

## <a name="next-steps"></a>Pasos siguientes

 - [Azure Data Explorer](/azure/data-explorer/)
 - [Azure Data Factory](/azure/data-factory/)
 - [Log Analytics en Azure Monitor](/azure/azure-monitor/logs/log-analytics-overview)
 - [Arquitectura de SQL de Azure Synapse](overview-architecture.md)
 - [Introducción a Azure Synapse Analytics](../get-started.md)