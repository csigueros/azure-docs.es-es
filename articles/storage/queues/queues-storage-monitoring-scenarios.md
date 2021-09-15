---
title: Procedimientos recomendados para supervisar Azure Queue Storage
description: Conozca procedimientos recomendados y cómo aplicarlos al usar métricas y registros para supervisar su instancia de Azure Queue Storage.
author: normesta
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.author: normesta
ms.date: 08/24/2021
ms.custom: monitoring
ms.openlocfilehash: e7344330740bebc41a49547c7602a852b5f7665f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868749"
---
# <a name="best-practices-for-monitoring-azure-queue-storage"></a>Procedimientos recomendados para supervisar Azure Queue Storage

En este artículo se ofrece una colección de escenarios comunes de supervisión de Queue Storage y se proporcionan procedimientos recomendados para llevarlos a cabo.  

## <a name="monitor-message-counts-in-each-queue"></a>Supervisión del número de mensajes de cada cola 

Puede supervisar el número de mensajes de todas las colas de una cuenta de almacenamiento mediante la métrica `QueueMessageCount`. Esta métrica se actualiza diariamente.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Si usa PowerShell, puede ejecutar un comando similar al siguiente:  

```powershell
(Get-AzMetric -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/queueServices/default -MetricName "QueueMessageCount").data.Average
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Si usa la CLI de Azure, puede ejecutar un comando similar al siguiente: 

```azurecli
az monitor metrics list --resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/ --metric "QueueMessageCount" --interval PT1H --query value[0].timeseries[0].data[0].average
```

---

En caso de que necesite determinar dinámicamente si se deben ajustar las cargas de trabajo para controlar el volumen de mensajes, puede consultar el número aproximado de mensajes de cada cola y, a continuación, adoptar la medida pertinente. Use la operación REST [Obtener metadatos de cola](/rest/api/storageservices/get-queue-metadata) o cualquiera de los SDK de Blob Storage admitidos para obtener el número aproximado de mensajes. 

En el siguiente ejemplo se usa la biblioteca de .NET v12 de Azure Storage para obtener el número aproximado de mensajes:

```csharp
        static async Task<string> RetrieveNextMessageAsync(QueueClient theQueue)
        {
            if (await theQueue.ExistsAsync())
            {
                QueueProperties properties = await theQueue.GetPropertiesAsync();

                if (properties.ApproximateMessagesCount > 0)
                {
                    QueueMessage[] retrievedMessage = await theQueue.ReceiveMessagesAsync(1);
                    string theMessage = retrievedMessage[0].MessageText;
                    await theQueue.DeleteMessageAsync(retrievedMessage[0].MessageId, retrievedMessage[0].PopReceipt);
                    return theMessage;
                }

                return null;
            }

            return null;
        }
```

Considere también la posibilidad de usar Service Bus, que admite mensajes por entidad. Para obtener más información, consulte [Referencia de datos para la supervisión de Azure Service Bus](../../service-bus-messaging/monitor-service-bus-reference.md). 

## <a name="audit-account-activity"></a>Auditoría de la actividad de las cuentas

En muchos casos, deberá auditar las actividades de las cuentas de almacenamiento para garantizar la seguridad y el cumplimiento. Las operaciones en las cuentas de almacenamiento se dividen en dos categorías: *Plano de control* y *Plano de datos*. 

Una operación de plano de control es una solicitud de Azure Resource Manager para crear una cuenta de almacenamiento o actualizar una propiedad de una cuenta de almacenamiento existente. Para obtener más información, vea [Azure Resource Manager](../../azure-resource-manager/management/overview.md). 

Una operación del plano de datos es una operación en los datos de una cuenta de almacenamiento que es el resultado de una solicitud al punto de conexión del servicio de almacenamiento. Por ejemplo, se ejecuta una operación de plano de datos cuando se agrega un mensaje a la cola. Para obtener más información, consulte la [API de Azure Storage](/rest/api/storageservices/). 

En la sección se muestra cómo identificar la información "cuándo", "quién", "qué" y "cómo" de las operaciones de plano de datos y de control.

### <a name="auditing-control-plane-operations"></a>Auditoría de las operaciones del plano de control

Las operaciones de Resource Manager se capturan en el [registro de actividad de Azure](../../azure-monitor/essentials/activity-log.md). Para ver el registro de actividad, abra la cuenta de almacenamiento en Azure Portal y, a continuación, seleccione **Registro de actividad**.

> [!div class="mx-imgBorder"]
> ![Registro de actividad](./media/queues-storage-monitoring-scenarios/activity-log.png):


Abra cualquier entrada de registro para ver el archivo JSON que describa la actividad. El siguiente JSON muestra la información "cuándo", "qué" y "cómo" de una operación de plano de control:

> [!div class="mx-imgBorder"]
> ![JSON de registro de actividad](./media/queues-storage-monitoring-scenarios/activity-log-json.png)

La disponibilidad de la información "quién" depende del método de autenticación que se usó para realizar la operación del plano de control. Si una entidad de seguridad de Azure AD realizó la autorización, el identificador de objeto de esa entidad de seguridad también aparecería en esta salida JSON (por ejemplo: `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`). Dado que es posible que no vea siempre otra información relacionada con la identidad, como una dirección de correo electrónico o un nombre, el identificador de objeto siempre es la mejor manera de identificar de forma única la entidad de seguridad. 

Puede encontrar el nombre descriptivo de esa entidad de seguridad tomando el valor del identificador de objeto y buscando la entidad de seguridad en la página Azure AD de Azure Portal. En la captura de pantalla siguiente se muestra un resultado de búsqueda en Azure AD.

> [!div class="mx-imgBorder"]
> ![Búsqueda en Azure Active Directory](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>Auditoría de operaciones del plano de datos

Las operaciones del plano de datos se capturan en los [registros de recursos de Azure Storage](monitor-queue-storage.md#analyzing-logs). Puede [configurar la opción Diagnóstico](monitor-queue-storage.md#send-logs-to-azure-log-analytics) para exportar los registros al área de trabajo de Log Analytics para una experiencia de consulta nativa. 

Esta es una consulta de Log Analytics que recupera la información "cuándo", "quién", "qué" y "cómo" en una lista de entradas de registro. 

```kusto
StorageQueueLogs 
| where TimeGenerated > ago(3d) 
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

