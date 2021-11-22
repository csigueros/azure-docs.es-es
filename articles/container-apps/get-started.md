---
title: 'Inicio rápido: Implementar la primera aplicación de contenedor'
description: Implemente la primera aplicación en la versión preliminar de Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: bb2b24c080f7f8173280d5c31ca711d7241d04f1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552724"
---
# <a name="quickstart-deploy-your-first-container-app"></a>Inicio rápido: Implementar la primera aplicación de contenedor

La versión preliminar de Azure Container Apps permite ejecutar microservicios y aplicaciones contenedorizadas en una plataforma sin servidor. Con Container Apps, puede disfrutar de las ventajas de ejecutar contenedores y, al mismo tiempo, dejar atrás las preocupaciones de configurar manualmente la infraestructura en la nube y orquestadores de contenedores complejos.

En este inicio rápido, cree un entorno seguro de Container Apps e implemente su primera aplicación de contenedor.

## <a name="prerequisites"></a>Prerrequisitos

- Instale la [CLI de Azure](/cli/azure/install-azure-cli).

## <a name="setup"></a>Configurar

Comience iniciando sesión en Azure desde la CLI. Ejecute el siguiente comando y siga las indicaciones para completar el proceso de autenticación.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
```

---

A continuación, instale la extensión de Azure Container Apps en la CLI.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az extension add \
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
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

```azurecli
az provider register --namespace Microsoft.Web
```

---

A continuación, agregue estas variables de entorno:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
RESOURCE_GROUP="my-container-apps"
LOCATION="canadacentral"
LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
CONTAINERAPPS_ENVIRONMENT="my-environment"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-container-apps"
$LOCATION="canadacentral"
$LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
$CONTAINERAPPS_ENVIRONMENT="my-environment"
```

---

Con estas variables definidas, puede crear un grupo de recursos para organizar los servicios relacionados con la nueva aplicación de contenedor.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group create `
  --name $RESOURCE_GROUP `
  --location "$LOCATION"
```

---

Con la CLI actualizada y un nuevo grupo de recursos disponible, puede crear un entorno de Container Apps e implementar la aplicación de contenedor.

## <a name="create-an-environment"></a>Creación de un entorno

Un entorno de Azure Container Apps crea un límite seguro alrededor de un grupo de aplicaciones de contenedor. Las aplicaciones de contenedor implementadas en el mismo entorno se implementan en la misma red virtual y escriben registros en la misma área de trabajo de Log Analytics.

Azure Log Analytics se usa para supervisar la aplicación de contenedor necesaria al crear un entorno de Container Apps.

Cree un área de trabajo de Log Analytics con el siguiente comando:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

A continuación, recupere el identificador de cliente y el secreto de cliente de Log Analytics.

# <a name="bash"></a>[Bash](#tab/bash)

Asegúrese de ejecutar cada consulta por separado para que la solicitud se complete con tiempo suficiente.

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Asegúrese de ejecutar cada consulta por separado para que la solicitud se complete con tiempo suficiente.

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=(az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
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

```azurecli
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location "$LOCATION"
```

---

## <a name="create-a-container-app"></a>Crear una aplicación de contenedor

Ahora que ha creado un entorno, puede implementar la primera aplicación de contenedor. Usando el `containerapp create` comando, implemente una imagen de contenedor en Azure Container Instances.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name my-container-app \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
  --target-port 80 \
  --ingress 'external' \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name my-container-app `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest `
  --target-port 80 `
  --ingress 'external' `
  --query configuration.ingress.fqdn
```

---

Al establecer `--ingress` en `external`, la aplicación contenedora estará disponible para solicitudes públicas.

Aquí, el comando `create` devuelve el nombre de dominio completo de la aplicación contenedora. Copie esta ubicación en un explorador web y verá el mensaje siguiente.

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="Primera implementación de Azure Container Apps.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, puede eliminar la instancia de Azure Container Apps y todos los servicios asociados quitando el grupo de recursos.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --name $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
  --name $RESOURCE_GROUP
```

---

> [!TIP]
> ¿Tiene problemas? Háganoslo saber en GitHub abriendo una incidencia en el [repositorio de Azure Container Apps](https://github.com/microsoft/azure-container-apps).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Entornos en Azure Container Apps](environment.md)
