---
title: 'Tutorial: Implementación de una aplicación de procesamiento en segundo plano con Azure Container Apps (versión preliminar)'
description: Aprenda a crear una aplicación que se ejecute continuamente en segundo plano con Azure Container Apps
services: app-service
author: jorgearteiro
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: joarteir
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c5cda6e2793ca70c1d53b8a4b4ce4235fa07efd
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577676"
---
# <a name="tutorial-deploy-a-background-processing-application-with-azure-container-apps-preview"></a>Tutorial: Implementación de una aplicación de procesamiento en segundo plano con Azure Container Apps (versión preliminar)

Azure Container Apps permite implementar aplicaciones sin necesidad de exponer puntos de conexión públicos. En este tutorial, implementará una aplicación de ejemplo que lee mensajes de una cola de Azure Storage y los registra en el área de trabajo de Azure Log Analytics. Con las reglas de escala de Container Apps, la aplicación se puede escalar y reducir verticalmente en función de la longitud de la cola de Azure Storage. Cuando no hay ningún mensaje en la cola, la aplicación de contenedor se reduce verticalmente a cero.

Aprenderá a:

> [!div class="checklist"]
> * Crear un entorno de Container Apps para implementar las aplicaciones de contenedor
> * Crear una cola de Azure Storage para enviar mensajes a la aplicación de contenedor
> * Implementar la aplicación de procesamiento en segundo plano como una aplicación de contenedor
> * Comprobar que la aplicación de contenedor procesa los mensajes de cola

## <a name="prerequisites"></a>Prerrequisitos

Los elementos siguientes son necesarios para completar este tutorial:

* **CLI de Azure**: debe tener la versión 2.29.0 o posterior de la CLI de Azure instalada en el equipo local.
  * Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).
  
## <a name="setup"></a>Configurar

En este tutorial se usan las siguientes variables de entorno:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

---

Cree una variable para el nombre de la cuenta de almacenamiento.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<MY_STORAGE_ACCOUNT_NAME>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

Reemplace el marcador de posición `<storage account name>` por un valor propio antes de ejecutar este fragmento de código. Los nombres de las cuentas de almacenamiento deben ser únicos en Azure, tener entre 3 y 24 caracteres, y solo pueden incluir números y letras en minúscula. La cuenta de almacenamiento se creará en un paso siguiente.

A continuación, inicie sesión en Azure desde la CLI.

Ejecute el siguiente comando y siga las indicaciones para completar el proceso de autenticación.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az login
```

---

Para asegurarse de que ejecuta la versión más reciente de la CLI, use el comando `upgrade`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az upgrade
```

---

A continuación, instale la extensión de Azure Container Apps en la CLI.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az extension add \
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az extension add `
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

---

Ahora que la extensión está instalada, registre el espacio de nombres `Microsoft.Web`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az provider register --namespace Microsoft.Web
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az provider register --namespace Microsoft.Web
```

---

Usará un grupo de recursos para organizar los servicios relacionados con la nueva aplicación de contenedor. Cree el grupo con el comando siguiente:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az group create `
  --name $RESOURCE_GROUP `
  --location $LOCATION
```

---

Con la CLI actualizada y un nuevo grupo de recursos disponible, puede crear un entorno de Container Apps e implementar la aplicación de contenedor.

## <a name="create-an-environment"></a>Creación de un entorno

Los entornos de Azure Container Apps actúan como límite seguro alrededor de un grupo de aplicaciones de contenedor. Cada aplicación de contenedor en el mismo entorno se implementa en la misma red virtual y escribe registros en la misma área de trabajo de Log Analytics.

Azure Log Analytics se usa para supervisar la aplicación de contenedor necesaria al crear un entorno de Container Apps.

Cree un área de trabajo de Log Analytics con el siguiente comando:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

A continuación, recupere el identificador de cliente y el secreto de cliente de Log Analytics. Asegúrese de ejecutar cada consulta por separado para que la solicitud se complete con tiempo suficiente.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"')
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

---

Las aplicaciones de contenedor individuales se implementan en un entorno de Azure Container Apps. Para crear el entorno, ejecute el comando siguiente:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp env create \
  --name $CONTAINERAPPS_ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location $LOCATION
```

---

## <a name="set-up-a-storage-queue"></a>Configuración de una cola de almacenamiento

