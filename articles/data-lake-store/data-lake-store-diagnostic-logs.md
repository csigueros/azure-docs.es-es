---
title: Visualización de registros de diagnóstico de Azure Data Lake Storage Gen1 | Microsoft Docs
description: 'Sepa cómo configurar registros de diagnóstico y tener acceso a ellos para Azure Data Lake Storage Gen1. '
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 03/26/2018
ms.author: normesta
ms.openlocfilehash: 325bca316aaf4add854ea473f71c9f9883fa42eb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602942"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Acceso a los registros de diagnóstico de Azure Data Lake Storage Gen1
Sepa cómo habilitar el registro de diagnósticos en su cuenta de Azure Data Lake Storage Gen1 y cómo ver los registros recopilados relativos a su cuenta.

Las organizaciones pueden habilitar el registro de diagnósticos en sus cuentas de Azure Data Lake Storage Gen1 para recopilar trazas de auditoría de acceso a datos que proporcionan información como, por ejemplo, la lista de usuarios que tienen acceso a los datos, con qué frecuencia se tiene acceso a ellos, qué cantidad de datos se almacena en la cuenta, etc. Cuando está habilitado, el diagnóstico o las solicitudes se registran del mejor modo posible. Tanto las entradas de registro de solicitudes como de diagnóstico se crean solo si se presentan solicitudes al punto de conexión de servicio.

