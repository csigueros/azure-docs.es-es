---
title: Servicios y esquemas admitidos de los registros de recursos de Azure
description: Conozca los esquemas de los eventos y servicios admitidos para los registros de recurso de Azure.
ms.topic: reference
ms.date: 05/10/2021
ms.openlocfilehash: 99746b8f392d8afc5df9aa14ac7e1c7f19069151
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129454988"
---
# <a name="common-and-service-specific-schemas-for-azure-resource-logs"></a>Esquemas específicos de servicio y común para los registros de recursos de Azure

> [!NOTE]
> Los registros de recurso se conocían anteriormente como registros de diagnóstico. El nombre se cambió en octubre de 2019, dado que los tipos de registros recopilados por Azure Monitor evolucionaron para incluir algo más que tan solo el recurso de Azure. 
>
> Este artículo se usa para enumerar las categorías de registro de recursos que puede recopilar. Dicha lista se encuentra ahora en [Categorías del registro de recursos](resource-logs-categories.md). 

Los [registros de recursos de Azure Monitor](../essentials/platform-logs-overview.md) son los registros emitidos por los servicios de Azure que describen el funcionamiento de dichos servicios o recursos. Todos los registros de recursos disponibles en Azure Monitor comparten un esquema de nivel superior común. Cada servicio tiene la flexibilidad de emitir propiedades únicas para sus propios eventos.

Una combinación del tipo de recurso (disponible en la propiedad `resourceId`) y la categoría identifican un esquema de forma única. En este artículo se describen los esquemas de nivel superior para los registros de recursos y los vínculos a los esquemas para cada servicio.


## <a name="top-level-common-schema"></a>Esquema común de nivel superior

