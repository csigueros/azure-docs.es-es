---
title: Recopilación de los registros de aplicaciones y las métricas de Apache Spark mediante la cuenta de Azure Storage (versión preliminar)
description: En este artículo se muestra cómo usar la extensión de emisión de diagnósticos de Synapse Spark para recopilar registros, registros de eventos y metrics.cluster y cómo se integran los paneles de Grafana.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: c29cdbd9879397b3e171160f93ccd0ac712467db
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544748"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-storage-accountpreview"></a>Recopilación de los registros de aplicaciones y las métricas de Apache Spark mediante la cuenta de Azure Storage (versión preliminar)

La extensión de emisión de diagnósticos de Synapse Apache Spark es una biblioteca que permite a la aplicación Apache Spark emitir registros, registros de eventos y métricas a uno o varios destinos, incluidos Azure Log Analytics, Azure Storage y Azure Event Hubs. 

En este tutorial aprenderá a usar la extensión de emisión de diagnósticos de Synapse Apache Spark para emitir registros, registros de eventos y métricas de las aplicaciones Apache Spark a la cuenta de almacenamiento de Azure.

## <a name="collect-logs-and-metrics-to-storage-account"></a>Recopilación de registros y métricas en la cuenta de almacenamiento

### <a name="step-1-create-a-storage-account"></a>Paso 1: Crear una cuenta de almacenamiento

