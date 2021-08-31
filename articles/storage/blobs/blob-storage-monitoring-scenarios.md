---
title: Procedimientos recomendados para supervisar Azure Blob Storage
description: Conozca las directrices de procedimientos recomendados y cómo usar métricas y registros para supervisar la instancia de Azure Blob Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 07/30/2021
ms.custom: monitoring
ms.openlocfilehash: 1d09538b9ada9a355ed956640d13a10ad544b348
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122180408"
---
# <a name="best-practices-for-monitoring-azure-blob-storage"></a>Procedimientos recomendados para supervisar Azure Blob Storage

En este artículo se ofrece una colección de escenarios comunes de supervisión de almacenamiento y se proporcionan directrices de procedimientos recomendados para realizarlos.  

## <a name="identify-storage-accounts-with-no-or-low-use"></a>Identificación de cuentas de almacenamiento sin uso o con uso bajo

Storage Insights es un panel sobre las métricas Azure Storage y los registros. Puede usar Storage Insights para examinar el volumen de transacciones y la capacidad usada de todas las cuentas. Esa información puede ayudarle a decidir qué cuentas desea retirar. Para configurar Storage Insights, consulte [Supervisión del servicio de almacenamiento con Azure Monitor Storage Insights](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json). 

### <a name="analyze-transaction-volume"></a>Análisis del volumen de transacciones