| Nombre | Obligatorio u opcional | Descripción |
|---|---|---|
| `time` | Obligatorio | Marca de tiempo (UTC) del evento. |
| `resourceId` | Obligatorio | Identificador del recurso que ha emitido el evento. Para los servicios de inquilino, presenta el formato */tenants/tenant-id/providers/provider-name*. |
| `tenantId` | Necesario para los registros de inquilinos | El identificador del inquilino de Active Directory al que está asociado este evento. Esta propiedad solo se usa para los registros de nivel de inquilino. No aparece en los registros de nivel de recurso. |
| `operationName` | Requerido | Nombre de la operación que representa este evento. Si el evento representa una operación de control de acceso basado en rol de Azure (RBAC), este es el nombre de la operación de Azure RBAC (por ejemplo, `Microsoft.Storage/storageAccounts/blobServices/blobs/Read`). Este nombre se suele modelar con la forma de una operación de Azure Resource Manager, incluso si no se trata de una operación documentada de Resource Manager: (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| `operationVersion` | Opcionales | La versión de API asociada con la operación, si `operationName` se ha realizado mediante una API (por ejemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Si ninguna API se corresponde con esta opración, la versión representa la versión de esa operación en caso de que las propiedades asociadas con la operación cambien en el futuro. |
| `category` | Obligatorio | Categoría de registro del evento. La categoría es la granularidad con la que se pueden habilitar o deshabilitar los registros en un recurso determinado. Las propiedades que aparecen en el blob de propiedades de un evento son las mismas dentro de una categoría de registro y un tipo de recurso concretos. Las categorías de registros típicas son `Audit`, `Operational`, `Execution` y `Request`. |
| `resultType` | Opcional | Estado del evento. Entre los valores típicos se incluyen `Started`, `In Progress`, `Succeeded`, `Failed`, `Active` y `Resolved`. |
| `resultSignature` | Opcionales | Subestado del evento. Si esta operación se corresponde con una llamada API de REST, este campo es el código de estado HTTP de la llamada REST correspondiente. |
| `resultDescription `| Opcionales | Descripción de texto estático de esta operación; por ejemplo, `Get storage file`. |
| `durationMs` | Opcional | Duración de la operación en milisegundos. |
| `callerIpAddress` | Opcional | Dirección IP del autor de la llamada, si la operación se corresponde con una llamada API que podría proceder de una entidad con una dirección IP disponible públicamente. |
| `correlationId` | Opcionales | GUID que se usa para agrupar un conjunto de eventos relacionados. Normalmente, si dos eventos tienen el mismo valor `operationName` pero dos estados diferentes (por ejemplo, `Started` y `Succeeded`), comparten el mismo valor `correlationID`. Esto también puede representar otras relaciones entre los eventos. |
| `identity` | Opcional | Blob JSON que describe la identidad del usuario o la aplicación que realizó la operación. Normalmente esto incluye la autorización y las notificaciones o el token JWT de Active Directory. |
| `Level` | Opcional | Nivel de gravedad del evento. Debe ser uno de los siguientes: `Informational`, `Warning`, `Error` o `Critical`. |
| `location` | Opcionales | Región del recurso que emite el evento; por ejemplo, `East US` o `France South`. |
| `properties` | Opcionales | Todas las propiedades extendidas relacionadas con esta categoría de eventos. Todas las propiedades personalizadas o únicas se deben colocar dentro de esta "Parte B" del esquema. |

## <a name="service-specific-schemas"></a>Esquemas específicos por servicio

El esquema para los registros de recursos varía según la categoría de registro y el recurso. En la lista siguiente se muestran los servicios de Azure que hacen que los registros de recursos y los vínculos estén disponibles para el servicio y los esquemas específicos de la categoría (cuando sea posible). La lista cambia a medida que se agregan nuevos servicios. Si no ve lo que necesita, no dude en abrir una incidencia de GitHub en este artículo para que podamos actualizarlo.

| Servicio o característica | Esquema y documentación |
| --- | --- |
| Azure Active Directory | [Información general](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Esquema de registros de auditoría](../../active-directory/reports-monitoring/overview-reports.md) y [Esquema de inicios de sesión](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Azure Analysis Services | [Azure Analysis Services: Configuración del registro de diagnóstico](../../analysis-services/analysis-services-logging.md) |
| Azure API Management | [Registros de recursos de API Management](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Azure App Service | [Registros de App Service](../../app-service/troubleshoot-diagnostic-logs.md)
| Azure Application Gateway |[Registro para Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics para Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registro de Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Cognitive Services | [Registro para Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Azure Container Instances | [Registro para Azure Container Instances](../../container-instances/container-instances-log-analytics.md#log-schema) |
| Azure Container Registry | [Registro para Azure Container Registry](../../container-registry/monitor-service.md) |
| Azure Content Delivery Network | [Registros de diagnóstico para Content Delivery Network](../../cdn/cdn-azure-diagnostic-logs.md) |
| Azure Cosmos DB | [Registro de Azure Cosmos DB](../../cosmos-db/monitor-cosmos-db.md) |
| Explorador de datos de Azure | [Registros de Azure Data Explorer](/azure/data-explorer/using-diagnostic-logs) |
| Azure Data Factory | [Supervisión de Data Factory mediante Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Análisis con Azure Data Lake |[Acceso a los registros para Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Azure Data Lake Storage |[Acceso a los registros para Azure Data Lake Storage](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Database for MySQL | [Registros de diagnóstico de Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Registros de Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Registro de diagnósticos en Azure Databricks](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure DDoS Protection | [Registro en el estándar de Azure DDoS Protection](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Azure Digital Twins | [Configuración de diagnósticos de Azure Digital Twins](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Azure Event Hubs |[Registros de Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Azure ExpressRoute | Esquema no disponible |
| Azure Firewall | [Registro de Azure Firewall](../../firewall/logs-and-metrics.md#diagnostic-logs) |
| Azure Front Door | [Registros para Azure Front Door](../../frontdoor/front-door-diagnostics.md) |
| Azure IoT Hub | [Operaciones de IoT Hub](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Azure Key Vault |[Registro de Azure Key Vault](../../key-vault/general/logging.md) |
| Azure Kubernetes Service |[Registros de Azure Kubernetes Service](../../aks/monitor-aks-reference.md#resource-logs) |
| Azure Load Balancer |[Log Analytics para Azure Load Balancer](../../load-balancer/monitor-load-balancer.md) |
| Azure Logic Apps |[Esquema de seguimiento personalizado de Logic Apps B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure Machine Learning | [Registro de diagnóstico en Azure Machine Learning](../../machine-learning/monitor-resource-reference.md) |
| Azure Media Services | [Esquemas de supervisión de Media Services](../../media-services/latest/monitoring/monitor-media-services-data-reference.md#schemas) |
| Grupos de seguridad de red |[Log Analytics para grupos de seguridad de red (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Azure Power BI Embedded | [Registro para Power BI Embedded en Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Modelo de datos para Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Búsqueda de análisis de tráfico |[Habilitación y uso de análisis de tráfico de búsqueda](../../search/search-traffic-analytics.md) |
| Azure Service Bus |[Registros de Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Azure SQL Database | [Registro de Azure SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Almacenamiento de Azure | [Blobs](../../storage/blobs/monitor-blob-storage-reference.md#resource-logs-preview), [instancias de Files](../../storage/files/storage-files-monitoring-reference.md#resource-logs-preview), [Colas](../../storage/queues/monitor-queue-storage-reference.md#resource-logs-preview), [Tablas](../../storage/tables/monitor-table-storage-reference.md#resource-logs-preview) |
| Azure Stream Analytics |[Registros de trabajo](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Administrador de tráfico de Azure | [Esquema de registro de Traffic Manager](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Azure Virtual Network | Esquema no disponible |
| Puertas de enlace de red virtual | Esquema no disponible |



## <a name="next-steps"></a>Pasos siguientes

* [Visualización de las categorías de registro de recursos que se pueden recopilar](resource-logs-categories.md)
* [Más información sobre los registros de recurso](../essentials/platform-logs-overview.md)
* [Transmisión de registros de recursos a Event Hubs](./resource-logs.md#send-to-azure-event-hubs)
* [Cambio de la configuración de diagnóstico del registro de recursos con la API de REST de Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Análisis de registros desde Azure Storage con Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)