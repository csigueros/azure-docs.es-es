---
title: Procedimiento para supervisar Azure Purview
description: Obtenga información sobre cómo configurar las métricas, las alertas y la configuración de diagnóstico de Azure Purview mediante Azure Monitor.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 7028bb42481c2559d7aa9d044bd25771efc41f31
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213747"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Métricas de Azure Purview en Azure Monitor

En este artículo se describe cómo configurar las métricas, las alertas y la configuración de diagnóstico de Azure Purview mediante Azure Monitor.

## <a name="monitor-azure-purview"></a>Supervisión de Azure Purview

Los administradores de Azure Purview pueden usar Azure Monitor para realizar un seguimiento del estado operativo de la cuenta de Purview. Las métricas se recopilan para proporcionar puntos de datos a fin de permitirle realizar un seguimiento de posibles problemas, solucionar problemas y mejorar la confiabilidad de la cuenta de Purview. Se envían métricas a Azure Monitor de los eventos que se producen en Azure Purview.

## <a name="aggregated-metrics"></a>Métricas agregadas

Es posible acceder a las métricas desde Azure Portal para una cuenta de Purview. El acceso a las métricas se controla mediante la asignación de roles de la cuenta de Purview. Los usuarios deben formar parte del rol "Lector de supervisión" en Azure Purview para ver las métricas. Consulte los [permisos del rol Lector de supervisión](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) para obtener más información sobre los niveles de acceso de los roles.

La persona que crea la cuenta de Purview obtiene automáticamente los permisos para ver las métricas. Si otras personas desean ver las métricas, debe agregarlas al rol **Lector de supervisión** mediante los pasos siguientes:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Adición de un usuario al rol Lector de supervisión

Para agregar un usuario al rol **Lector de supervisión**, el propietario de la cuenta de Purview o el propietario de la suscripción pueden seguir estos pasos:

1. Diríjase a [Azure Portal](https://portal.azure.com) y busque el nombre de la cuenta de Azure Purview.

2. Seleccione **Access Control (IAM)** .

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Captura de pantalla que muestra cómo acceder a IAM.":::

3. Seleccione **Agregar una asignación de roles**.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Captura de pantalla que muestra cómo agregar una asignación de roles.":::

4. Seleccione el rol **Lector de supervisión** y establezca la asignación de acceso para **usuarios, grupos o entidades de servicio de Azure AD**. Asimismo, asigne la cuenta de AAD para acceder a las métricas.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Captura de pantalla que muestra cómo agregar el rol Lector de supervisión.":::

## <a name="metrics-visualization"></a>Visualización de las métricas

Los usuarios del rol **Lector de supervisión** pueden ver las métricas agregadas y los registros de diagnóstico enviados a Azure Monitor. Las métricas se muestran en Azure Portal para la cuenta de Purview correspondiente. En Azure Portal, seleccione la sección Métricas para ver la lista de todas las métricas disponibles.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Captura de pantalla que muestra la sección de métricas de Purview disponibles." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Los usuarios de Azure Purview también pueden acceder a la página de métricas directamente desde el centro de administración de la cuenta de Azure Purview. Seleccione Azure Monitor en la página principal del centro de administración de Purview para iniciar Azure Portal.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Captura de pantalla que muestra cómo iniciar las métricas de Purview desde el centro de administración." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Métricas disponibles

Para familiarizarse con el uso de la sección de métricas en Azure Portal, lea previamente los dos documentos siguientes. [Introducción al Explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) y [Características avanzadas del Explorador de métricas de Azure](../azure-monitor/essentials/metrics-charts.md).

La tabla siguiente contiene la lista de métricas disponibles para explorar en Azure Portal:

| Nombre de la métrica | Espacio de nombres de métrica | Tipo de agregación | Descripción |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Examen cancelado | Examen automatizado | Sum <br> Count | Permite agregar los exámenes de origen de datos cancelados durante el período de tiempo. |
| Examen completado | Examen automatizado | Sum <br> Count | Permite agregar los exámenes de origen de datos completados durante el período de tiempo. |
| Examen con errores | Examen automatizado | Sum <br> Count | Permite agregar los exámenes de origen de datos con errores durante el período de tiempo. |
| Tiempo invertido en el examen | Examen automatizado | Min <br> Max <br> Sum <br> Avg. | Permite agregar el tiempo total que han tardado los exámenes durante el período de tiempo. |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Registros de diagnóstico para la cuenta de Azure Storage

Los eventos de telemetría sin procesar se emiten a Azure Monitor. Los eventos se pueden registrar en una cuenta de almacenamiento de cliente que elija para su posterior análisis. La exportación de registros se realiza a través de la configuración de diagnóstico de la cuenta Purview en Azure Portal.

Siga los pasos para crear una configuración de diagnóstico para la cuenta de Azure Purview.

1. Cree de una configuración de diagnóstico para recopilar registros de plataforma y métricas mediante las instrucciones de este artículo: [Creación de una configuración de diagnóstico para enviar registros de plataforma y métricas a diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md). Seleccione el destino solamente como la cuenta de almacenamiento de Azure.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Captura de pantalla que muestra cómo se crea un registro de diagnóstico." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Registre los eventos en una cuenta de almacenamiento. Se recomienda usar una cuenta de almacenamiento dedicada para archivar los registros de diagnóstico. Cree una siguiendo el artículo [Creación de una cuenta de Storage](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Captura de pantalla que muestra la asignación de la cuenta de almacenamiento para el registro de diagnóstico." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Puede tardar 15 minutos en empezar a recibir registros en la cuenta de almacenamiento recién creada. [Consulte la retención de datos y el esquema de registros de recursos en la cuenta de Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Una vez configurados los registros de diagnóstico, los eventos fluyen a la cuenta de almacenamiento.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

El evento realiza un seguimiento del ciclo de vida del examen. Una operación de examen sigue el progreso a través de una secuencia de estados, como En cola, En ejecución y, por último, el estado terminal Correcto, Error o Cancelado. Se registra un evento para cada transición de estado, y el esquema del evento tendrá las propiedades siguientes.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

En la sección siguiente se muestra el registro de ejemplo de una instancia de evento.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Pasos siguientes

[Visualización de información de recursos](asset-insights.md)