Desde la [vista Storage Insights en Azure monitor](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json#view-from-azure-monitor), ordene sus cuentas en orden ascendente utilizando la columna **Transacciones**. En la imagen siguiente se muestra una cuenta con un volumen de transacción bajo durante el período especificado. 

> [!div class="mx-imgBorder"]
> ![Volumen de transacciones en Storage Insights](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-volume.png)

Haga clic en el vínculo de la cuenta para obtener más información sobre estas transacciones. En este ejemplo, la mayoría de las solicitudes se realizan al servicio de Storage Blob. 

> [!div class="mx-imgBorder"]
> ![Transacción por tipo de servicio](./media/blob-storage-monitoring-scenarios/storage-insights-transactions-by-storage-type.png)

Para determinar qué tipo de solicitudes se están realizando, profundice en el gráfico **Transacciones por nombre de API**. 

> [!div class="mx-imgBorder"]
> ![API de transacciones de Storage](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-apis.png)

En este ejemplo, todas las solicitudes enumeran operaciones o solicitudes de información de propiedades de cuenta. No hay transacciones de lectura y escritura. Esto podría llevar a pensar que la cuenta no se está utilizando de forma significativa. 

### <a name="analyze-used-capacity"></a>Análisis de la capacidad usada

Desde la pestaña **Capacidad** de la vista  [Storage Insights en Azure Monitor](../common/storage-insights-overview.md#view-from-azure-monitor), ordene sus cuentas en orden ascendente utilizando la columna **Capacidad utilizada por la cuenta**. En la imagen siguiente se muestra una cuenta con un volumen de capacidad inferior al de otras cuentas. 

> [!div class="mx-imgBorder"]
> ![Capacidad de almacenamiento usada](./media/blob-storage-monitoring-scenarios/storage-insights-capacity-used.png)

Para examinar los blobs asociados a esta capacidad usada, puede usar Explorador de Storage. Para un gran número de blobs, considere la posibilidad de generar un informe mediante una [directiva de inventario de Blob](blob-inventory.md). 

## <a name="monitor-the-use-of-a-container"></a>Supervisión del uso de un contenedor

Si particiona los datos del cliente por contenedor, puede supervisar la cantidad de capacidad que usa cada cliente. Puede usar el inventario de Azure Storage Blob para realizar un inventario de blobs con información de tamaño. A continuación, puede agregar el tamaño y el recuento en el nivel de contenedor. Por ejemplo, consulte [Cálculo del recuento de blobs y su tamaño total por contenedor mediante el inventario de Azure Storage](calculate-blob-count-size.md).

También puede evaluar el tráfico en el nivel de contenedor consultando los registros. Para obtener más información sobre la escritura de consultas en Log Analytics, consulte [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md). Para obtener más información sobre el esquema de registros de almacenamiento, consulte la [referencia de supervisión de datos en Azure Blob Storage ](monitor-blob-storage-reference.md#resource-logs-preview).

Esta es una consulta para obtener el número de transacciones de lectura y el número de bytes leídos en cada contenedor.


```kusto
StorageBlobLogs
| where OperationName  == "GetBlob"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize ReadSize = sum(ResponseBodySize), ReadCount = count() by tostring(ContainerName)
```

La consulta siguiente usa una consulta similar para obtener información sobre las operaciones de escritura. 

```kusto
StorageBlobLogs
| where OperationName == "PutBlob" or
  OperationName == "PutBlock" or
  OperationName == "PutBlockList" or
  OperationName == "AppendBlock" or 
  OperationName == "SnapshotBlob" or
  OperationName == "CopyBlob" or 
  OperationName == "SetBlobTier" 
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize WriteSize = sum(RequestBodySize), WriteCount = count() by tostring(ContainerName)
```

La consulta anterior hace referencia a los nombres de varias operaciones porque más de un tipo de operación puede contar como una operación de escritura. Para obtener más información sobre qué operaciones se consideran de lectura y escritura, consulte los [precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) o [Azure Data Lake Storage](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="audit-account-activity"></a>Auditoría de la actividad de las cuentas

En muchos casos, deberá auditar las actividades de las cuentas de almacenamiento para garantizar la seguridad y el cumplimiento. Las operaciones en las cuentas de almacenamiento se dividen en dos categorías: *Plano de control* y *Plano de datos*. 

Una operación de plano de control es una solicitud de Azure Resource Manager para crear una cuenta de almacenamiento o actualizar una propiedad de una cuenta de almacenamiento existente. Para obtener más información, vea [Azure Resource Manager](../../azure-resource-manager/management/overview.md). 

Una operación del plano de datos es una operación en los datos de una cuenta de almacenamiento que es el resultado de una solicitud al punto de conexión del servicio de almacenamiento. Por ejemplo, se ejecuta una operación de plano de datos cuando se carga un blob en una cuenta de almacenamiento o se descarga un blob desde una cuenta de almacenamiento. Para obtener más información, consulte la [API de Azure Storage](/rest/api/storageservices/). 

En la sección se muestra cómo identificar la información "cuándo", "quién", "qué" y "cómo" de las operaciones de plano de datos y de control.

### <a name="auditing-control-plane-operations"></a>Auditoría de las operaciones del plano de control

Las operaciones de Resource Manager se capturan en el [registro de actividad de Azure](../../azure-monitor/essentials/activity-log.md). Para ver el registro de actividad, abra la cuenta de almacenamiento en Azure Portal y, a continuación, seleccione **Registro de actividad**.

> [!div class="mx-imgBorder"]
> ![Registro de actividad](./media/blob-storage-monitoring-scenarios/activity-log.png):


Abra cualquier entrada de registro para ver el archivo JSON que describa la actividad. El siguiente JSON muestra la información "cuándo", "qué" y "cómo" de una operación de plano de control:

> [!div class="mx-imgBorder"]
> ![JSON de registro de actividad](./media/blob-storage-monitoring-scenarios/activity-log-json.png)

La disponibilidad de la información "quién" depende del método de autenticación que se usó para realizar la operación del plano de control. Si una entidad de seguridad de Azure AD realizó la autorización, el identificador de objeto de esa entidad de seguridad también aparecería en esta salida JSON (por ejemplo: `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`). Dado que es posible que no vea siempre otra información relacionada con la identidad, como una dirección de correo electrónico o un nombre, el identificador de objeto siempre es la mejor manera de identificar de forma única la entidad de seguridad. 

Puede encontrar el nombre descriptivo de esa entidad de seguridad tomando el valor del identificador de objeto y buscando la entidad de seguridad en la página Azure AD de Azure Portal. En la captura de pantalla siguiente se muestra un resultado de búsqueda en Azure AD.

> [!div class="mx-imgBorder"]
> ![Búsqueda en Azure Active Directory](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>Auditoría de operaciones del plano de datos

Las operaciones del plano de datos se capturan en los [registros de recursos de Azure Storage](monitor-blob-storage.md#analyzing-logs). Puede [configurar la opción Diagnóstico](monitor-blob-storage.md#send-logs-to-azure-log-analytics) para exportar los registros al área de trabajo de Log Analytics para una experiencia de consulta nativa. 

Esta es una consulta de Log Analytics que recupera la información "cuándo", "quién", "qué" y "cómo" en una lista de entradas de registro. 

```kusto
StorageBlobLogs 
| where TimeGenerated > ago(3d) 
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

Para la parte "cuándo" de la auditoría, el campo `TimeGenerated` muestra cuándo se registró la entrada del registro. 

Para la parte "qué" de la auditoría, el campo `Uri` muestra el elemento que fue modificado o leído. 

Para la parte "cómo" de la auditoría, el campo `OperationName` muestra qué operación se ejecutó. 

Para la parte "quién" de la auditoría, `AuthenticationType` muestra qué tipo de autenticación se usó para realizar una solicitud. Este campo puede mostrar cualquiera de los tipos de autenticación que admite Azure Storage, incluido el uso de una clave de cuenta, un token de SAS o Azure Active Directory (Azure AD). 

Si una solicitud se autenticó mediante Azure AD, el campo `RequesterObjectId` proporciona la manera más confiable de identificar la entidad de seguridad. Puede encontrar el nombre descriptivo de esa entidad de seguridad tomando el valor el valor del id. del campo `RequesterObjectId` y buscando la entidad de seguridad en la página Azure AD de Azure Portal. En la captura de pantalla siguiente se muestra un resultado de búsqueda en Azure AD.

> [!div class="mx-imgBorder"]
> ![Búsqueda en Azure Active Directory](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

En algunos casos, un nombre de usuario principal o *UPN* puede aparecer en los registros. Por ejemplo, si la entidad de seguridad es un usuario de Azure AD, es probable que aparezca el UPN. Para otros tipos de entidades de seguridad, como identidades administradas asignadas por el usuario, o en determinados escenarios, como la autenticación entre inquilinos Azure AD, el UPN no aparecerá en los registros. 

Esta consulta muestra todas las operaciones de lectura realizadas por entidades de seguridad de OAuth.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "GetBlob"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

La clave compartida y la autenticación sas no proporcionan ningún medio para auditar identidades individuales. Por lo tanto, si desea mejorar la capacidad de realizar auditorías en función de la identidad, se recomienda realizar la transición a Azure AD y evitar la autenticación de clave compartida y SAS. Para obtener información sobre cómo evitar la autenticación de clave compartida y SAS, consulte [Prevent Shared Key authorization for an Azure Storage account](../common/shared-key-authorization-prevent.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=portal) (Impedir la autorización de clave compartida para una Azure Storage compartido). Para empezar con Azure AD, consulte [Autenticación del acceso a blobs mediante Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="optimize-cost-for-infrequent-queries"></a>Optimización del coste de las consultas poco frecuentes

Si mantiene grandes cantidades de datos de registro pero planea consultarlos solo ocasionalmente (por ejemplo, para cumplir las obligaciones de cumplimiento y seguridad), considere la posibilidad de archivar los registros en una cuenta de almacenamiento en lugar de usar Log Analytics. En el caso de un gran número de transacciones, el coste de usar Log Analytics podría ser alto en relación con el archivado en el almacenamiento y el uso de otras técnicas de consulta. Log Analytics tiene sentido en los casos en los que desea usar las funcionalidades enriquecidas de Log Analytics. Puede reducir el coste de consultar datos archivando los registros en una cuenta de almacenamiento y, a continuación, consultando esos registros en un área de trabajo de Synapse.

Puede exportar registros a Log Analytics para obtener funcionalidades de consulta nativas enriquecidas. Si tiene transacciones masivas en la cuenta de almacenamiento, el coste de usar registros con Log Analytics puede ser alto. Consulte [Precios de Azure Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). Si solo tiene previsto consultar registros ocasionalmente (por ejemplo, los registros de consulta para la auditoría de cumplimiento), puede considerar la posibilidad de reducir el coste total mediante la exportación de registros a la cuenta de almacenamiento y, a continuación, usar una solución de consulta sin servidor sobre los datos de registro, por ejemplo, Azure Synapse.

Con Azure Synapse, puede crear un grupo de SQL sin servidores para consultar los datos de registro cuando sea necesario. Esto podría ahorrar costes significativamente. 

1. Enrutamiento de registros a la cuenta de almacenamiento Consulte [Creación de una configuración de diagnóstico](monitor-blob-storage.md#creating-a-diagnostic-setting).

2. Creación y configuración del área de trabajo de Synapse Consulte [Inicio rápido: Creación de un área de trabajo de Synapse](../../synapse-analytics/quickstart-create-workspace.md).

2. Registros de consultas. Consulte [archivos JSON mediante un grupo de SQL sin servidor en Azure Synapse Analytics](../../synapse-analytics/sql/query-json-files.md).

   Este es un ejemplo:

   ```sql
    select
        JSON_VALUE(doc, '$.time') AS time,
        JSON_VALUE(doc, '$.properties.accountName') AS accountName,
        JSON_VALUE(doc, '$.identity.type') AS identityType,    
        JSON_VALUE(doc, '$.identity.requester.objectId') AS requesterObjectId,
        JSON_VALUE(doc, '$.operationName') AS operationName,
        JSON_VALUE(doc, '$.callerIpAddress') AS callerIpAddress,
        JSON_VALUE(doc, '$.uri') AS uri
        doc
    from openrowset(
            bulk 'https://demo2uswest4log.blob.core.windows.net/insights-logs-storageread/resourceId=/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/demo2uswest/blobServices/default/y=2021/m=03/d=19/h=*/m=*/PT1H.json',
            format = 'csv', fieldterminator ='0x0b', fieldquote = '0x0b'
        ) with (doc nvarchar(max)) as rows
    order by JSON_VALUE(doc, '$.time') desc

   ```

## <a name="see-also"></a>Vea también

- [Supervisión de Azure Blob Storage](monitor-blob-storage.md).
- [Tutorial: Uso de consultas de Kusto en Azure Data Explorer y Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).
- [Introducción a consultas de registros en Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

