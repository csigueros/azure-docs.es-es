---
title: Archivo de inclusión
description: archivo de inclusión
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f691f760c9abfed773db8602f878dfc7ba0c08ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520758"
---
### <a name="general-query-limits"></a>Límites generales de las consultas

| Límite | Descripción |
|:---|:---|
| Lenguaje de consulta | Azure Monitor usa el mismo [lenguaje de consulta Kusto](/azure/kusto/query/) que Azure Data Explorer. Consulte las [diferencias del lenguaje de consulta de registros de Azure Monitor](/azure/data-explorer/kusto/query/) para los elementos del lenguaje KQL que no se admiten en Azure Monitor. |
| Regiones de Azure | Las consultas de registro pueden experimentar una sobrecarga excesiva cuando los datos abarcan áreas de trabajo Log Analytics de varias regiones de Azure. Para más información, consulte [Límites de consulta](../articles/azure-monitor/logs/scope.md#query-scope-limits). |
| Consultas entre recursos | El número máximo de recursos de Application Insights y de áreas de trabajo de Log Analytics en una sola consulta se limita a 100.<br>No se admite la consulta entre recursos en el Diseñador de vistas.<br>La consulta entre recursos en las alertas de registro se admite en la nueva API scheduledQueryRules.<br>Consulte [Límites de la consulta entre recursos](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits) para obtener más información. |

### <a name="user-query-throttling"></a>Limitación de las consultas de usuario
Azure Monitor tiene varios límites para protegerse frente a los usuarios que envían un número excesivo de consultas. Este comportamiento puede sobrecargar potencialmente los recursos de back-end del sistema y poner en peligro la capacidad de respuesta del servicio. Los límites siguientes están diseñados para proteger a los clientes frente a interrupciones y garantizar un nivel de servicio coherente. Los límites y la limitación de usuarios están diseñados para afectar solo a un escenario de uso extremo y no deben ser de importancia para un uso normal.


| Measure | Límite por usuario | Descripción |
|:---|:---|:---|
| Consultas simultáneas | 5 | Un usuario puede ejecutar hasta 5 consultas simultáneas; cualquier consulta adicional se agregará a una cola. Cuando finaliza una de las consultas en ejecución, se extrae la primera consulta de la cola y comienza a ejecutarse. Nota: Las consultas de alertas no forman parte de este límite.
| Tiempo en la cola de simultaneidad | 3 minutos | Si una consulta se encuentra en la cola durante más de 3 minutos sin iniciarse, se terminará con una respuesta de error HTTP con el código 429. |
| Total de consultas en la cola de simultaneidad | 200 | Una vez que el número de consultas de la cola llega a 200, la siguiente consulta se rechazará con un código de error HTTP 429. Este número es adicional a las cinco consultas que se pueden ejecutar simultáneamente. |
| Velocidad de consulta | 200 consultas cada 30 segundos | La velocidad global a la que un solo usuario puede enviar consultas a todas las áreas de trabajo. Este límite se aplica a consultas mediante programación o a consultas iniciadas por elementos de visualización, como los paneles de Azure y la página de resumen del área de trabajo de Log Analytics. |

- Optimice las consultas como se describe en [Optimización de las consultas de registro en Azure Monitor](../articles/azure-monitor/logs/query-optimization.md).
- Los paneles y los libros pueden contener varias consultas en una sola vista que generan una ráfaga de consultas cada vez que se cargan o actualizan. Considere la posibilidad de dividirlos en varias vistas que se cargan a petición. 
- En Power BI, considere la posibilidad de extraer solo los resultados agregados en lugar de los registros sin procesar.