Cree una cuenta de Azure Storage.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage account create \
  --name $STORAGE_ACCOUNT \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION" \
  --sku Standard_RAGRS \
  --kind StorageV2
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location $LOCATION `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

A continuación, obtenga la cadena de conexión de la cola.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
QUEUE_CONNECTION_STRING=`az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$QUEUE_CONNECTION_STRING=(az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"')
```

---

Ahora puede crear la cola de mensajes.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage queue create \
  --name "myqueue" \
  --account-name $STORAGE_ACCOUNT \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage queue create `
  --name "myqueue" `
  --account-name $STORAGE_ACCOUNT `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

Por último, puede enviar un mensaje a la cola.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage message put \
  --content "Hello Queue Reader App" \
  --queue-name "myqueue" \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage message put `
  --content "Hello Queue Reader App" `
  --queue-name "myqueue" `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

## <a name="deploy-the-background-application"></a>Implementación de la aplicación en segundo plano

Cree un archivo denominado *queue.json* y pegue dentro el código de configuración siguiente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "",
            "type": "String"
        },
        "queueconnection": {
            "defaultValue": "",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
    {
        "name": "queuereader",
        "type": "Microsoft.Web/containerApps",
        "apiVersion": "2021-03-01",
        "kind": "containerapp",
        "location": "[parameters('location')]",
        "properties": {
            "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
            "configuration": {
                "activeRevisionsMode": "single",
                "secrets": [
                {
                    "name": "queueconnection",
                    "value": "[parameters('queueconnection')]"
                }]
            },
            "template": {
                "containers": [
                    {
                        "image": "mcr.microsoft.com/azuredocs/containerapps-queuereader",
                        "name": "queuereader",
                        "env": [
                            {
                                "name": "QueueName",
                                "value": "myqueue"
                            },
                            {
                                "name": "QueueConnectionString",
                                "secretref": "queueconnection"
                            }
                        ]
                    }
                ],
                "scale": {
                    "minReplicas": 1,
                    "maxReplicas": 10,
                    "rules": [
                        {
                            "name": "myqueuerule",
                            "azureQueue": {
                                "queueName": "myqueue",
                                "queueLength": 100,
                                "auth": [
                                    {
                                        "secretRef": "queueconnection",
                                        "triggerParameter": "connection"
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    }]
}

```

Ahora puede crear e implementar la aplicación de contenedor.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az deployment group create --resource-group "$RESOURCE_GROUP" \
  --template-file ./queue.json \
  --parameters \
    environment_name="$CONTAINERAPPS_ENVIRONMENT" \
    queueconnection="$QUEUE_CONNECTION_STRING" \
    location="$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az deployment group create --resource-group "$RESOURCE_GROUP" `
  --template-file ./queue.json `
  --parameters `
    environment_name="$CONTAINERAPPS_ENVIRONMENT" `
    queueconnection="$QUEUE_CONNECTION_STRING" `
    location="$LOCATION"
```

---

Este comando implementa la aplicación de demostración desde la imagen de contenedor pública denominada `mcr.microsoft.com/azuredocs/containerapps-queuereader` y establece los secretos y las variables de entorno que usa la aplicación.

La aplicación escala verticalmente hasta 10 réplicas en función de la longitud de la cola, como se define en la sección `scale` de la plantilla de ARM.

## <a name="verify-the-result"></a>Comprobar el resultado

La aplicación de contenedor que se ejecuta como un proceso en segundo plano crea entradas de registros en Log Analytics a medida que llegan mensajes desde Azure Storage Queue. Es posible que tenga que esperar unos minutos para que el análisis llegue por primera vez antes de poder consultar los datos registrados.

Ejecute el comando siguiente para ver los mensajes registrados. Este comando necesita la extensión de Log Analytics, así que acepte el mensaje para instalar la extensión cuando se le solicite.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" `
  --out table
```

---

> [!TIP]
> ¿Tiene problemas? Háganoslo saber en GitHub abriendo una incidencia en el [repositorio de Azure Container Apps](https://github.com/microsoft/azure-container-apps).

## <a name="clean-up-resources"></a>Limpieza de recursos

Una vez que haya terminado, limpie los recursos de Container Apps mediante la ejecución del comando siguiente para eliminar el grupo de recursos.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az group delete `
  --resource-group $RESOURCE_GROUP
```

---

Este comando elimina todo el grupo de recursos, incluida la instancia de Container Apps, la cuenta de almacenamiento, el área de trabajo de Log Analytics y cualquier otro recurso del grupo de recursos.
