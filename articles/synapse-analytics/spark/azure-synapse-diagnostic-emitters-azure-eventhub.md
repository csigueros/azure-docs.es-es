---
title: Recopilación de los registros de aplicaciones y las métricas de Apache Spark mediante Azure Event Hubs (versión preliminar)
description: En este tutorial aprenderá a usar la extensión de emisión de diagnósticos de Synapse Apache Spark para emitir registros, registros de eventos y métricas de las aplicaciones Apache Spark a Azure Event Hubs.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: c3b8fbda0eaaa932f784540cc55d0a4e583927f0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544717"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-event-hubs-preview"></a>Recopilación de los registros de aplicaciones y las métricas de Apache Spark mediante Azure Event Hubs (versión preliminar)

La extensión de emisión de diagnósticos de Synapse Apache Spark es una biblioteca que permite a la aplicación Apache Spark emitir registros, registros de eventos y métricas a uno o varios destinos, incluidos Azure Log Analytics, Azure Storage y Azure Event Hubs. 

En este tutorial aprenderá a usar la extensión de emisión de diagnósticos de Synapse Apache Spark para emitir registros, registros de eventos y métricas de las aplicaciones Apache Spark a Azure Event Hubs.

## <a name="collect-logs-and-metrics-to-azure-event-hubs"></a>Recopilación de registros y métricas en Azure Event Hubs

### <a name="step-1-create-an-azure-event-hub-instance"></a>Paso 1: Creación de una instancia de Azure Event Hubs

Para recopilar registros de diagnóstico y métricas en Azure Event Hubs, puede usar una instancia de Azure Event Hubs existente.
Si no tiene, puede [crear un centro de eventos](../../event-hubs/event-hubs-create.md).

### <a name="step-2-create-a-apache-spark-configuration-file"></a>Paso 2: Creación de un archivo de configuración de Apache Spark

Cree un archivo `diagnostic-emitter-azure-event-hub-conf.txt` y copie el siguiente contenido en él. Como alternativa, descargue un [archivo de plantilla de ejemplo](https://go.microsoft.com/fwlink/?linkid=2169375) para la configuración del grupo de Apache Spark.

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureEventHub
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.secret <connection-string>
```

En el archivo de configuración, rellene estos parámetros: `<connection-string>`.
Para mayor descripción de los parámetros, consulte las [configuraciones de Azure Event Hubs](#available-configurations).

### <a name="step-3-upload-the-apache-spark-configuration-file-to-apache-spark-pool"></a>Paso 3: Carga del archivo de configuración de Apache Spark en el grupo de Apache Spark

1. Vaya al grupo de Apache Spark en Synapse Studio **(Administrar > Grupos de Apache Spark)** .
2. Haga clic en el botón **"..."** situado a la derecha del grupo de Apache Spark y seleccione **Apache Spark configuration** (Configuración de Apache Spark).
3. Haga clic en **Cargar**, elija el archivo de configuración ".txt" y haga clic en **Aplicar**.

## <a name="available-configurations"></a>Configuraciones disponibles

| Configuración                                                               | Descripción                                                                                                                                                                                          |
| --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `spark.synapse.diagnostic.emitters`                                         | Necesario. Nombres de destino separados por comas de emisores de diagnósticos.                                                                                                                              |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | Necesario. Tipo de destino integrado. Para habilitar Azure Event Hubs como destino, el valor debe ser `AzureEventHub`.                                                                                    |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | Opcional. Categorías de registro seleccionadas separadas por comas. Entre los valores disponibles se incluyen: `DriverLog`, `ExecutorLog`, `EventLog` y `Metrics`. Si no se especifica, se establece en **todas** categorías.              |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | Opcional. Cadena de conexión de la instancia de Azure Event Hubs. Este campo debe coincidir con este patrón `Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>;EntityPath=<PathName>`. |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | Es necesario si `.secret` no se especifica. Nombre del [almacén de claves de Azure](../../key-vault/general/overview.md) donde se almacena el secreto (cadena de conexión).                                                                  |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | Requerido si se especifica `.secret.keyVault`. Nombre del almacén de claves de Azure donde se almacena el secreto (cadena de conexión).                                                                         |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | Opcional. Nombres de eventos de Spark separados por comas, puede especificar qué eventos se recopilan. Por ejemplo: `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | Opcional. Nombres del registrador log4j separados por comas, puede especificar qué registros se recopilan. Por ejemplo: `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | Opcional. Sufijos de nombre de métrica de Spark separados por comas, puede especificar qué métricas se recopilan. Por ejemplo: `jvm.heap.used` |


> [!NOTE]
>
> La cadena de conexión de la instancia de Azure Event Hubs siempre debe incluir `EntityPath`, que es el nombre de la instancia de Azure Event Hubs.

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