Para recopilar registros de diagnóstico y métricas en la cuenta de almacenamiento, puede usar cuentas de Azure Storage existentes. O bien, si no tiene una, puede [crear una cuenta de Azure Blob Storage](../../storage/common/storage-account-create.md) o [crear una cuenta de almacenamiento para usarla con Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

### <a name="step-2-create-an-apache-spark-configuration-file"></a>Paso 2: Creación de un archivo de configuración de Apache Spark

Cree un archivo `diagnostic-emitter-azure-storage-conf.txt` y copie el siguiente contenido en él. Como alternativa, descargue un [archivo de plantilla de ejemplo](https://go.microsoft.com/fwlink/?linkid=2169375) para la configuración del grupo de Apache Spark.

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureStorage
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.uri https://<my-blob-storage>.blob.core.windows.net/<container-name>/<folder-name>
spark.synapse.diagnostic.emitter.MyDestination1.auth AccessKey
spark.synapse.diagnostic.emitter.MyDestination1.secret <storage-access-key>
```

En el archivo de configuración, rellene estos parámetros: `<my-blob-storage>`, `<container-name>`, `<folder-name>`, `<storage-access-key>`.
Para mayor descripción de los parámetros, consulte las [configuraciones de Azure Storage](#available-configurations).

### <a name="step-3-upload-the-apache-spark-configuration-file-to-spark-pool"></a>Paso 3: Carga del archivo de configuración de Apache Spark en el grupo de Spark

1. Vaya al grupo de Apache Spark en Synapse Studio **(Administrar > Grupos de Apache Spark)** .
1. Haga clic en el botón **"..."** situado a la derecha del grupo de Apache Spark y seleccione **Apache Spark configuration** (Configuración de Apache Spark).
1. Haga clic en **Cargar** y elija el archivo de configuración ".txt". Haga clic en **Aplicar**.

### <a name="step-4-view-the-logs-files-in-azure-storage-account"></a>Paso 4: Visualización de los archivos de registro en la cuenta de almacenamiento de Azure


Después de enviar un trabajo al grupo de Apache Spark configurado, debería poder ver los archivos de registro y las métricas en la cuenta de almacenamiento de destino.
Los registros se colocarán en las rutas de acceso correspondientes según las distintas aplicaciones según `<workspaceName>.<sparkPoolName>.<livySessionId>`.
Todos los archivos de registro estarán en formato de líneas JSON (también denominado JSON delimitado por nuevas líneas, ndjson), lo cual resulta muy práctico para el procesamiento de los datos.

## <a name="available-configurations"></a>Configuraciones disponibles

| Configuración | Descripción |
| --- | --- |
| `spark.synapse.diagnostic.emitters`                                         | Necesario. Nombres de destino separados por comas de emisores de diagnósticos. Por ejemplo: `MyDest1,MyDest2` |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | Necesario. Tipo de destino integrado. Para habilitar el destino de almacenamiento de Azure, `AzureStorage` debe incluirse en este campo. |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | Opcional. Categorías de registro seleccionadas separadas por comas. Entre los valores disponibles se incluyen: `DriverLog`, `ExecutorLog`, `EventLog` y `Metrics`. Si no se especifica, se establece en **todas** categorías. |
| `spark.synapse.diagnostic.emitter.<destination>.auth`                       | Necesario. `AccessKey` para usar la autorización de [clave de acceso](../../storage/common/storage-account-keys-manage.md) de la cuenta de almacenamiento. `SAS` para la autorización mediante [firmas de acceso compartido](../../storage/common/storage-sas-overview.md). |
| `spark.synapse.diagnostic.emitter.<destination>.uri`                        | Necesario. URI de la carpeta del contenedor de blobs de destino. Debe coincidir con el patrón `https://<my-blob-storage>.blob.core.windows.net/<container-name>/<folder-name>`. |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | Opcional. Contenido del secreto (AccessKey o SAS). |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | Es necesario si `.secret` no se especifica. Nombre del [almacén de claves de Azure](../../key-vault/general/overview.md) donde se almacena el secreto (AccessKey o SAS). |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | Requerido si se especifica `.secret.keyVault`. Nombre del almacén de claves de Azure donde se almacena el secreto (AccessKey o SAS). |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | Opcional. Nombres de eventos de Spark separados por comas, puede especificar qué eventos se recopilan. Por ejemplo: `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | Opcional. Nombres del registrador log4j separados por comas, puede especificar qué registros se recopilan. Por ejemplo: `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | Opcional. Sufijos de nombre de métrica de Spark separados por comas, puede especificar qué métricas se recopilan. Por ejemplo: `jvm.heap.used` |

## <a name="log-data-sample"></a>Ejemplo de datos de registro

Este es un registro de ejemplo en formato JSON:

```json
{
    "timestamp": "2021-01-02T12:34:56.789Z",
    "category": "Log|EventLog|Metrics",
    "workspaceName": "<my-workspace-name>",
    "sparkPool": "<spark-pool-name>",
    "livyId": "<livy-session-id>",
    "applicationId": "<application-id>",
    "applicationName": "<application-name>",
    "executorId": "<driver-or-executor-id>",
    "properties": {
        // The message properties of logs, events and metrics.
        "timestamp": "2021-01-02T12:34:56.789Z",
        "message": "Registering signal handler for TERM",
        "logger_name": "org.apache.spark.util.SignalUtils",
        "level": "INFO",
        "thread_name": "main"
        // ...
    }
}
```

## <a name="synapse-workspace-with-data-exfiltration-protection-enabled"></a>Área de trabajo de Synapse con la protección de datos contra la filtración habilitada

Las áreas de trabajo de Azure Synapse Analytics admiten la habilitación de la protección contra la filtración de datos. Con la protección contra la filtración, los registros y las métricas no se pueden enviar directamente a los puntos de conexión de destino. Puede crear los [puntos de conexión privados administrados](../../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md) correspondientes para distintos puntos de conexión de destino o [crear reglas de firewall de IP](../../synapse-analytics/security/synapse-workspace-ip-firewall.md) en este escenario.


1. Vaya a **Synapse Studio > Manage > Managed private endpoints** (Administrar puntos de conexión privados administrados), haga clic en el botón **Nuevo**, seleccione **Azure Blob Storage** o **Azure Data Lake Storage Gen2** y seleccione **Continuar**.
   > [!div class="mx-imgBorder"]
   > ![Creación del punto de conexión privado administrado 1](./media/azure-synapse-diagnostic-emitters-azure-storage/create-private-endpoint-1.png)
2. Elija la cuenta de Azure Storage en el valor de **nombre de cuenta de Storage** y haga clic en el botón **Crear**.
   > [!div class="mx-imgBorder"]
   > ![Creación del punto de conexión privado administrado 2](./media/azure-synapse-diagnostic-emitters-azure-storage/create-private-endpoint-2.png)
3. Espere unos minutos durante el aprovisionamiento del punto de conexión privado.
4. Vaya a la cuenta de almacenamiento en Azure Portal; en la página **Redes** > **Conexiones del punto de conexión privado**, seleccione la conexión que acaba de aprovisionar y seleccione **Aprobar**.