## <a name="prerequisites"></a>Prerrequisitos
* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Cuenta de Azure Data Lake Storage Gen1**. Siga las instrucciones de [Introducción a Azure Data Lake Storage Gen1 con Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Habilitación del registro de diagnósticos en la cuenta de Azure Data Lake Storage Gen1
1. Inicie sesión en el nuevo [Azure Portal](https://portal.azure.com).
2. Abra la cuenta de Azure Data Lake Storage Gen1 y, en la hoja de la cuenta de Azure Data Lake Storage Gen1, haga clic en **Configuración de diagnóstico**.
3. En la hoja **Configuración de diagnóstico**, haga clic en **Activar diagnósticos**.

    ![Captura de pantalla de la cuenta de Data Lake Storage Gen 1 con la opción Configuración de diagnóstico y la opción Activar diagnósticos seleccionadas.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Habilitar registros de diagnóstico")

3. En la hoja **Configuración de diagnóstico**, realice los siguientes cambios para configurar el registro de diagnóstico.
   
    ![Captura de pantalla de la sección Configuración de diagnóstico con el cuadro de texto Nombre y la opción Guardar seleccionada.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Habilitar registros de diagnóstico")
   
   * En el **Nombre**, escriba un valor para la configuración del registro de diagnóstico.
   * Puede optar por almacenar o procesar los datos de maneras diferentes.
     
        * Seleccione la opción **Archive to a storage account** (Archivar en cuenta de almacenamiento) para almacenar los registros en una cuenta de Azure Storage. Use esta opción si quiere archivar los datos que se procesarán por lotes más adelante. Si selecciona esta opción, debe proporcionar una cuenta de Azure Storage para guardar los registros.
        
        * Seleccione la opción **Stream to an event hub** (Transmitir a un centro de eventos) para transmitir los datos de registro a una instancia de Azure Event Hubs. Lo más probable es que use esta opción si tiene una canalización de procesamiento de bajada para analizar los registros entrantes en tiempo real. Si selecciona esta opción, debe proporcionar los detalles del Centro de eventos de Azure que quiera usar.

        * Seleccione la opción **Enviar a Log Analytics** para usar el servicio de Azure Monitor con el fin de analizar los datos de registro generados. Si selecciona esta opción, debe proporcionar los detalles del área de trabajo de Log Analytics que usaría para realizar análisis de registros. Consulte [Visualización o análisis de los datos recopilados con la búsqueda de registros de Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md) para obtener más información sobre el uso de los registros de Azure Monitor.
     
   * Indique si quiere obtener los registros de auditoría, los registros de solicitudes o ambos.
   * Especifique el número de días durante los que deben conservarse los datos. La retención solo es aplicable si está utilizando la cuenta de Azure Storage para archivar datos de registro.
   * Haga clic en **Save**(Guardar).

Una vez habilitada la configuración de diagnóstico, puede ver los registros en la pestaña **Registros de diagnóstico** .

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Visualización de los registros de diagnóstico de la cuenta de Azure Data Lake Storage Gen1
Existen dos formas de ver los datos de registro para la cuenta de Azure Data Lake Storage Gen1.

* Desde la vista de configuración de la cuenta de Data Lake Storage Gen1
* Desde la cuenta de Azure Storage donde se almacenan los datos

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Mediante la vista de configuración de Data Lake Storage Gen1
1. En la hoja **Configuración** de su cuenta de Data Lake Storage Gen1, haga clic en **Registros de diagnóstico**.
   
    ![Visualización de los registros de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Visualización de los registros de diagnóstico") 
2. En la hoja **Registros de diagnóstico**, debería ver los registros clasificados por **Registros de auditoría** y **Registros de solicitudes**.
   
   * Los registros de solicitudes capturan todas las solicitudes API realizadas en la cuenta de Data Lake Storage Gen1.
   * Los registros de auditoría son parecidos a los de solicitud, pero proporcionan un desglose mucho más detallado de las operaciones que tienen lugar en la cuenta de Data Lake Storage Gen1. Por ejemplo, una llamada de API de carga única en los registros de solicitud podría producir varias operaciones "Append" en los registros de auditoría.
3. Para descargar los registros, haga clic en el vínculo **Descargar** de cada entrada de registro.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>En la cuenta de Azure Storage que contiene los datos de registro
1. Abra la hoja de la cuenta de Azure Storage asociada con Data Lake Storage Gen1 para el registro y haga clic en Blobs. La hoja **Blob service** muestra dos contenedores.
   
    ![Captura de pantalla de la hoja Data Lake Storage Gen 1 con la opción Blobs seleccionada y la hoja Blob service con los nombres de los dos servicios de blob seleccionados.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visualización de los registros de diagnóstico")
   
   * El contenedor **insights-logs-audit** contiene los registros de auditoría.
   * El contenedor **insights-logs-requests** contiene los registros de solicitudes.
2. Dentro de estos contenedores, los registros se almacenan con la siguiente estructura.
   
    ![Captura de pantalla de la estructura de registro almacenada en el contenedor.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Visualización de los registros de diagnóstico")
   
    Por ejemplo, la ruta de acceso completa a un registro de auditoría sería `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    De forma similar, la ruta de acceso completa a un registro de solicitudes sería `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Comprender la estructura de los datos de registro
Los registros de auditoría y de solicitud tienen un formato JSON. En esta sección, veremos la estructura de JSON de los registros de auditoría y de solicitud.

### <a name="request-logs"></a>Request Logs
Este es un ejemplo de una entrada en el registro de solicitud con formato JSON. Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-07T21:02:53.456Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"StoreIngressSize":0 ,"StoreEgressSize":4096,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z","QueryParameters":"api-version=<version>&op=<operationName>"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Esquema de un registro de solicitud
| Nombre | Tipo | Descripción |
| --- | --- | --- |
| time |String |Marca de tiempo (en UTC) del registro. |
| resourceId |String |Identificador del recurso en el que tuvo lugar la operación. |
| category |String |Categoría del registro. Por ejemplo, **Requests**. |
| operationName |String |Nombre de la operación que se registra. Por ejemplo, getfilestatus. |
| resultType |String |Estado de la operación. Por ejemplo, 200. |
| callerIpAddress |String |Dirección IP del cliente que realiza la solicitud. |
| correlationId |String |Identificador del registro que se puede usar para agrupar un conjunto de entradas de registro relacionadas. |
| identity |Object |Identidad que ha generado el registro. |
| properties |JSON |Vea más abajo para obtener más información. |

#### <a name="request-log-properties-schema"></a>Esquema de propiedades de un registro de solicitud
| Nombre | Tipo | Descripción |
| --- | --- | --- |
| HttpMethod |String |Método HTTP usado en la operación. Por ejemplo, GET. |
| Path |String |Ruta de acceso en la que se ha realizado la operación. |
| RequestContentLength |int |Longitud del contenido de la solicitud HTTP. |
| ClientRequestId |String |Identificador que distingue de manera única esta solicitud. |
| StartTime |String |Hora a la que el servidor ha recibido la solicitud. |
| EndTime |String |Hora a la que el servidor ha enviado una respuesta. |
| StoreIngressSize |long |Tamaño en bytes de entrada a Data Lake Store |
| StoreEgressSize |long |Tamaño en bytes de salida de Data Lake Store |
| QueryParameters |String |Descripción: Estos son los parámetros de consulta http. Ejemplo 1: api-version=2014-01-01&op=getfilestatus Ejemplo 2: op=APPEND&append=true&syncFlag=DATA&filesessionid=bee3355a-4925-4435-bb4d-ceea52811aeb&leaseid=bee3355a-4925-4435-bb4d-ceea52811aeb&offset=28313319&api-version=2017-08-01 |

### <a name="audit-logs"></a>Registros de auditoría
Este es un ejemplo de una entrada en el registro de auditoría con formato JSON. Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>Esquema de un registro de auditoría
| Nombre | Tipo | Descripción |
| --- | --- | --- |
| time |String |Marca de tiempo (en UTC) del registro. |
| resourceId |String |Identificador del recurso en el que tuvo lugar la operación. |
| category |String |Categoría del registro. Por ejemplo, **Audit**. |
| operationName |String |Nombre de la operación que se registra. Por ejemplo, getfilestatus. |
| resultType |String |Estado de la operación. Por ejemplo, 200. |
| resultSignature |String |Detalles adicionales sobre la operación. |
| correlationId |String |Identificador del registro que se puede usar para agrupar un conjunto de entradas de registro relacionadas. |
| identity |Object |Identidad que ha generado el registro. |
| properties |JSON |Vea más abajo para obtener más información. |

#### <a name="audit-log-properties-schema"></a>Esquema de propiedades de un registro de auditoría
| Nombre | Tipo | Descripción |
| --- | --- | --- |
| StreamName |String |Ruta de acceso en la que se ha realizado la operación. |

## <a name="samples-to-process-the-log-data"></a>Ejemplos para procesar los datos de registro
Al enviar registros de Data Lake Storage Gen1 a los registros de Azure Monitor (consulte [Visualización o análisis de los datos recopilados con la búsqueda de registros de Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md) para obtener más información sobre el uso de los registros de Azure Monitor), la consulta siguiente devolverá una tabla que contiene una lista de nombres para mostrar de usuarios, la hora de los eventos y el recuento de eventos para la hora del evento junto con un gráfico visual. Se puede modificar fácilmente para que muestre el identificador único de usuario u otros atributos:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 proporciona un ejemplo de cómo procesar y analizar los datos de registro. Puede encontrar el ejemplo en [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Consulte también
* [Introducción a Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Protección de datos en Data Lake Storage Gen1](data-lake-store-secure-data.md)
