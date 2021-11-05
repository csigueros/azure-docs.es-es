---
title: Diagnósticos de servidor para Cassandra API de Azure Cosmos DB
description: En este artículo se explican algunos códigos de error comunes en los Cassandra API de Azure Cosmos DB y cómo solucionar problemas con Log Analytics
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: troubleshooting
ms.date: 10/12/2021
ms.custom: template-how-to
ms.openlocfilehash: bbef336e16623113c45929710e49101bddfdf64e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093511"
---
# <a name="server-diagnostics-for-azure-cosmos-db-cassandra-api"></a>Diagnósticos de servidor para Cassandra API de Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Log Analytics es una herramienta de Azure Portal que le ayuda a ejecutar diagnósticos de servidor en la cuenta de Cassandra API. Ejecute consultas de registro a partir de los datos recopilados por los registros de Azure Monitor y analice sus resultados de manera interactiva. Los registros recuperados de las consultas de Log Analytics ayudan a proporcionar información sobre los datos.

## <a name="prerequisites"></a>Prerrequisitos

- Creación de un [área de trabajo de Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).
- Creación de [configuración de diagnóstico](../cosmosdb-monitor-resource-logs.md).
- Inicie [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) en la cuenta de Cassandra API.

## <a name="use-log-analytics"></a>Uso de Log Analytics
Después de completar la configuración de Log Analytics, puede empezar a explorar los registros para obtener más información.

### <a name="explore-data-plane-operations"></a>Explorar las operaciones del plano de datos
Use la tabla CDBCassandraRequests para ver las operaciones del plano de datos específicamente para la cuenta de Cassandra API. Consulta de muestra para ver la solicitud que consume topN(10) y obtener información detallada sobre cada solicitud realizada.

```Kusto
CDBCassandraRequests
| where RequestCharge  > 0
| project DatabaseName, CollectionName, DurationMs, OperationName, ActivityId, ErrorCode, RequestCharge, PIICommandText 
| order by RequestCharge
| take 10
```

#### <a name="error-codes-and-possible-solutions"></a>Códigos de error y posibles soluciones
|Código de estado | Código de error           | Descripción  |
|------------|----------------------|--------------|
| 200 | -1 | Correcto |
| 400 | 8704 | La consulta es correcta, pero con una sintaxis no válida. |
| 400 | 8192 | La consulta enviada tiene un error de sintaxis. Revise la consulta. |
| 400 | 8960 | La consulta no es válida debido a alguna incidencia de configuración. |
| 401 |8448 | El usuario registrado no tiene los permisos adecuados para realizar la consulta. |
| 403 | 8448 | Respuesta prohibida, ya que es posible que el usuario no tenga los permisos necesarios para llevar a cabo la solicitud. |
| 404 | 5376 | Excepción sin tiempo de espera durante una solicitud de escritura como resultado de la respuesta no encontrada. |
| 405 | 0 | Error del lado del servidor de Cassandra. El error rara vez se produce, abra una incidencia de soporte técnico. |
| 408 | 4608 | Tiempo de espera durante una solicitud de lectura. |
| 408 | 4352 | Excepción de tiempo de espera durante una solicitud serviceRequest de escritura. |
| 409 | 9216 | Intentar crear un espacio de claves o una tabla que ya existen. |
| 412 | 5376 | Error en la condición previa. Para garantizar la integridad de los datos, nos aseguramos de que la solicitud de escritura basada en la respuesta de lectura sea verdadera. Se devuelve una excepción de solicitud de escritura sin tiempo de espera. |
| 413 | 5376 | Esta excepción sin tiempo de espera durante una solicitud de escritura se debe a una carga quizás demasiado grande. Actualmente, hay un límite de 2 MB por fila. |
| 417 | 9472 | La excepción se produce cuando una instrucción preparada no se almacena en caché en el nodo de servidor. Debe ser transitorio o sin bloqueo. |
| 423 | 5376 | Hay un bloqueo porque actualmente se procesa una solicitud de escritura. |
| 429 | 4097| La excepción de sobrecarga es el resultado de la escasez de RU o la alta tasa de solicitudes. Probablemente necesite más RU para controlar la solicitud de mayor volumen. En Cassandra nativo esto puede interpretarse como que una de las máquinas virtuales no tienen suficiente CPU. Se recomienda revisar el modelo de datos actual para asegurarse de que no tiene un sesgo excesivo que pueda estar causando particiones activas. |
| 449 | 5376 | Excepción de ejecución simultánea. Esto se produce para garantizar que solo se actualice una escritura cada vez para una fila determinada. |
| 500 | 0 | Server cassandraError: ha ocurrido algo inesperado. Esto indica un error del lado servidor. |
| 503 | 4096 | Servicio no disponible. |
|   | 256 | Esto puede deberse a credenciales de conexión no válidas. Compruebe las credenciales de conexión. |
|   | 10 | Infracción de protocolo desencadenada por un mensaje de cliente. Un ejemplo es el mensaje de consulta enviado antes de que se haya enviado uno de inicio. |

### <a name="troubleshoot-query-consumption"></a>Solución de problemas de consumo de consultas
La tabla CDBPartitionKeyRUConsumption contiene detalles sobre el consumo de unidades de solicitud (RU) para las claves lógicas de cada región dentro de cada una de sus particiones físicas.

```Kusto
CDBPartitionKeyRUConsumption 
| summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
| render columnchart
 ```

### <a name="explore-control-plane-operations"></a>Explorar operaciones del plano de control
La tabla CLONControlPlaneRequests contiene detalles sobre las operaciones del plano de control, específicamente para cuentas de Cassandra API. 

```Kusto
CDBControlPlaneRequests
| where TimeGenerated > now(-6h)
| where  ApiKind == "Cassandra"
| where OperationName in ("Create", "Upsert", "Delete", "Execute")
| summarize by OperationName
 ```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).
- Más información sobre cómo [migrar de la instancia nativa de Apache Cassandra a Cassandra API de Azure Cosmos DB](migrate-data-databricks.md).