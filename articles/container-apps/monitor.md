---
title: Supervisión de una aplicación en la versión preliminar de Azure Container Apps
description: Obtenga información sobre cómo se supervisan y registran las aplicaciones en Azure Container Apps.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 852506b024939fdbdde6fa4d3ffebbe1c08b38fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093607"
---
# <a name="monitor-an-app-in-azure-container-apps-preview"></a>Supervisión de una aplicación en la versión preliminar de Azure Container Apps

Azure Container Apps recopila un amplio conjunto de datos sobre la aplicación de contenedor y los almacena mediante [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md). En este artículo, se describen los registros disponibles y cómo escribir y ver registros.

## <a name="writing-to-a-log"></a>Escritura en un registro

Al escribir en los [flujos de salida estándar (stdout) o de error estándar (stderr)](https://wikipedia.org/wiki/Standard_streams), los agentes de registro de Container Apps escriben registros para cada mensaje.

Cuando se registra un mensaje, se recopila la siguiente información en la tabla de registro:

| Propiedad | Comentarios |
|---|---|
| `RevisionName` | |
| `ContainerAppName` | |
| `ContainerGroupID` | |
| `ContainerGroupName` | |
| `ContainerImage` | |
| `ContainerID` | Identificador único del contenedor. Puede usar este valor para ayudar a identificar los bloqueos del contenedor. |
| `Stream` | Muestra si se usa `stdout` o `stderr` para el registro. |
| `EnvironmentName` | |

### <a name="simple-text-vs-structured-data"></a>Texto simple frente a datos estructurados

Puede registrar una cadena de texto única o una línea de datos JSON serializados. La información se muestra de forma diferente en función del tipo de datos que registre.

| Tipo de datos | Descripción |
|---|---|
| Línea de texto única | El texto aparece en la columna `Log_s`. |
| JSON serializado | El agente de registro analiza los datos y se muestran en columnas que coinciden con los nombres de las propiedades del objeto JSON. |

## <a name="viewing-logs"></a>Visualización de los registros

Los datos registrados mediante una aplicación de contenedor se almacenan en la tabla personalizada `ContainerAppConsoleLogs_CL` del área de trabajo de Log Analytics. Puede ver los registros mediante Azure Portal o con la CLI.

Establezca el nombre del grupo de recursos y el área de trabajo de Log Analytics y, a continuación, recupere el valor de `LOG_ANALYTICS_WORKSPACE_CLIENT_ID` con los siguientes comandos.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"

LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"

$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv
```

---

Use el siguiente comando de la CLI para ver los registros en la línea de comandos.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" `
  --out table
```

---

En la salida siguiente, se muestra el tipo de respuesta que se espera del comando de la CLI.

```console
ContainerAppName_s    Log_s                 TableName      TimeGenerated
--------------------  --------------------  -------------  ------------------------
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:09:00.168Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:36.197Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:43.171Z
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Protección de la aplicación de contenedor](secure-app.md)
