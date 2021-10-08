---
title: Supervisión de la referencia de los datos de Azure Key Vault
description: Material de referencia importante necesario para la supervisión de Key Vault
author: msmbaldwin
ms.topic: reference
ms.author: mbaldwin
ms.service: key-vault
ms.custom: subject-monitoring
ms.date: 07/07/2021
ms.openlocfilehash: d3404dbd28c662734fe7319f83b69b4c45234332
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356189"
---
# <a name="monitoring-key-vault-data-reference"></a>Supervisión de la referencia de los datos de Key Vault

Consulte [Supervisión de Key Vault](monitor-key-vault.md) para obtener más información sobre la recopilación y el análisis de datos de supervisión de Key Vault.

## <a name="metrics"></a>Métricas


En esta sección se indican todas las métricas de la plataforma recopiladas automáticamente para Key Vault.  

|Tipo de métrica | Espacio de nombres de proveedor de recursos/tipo<br/> y vínculo a métricas individuales |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults) |
| HSM administrado | [Microsoft.KeyVault/managedhsms](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) 

### <a name="key-vault-metrics"></a>Métricas de Key Vault

Proveedor de recursos y tipo: [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults)

| Nombre | Métrica | Unidad | Tipo | Descripción |
|:-------|:-----|:------------|:------------------|
| Disponibilidad total del almacén | Disponibilidad      | Percent    | Average | Disponibilidad de solicitudes del almacén            | 
| Saturación general del almacén | SaturationShoebox | Percent | Average| Capacidad usada del almacén | 
| Número total de visitas de la API de servicio | ServiceApiHit | Count | Count | Número total de visitas de la API de servicio |
| Latencia general de la API de servicio | ServiceApiLatency | MilliSeconds | Average | Latencia general de las solicitudes de la API de servicio |
| Número total de resultados de la API de servicio | ServiceApiResult | Count | Count | Número total de resultados de la API de servicio |

Para obtener más información, vea una lista de [todas las métricas de la plataforma que se admiten en Azure Monitor](/azure/azure-monitor/essentials/metrics-supported).

## <a name="metric-dimensions"></a>Dimensiones de métricas

Para obtener más información sobre las dimensiones de métricas, consulte [Métricas multidimensionales](/azure/azure-monitor/essentials/data-platform-metrics#multi-dimensional-metrics).

Key Vault tiene las siguientes dimensiones asociadas a sus métricas:

- ActivityType
- ActivityName
- TransactionType
- StatusCode
- StatusCodeClass

## <a name="resource-logs"></a>Registros del recurso

En esta sección se enumeran los tipos de registros de recursos que se pueden recopilar para Key Vault.

Como referencia, consulte una lista de [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults).  Para obtener información completa, vea [Registro de Azure Key Vault](logging.md).

|Tipo de registro de recursos | Espacio de nombres de proveedor de recursos/tipo<br/> y vínculo a métricas individuales |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) |
| HSM administrado | [Microsoft.KeyVault/managedhsms](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults) 

## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor

En esta sección se hace referencia a todas las tablas de Kusto de registros de Azure Monitor relacionadas con Key Vault y que están disponibles para consultar mediante Log Analytics. 

|Tipo de recurso | Notas |
|-------|-----|
| [Key Vault](/azure/azure-monitor/reference/tables/tables-resourcetype#key-vaults) | |

Puede encontrar una referencia de todas las tablas de registros de Azure Monitor o de Log Analytics en [Referencia de la tabla de registros de Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).

### <a name="diagnostics-tables"></a>Tablas de diagnóstico

Key Vault usa la tabla [Actividad de Azure](/azure/azure-monitor/reference/tables/azureactivity), [Azure Diagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) y las tablas [Métricas de Azure](/azure/azure-monitor/reference/tables/azuremetrics) para almacenar la información del registro de recursos. Las columnas siguientes son importantes.

**Diagnóstico de Azure**

| Propiedad | Descripción |
|:--- |:---|
| _ResourceId | Identificador único del recurso al que está asociado el registro. |
| CallerIPAddress | La dirección IP del usuario que ha realizado la operación de notificación de UPN o de notificación de SPN basada en la disponibilidad. |
| DurationMs | Duración de la operación en milisegundos. |
| httpStatusCode_d | Código de estado HTTP devuelto por la solicitud (por ejemplo, *200*) |
| Nivel | Nivel del evento. Uno de los siguientes valores: Critical, Error, Warning, Informational y Verbose. |
| OperationName | Nombre de la operación, por ejemplo, "Alert" |
| properties_s |  |
| Region_s | |
| requestUri_s | El URI de la solicitud de cliente |
| Resource | |
| ResourceProvider | Proveedor de recursos del recurso de Azure que informa de la métrica. |
| ResultSignature | |
| TimeGenerated | Fecha y hora en que se creó el registro. |

## <a name="see-also"></a>Vea también

- Para ver una descripción de la supervisión de Azure Key Vault, consulte [Supervisión de Azure Key Vault](monitor-key-vault.md).
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](/azure/azure-monitor/essentials/monitor-azure-resource).