Para la parte "cuándo" de la auditoría, el campo `TimeGenerated` muestra cuándo se registró la entrada del registro. 

Para la parte "qué" de la auditoría, el campo `Uri` muestra el elemento que fue modificado o leído. 

Para la parte "cómo" de la auditoría, el campo `OperationName` muestra qué operación se ejecutó. 

Para la parte "quién" de la auditoría, `AuthenticationType` muestra qué tipo de autenticación se usó para realizar una solicitud. Este campo puede mostrar cualquiera de los tipos de autenticación que admite Azure Storage, incluido el uso de una clave de cuenta, un token de SAS o Azure Active Directory (Azure AD). 

Si una solicitud se autenticó mediante Azure AD, el campo `RequesterObjectId` proporciona la manera más confiable de identificar la entidad de seguridad. Puede encontrar el nombre descriptivo de esa entidad de seguridad tomando el valor el valor del id. del campo `RequesterObjectId` y buscando la entidad de seguridad en la página Azure AD de Azure Portal. En la captura de pantalla siguiente se muestra un resultado de búsqueda en Azure AD.

> [!div class="mx-imgBorder"]
> ![Búsqueda en Azure Active Directory-2](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

En algunos casos, un nombre de usuario principal o *UPN* puede aparecer en los registros. Por ejemplo, si la entidad de seguridad es un usuario de Azure AD, es probable que aparezca el UPN. Para otros tipos de entidades de seguridad, como identidades administradas asignadas por el usuario, o en determinados escenarios, como la autenticación entre inquilinos Azure AD, el UPN no aparecerá en los registros. 

Esta consulta muestra todas las operaciones de escritura realizadas por entidades de seguridad de OAuth.

```kusto
StorageQueueLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutMessage"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

La clave compartida y la autenticación sas no proporcionan ningún medio para auditar identidades individuales. Por lo tanto, si desea mejorar la capacidad de realizar auditorías en función de la identidad, se recomienda realizar la transición a Azure AD y evitar la autenticación de clave compartida y SAS. Para obtener información sobre cómo evitar la autenticación de clave compartida y SAS, consulte [Prevent Shared Key authorization for an Azure Storage account](../common/shared-key-authorization-prevent.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json&tabs=portal) (Impedir la autorización de clave compartida para una Azure Storage compartido). Para empezar con Azure AD, consulte [Autenticación del acceso a blobs mediante Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="optimize-cost-for-infrequent-queries"></a>Optimización del coste de las consultas poco frecuentes

Puede exportar registros a Log Analytics para obtener funcionalidades de consulta nativas enriquecidas. Si tiene transacciones masivas en la cuenta de almacenamiento, el coste de usar registros con Log Analytics puede ser alto. Consulte [Precios de Azure Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). Si solo tiene previsto consultar registros ocasionalmente (por ejemplo, los registros de consulta para la auditoría de cumplimiento), puede considerar la posibilidad de reducir el coste total mediante la exportación de registros a la cuenta de almacenamiento y, a continuación, usar una solución de consulta sin servidor sobre los datos de registro, por ejemplo, Azure Synapse.

Con Azure Synapse, puede crear un grupo de SQL sin servidores para consultar los datos de registro cuando sea necesario. Esto podría ahorrar costes significativamente. 

1. Enrutamiento de registros a la cuenta de almacenamiento Consulte [Creación de una configuración de diagnóstico](monitor-queue-storage.md#creating-a-diagnostic-setting).

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

- [Supervisión de Azure Queue Storage](monitor-queue-storage.md)
- [Referencia de datos de supervisión de Azure Queue Storage](monitor-queue-storage-reference.md)
- [Tutorial: Uso de consultas de Kusto en Azure Data Explorer y Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).
- [Introducción a consultas de registros en Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

  

