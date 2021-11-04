---
title: 'Tutorial: Implementación de una aplicación Dapr en Azure Container Apps mediante la CLI de Azure'
description: Implementación de una aplicación Dapr en Azure Container Apps mediante la CLI de Azure.
services: app-service
author: asw101
ms.service: app-service
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: aawislan
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1fa71d13aa43d9fd681ab3eb6799734ea65c1482
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093051"
---
# <a name="tutorial-deploy-a-dapr-application-to-azure-container-apps-using-the-azure-cli"></a>Tutorial: Implementación de una aplicación Dapr en Azure Container Apps mediante la CLI de Azure

[Dapr](https://dapr.io/) (Distributed Application Runtime) es un entorno de ejecución que ayuda a crear microservicios resistentes, sin estado y con estado. En este tutorial, se implementa una aplicación Dapr de muestra en Azure Container Apps.

Aprenderá a:

> [!div class="checklist"]
> * Crear un entorno de Container Apps para las aplicaciones de contenedor
> * Crear un almacén de estado de Azure Blob Storage para la aplicación de contenedor
> * Implementar dos aplicaciones que producen y consumen mensajes y los conservan mediante el almacén de estado
> * Comprobar la interacción entre los dos microservicios.

Azure Container Apps ofrece una versión totalmente administrada de las API de Dapr al compilar microservicios. Al usar Dapr en Azure Container Apps, puede habilitar sidecars para que se ejecuten junto a los microservicios que proporcionan un amplio conjunto de funcionalidades. Las API de Dapr disponibles incluyen [llamadas de servicio a servicio](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/), [pub/sub](https://docs.dapr.io/developing-applications/building-blocks/pubsub/), [enlaces de eventos](https://docs.dapr.io/developing-applications/building-blocks/bindings/), [almacenes de estado](https://docs.dapr.io/developing-applications/building-blocks/state-management/) y [actores](https://docs.dapr.io/developing-applications/building-blocks/actors/).

En este tutorial, implementará las mismas aplicaciones desde el inicio rápido de Dapr [Hola mundo,](https://github.com/dapr/quickstarts/tree/master/hello-kubernetes) que consta de una aplicación cliente (Python) que genera mensajes y una aplicación de servicio (Node) que consume y conserva esos mensajes en un almacén de estado configurado. En el siguiente diagrama de arquitectura se muestran los componentes que conforman este tutorial:

:::image type="content" source="media/microservices-dapr/azure-container-apps-microservices-dapr.png" alt-text="Diagrama de arquitectura de Dapr Hola mundo microservicios en Azure Container Apps":::

## <a name="prerequisites"></a>Requisitos previos

* [CLI de Azure](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Antes de empezar

En esta guía se usan las siguientes variables de entorno:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
$STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

---

El fragmento de código anterior se puede usar para establecer las variables de entorno mediante bash, zsh o PowerShell.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<storage account name>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

Elija un nombre para `STORAGE_ACCOUNT`. Se creará en un paso posterior. Los nombres de las cuentas de almacenamiento deben ser *únicos en Azure*, deben tener entre 3 y 24 caracteres, y solo pueden incluir números y letras en minúscula.

## <a name="setup"></a>Configurar

Comience iniciando sesión en Azure desde la CLI.

Ejecute el siguiente comando y siga las indicaciones para completar el proceso de autenticación.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
```

---

Asegúrese de que ejecuta la versión más reciente de la CLI con el comando de actualización.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
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

```azurecli
az extension add `
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl 
```

---

Cree un grupo de recursos para organizar los servicios relacionados con la nueva aplicación de contenedor.

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

Los entornos de Azure Container Apps actúan como límites de aislamiento entre un grupo de aplicaciones de contenedor. Las aplicaciones de contenedor implementadas en el mismo entorno se implementan en la misma red virtual y escriben registros en la misma área de trabajo de Log Analytics.

Azure Log Analytics se usa para supervisar la aplicación de contenedor y es necesario al crear un entorno de Container Apps.

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

## <a name="set-up-a-state-store"></a>Configuración de un almacén de estado

### <a name="create-an-azure-blob-storage-account"></a>Creación de una cuenta de Azure Blob Storage

Use el siguiente comando para crear una nueva cuenta de Azure Storage.

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

```azurecli
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location "$LOCATION" `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

Una vez que se crea la cuenta de Azure Blob Storage, se necesitan los siguientes valores para los pasos posteriores de este tutorial.

* `storage_account_name` es el valor de la variable `STORAGE_ACCOUNT` que eligió anteriormente.

* `storage_container_name` es el valor de `STORAGE_ACCOUNT_CONTAINER` definido anteriormente (por ejemplo, `mycontainer`). Dapr crea un contenedor con este nombre si aún no existe en la cuenta de Azure Storage.

Obtenga la clave de la cuenta de almacenamiento con el siguiente comando.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT_KEY=`az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query '[0].value' --out tsv`

echo $STORAGE_ACCOUNT_KEY
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT_KEY=(az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query '[0].value' --out tsv)

echo $STORAGE_ACCOUNT_KEY
```

---

### <a name="configure-the-state-store-component"></a>Configuración del componente del almacén de estado

Con las propiedades de origen de los pasos anteriores, cree un archivo de configuración denominado *components.yaml*. Este archivo ayuda a permitir que la aplicación Dapr acceda al almacén de estado. En el ejemplo siguiente se muestra el aspecto que debe tener el archivo *components.yaml* cuando se configura para la cuenta de Azure Blob Storage:

```yaml
# components.yaml for Azure Blob storage component
- name: statestore
  type: state.azure.blobstorage
  version: v1
  metadata:
  # Note that in a production scenario, account keys and secrets 
  # should be securely stored. For more information, see
  # https://docs.dapr.io/operations/components/component-secrets
  - name: accountName
    value: <YOUR_STORAGE_ACCOUNT_NAME>
  - name: accountKey
    value: <YOUR_STORAGE_ACCOUNT_KEY>
  - name: containerName
    value: <YOUR_STORAGE_CONTAINER_NAME>
```

Para usar este archivo, asegúrese de reemplazar los valores del marcador de posición entre los corchetes `<>` por los suyos propios.

> [!NOTE]
> Container Apps no admite actualmente el [esquema nativo de componentes de Dapr](https://docs.dapr.io/operations/components/component-schema/). En el ejemplo anterior se usa el esquema admitido.
>
> En una aplicación de nivel de producción, siga las instrucciones de [administración de secretos](https://docs.dapr.io/operations/components/component-secrets) para administrar los secretos de forma segura.


## <a name="deploy-the-service-application-http-web-server"></a>Implementación de la aplicación de servicio (servidor web HTTP)

Vaya al directorio en el que ha almacenado el archivo *components.yaml* y ejecute el comando siguiente para implementar la aplicación de contenedor de servicios.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name nodeapp \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image dapriosamples/hello-k8s-node:latest \
  --target-port 3000 \
  --ingress 'external' \
  --min-replicas 1 \
  --max-replicas 1 \
  --enable-dapr \
  --dapr-app-port 3000 \
  --dapr-app-id nodeapp \
  --dapr-components ./components.yaml
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name nodeapp `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image dapriosamples/hello-k8s-node:latest `
  --target-port 3000 `
  --ingress 'external' `
  --min-replicas 1 `
  --max-replicas 1 `
  --enable-dapr `
  --dapr-app-port 3000 `
  --dapr-app-id nodeapp `
  --dapr-components ./components.yaml
```

---

Este comando implementa el servidor de aplicaciones de servicio (Node) en `--target-port 3000` (el puerto de la aplicación) junto con su sidecar de Dapr adjunto configurado con `--dapr-app-id nodeapp` y `--dapr-app-port 3000` para la detección e invocación del servicio. El almacén de estado se configura mediante `--dapr-components ./components.yaml`, lo que permite que el sidecar conserve el estado.


## <a name="deploy-the-client-application-headless-client"></a>Implementación de la aplicación cliente (cliente desatendido)

Ejecute el comando siguiente para implementar la aplicación de contenedor de cliente.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name pythonapp \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image dapriosamples/hello-k8s-python:latest \
  --min-replicas 1 \
  --max-replicas 1 \
  --enable-dapr \
  --dapr-app-id pythonapp
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name pythonapp `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image dapriosamples/hello-k8s-python:latest `
  --min-replicas 1 `
  --max-replicas 1 `
  --enable-dapr `
  --dapr-app-id pythonapp
```

---

Este comando implementa `pythonapp`, que también se ejecuta con un sidecar de Dapr que se usa para buscar y llamar de forma segura al sidecar de Dapr para `nodeapp`. Como esta aplicación está desatendida, no hay ningún `--target-port` que inicie un servidor ni es necesario habilitar la entrada.

## <a name="verify-the-result"></a>Comprobar el resultado

### <a name="confirm-successful-state-persistence"></a>Confirmación de la persistencia de estado correcta

Puede confirmar que los servicios funcionan correctamente mediante la visualización de datos en la cuenta de Azure Storage.

1. Abra [Azure Portal](https://portal.azure.com) en el navegador y vaya a la cuenta de almacenamiento.

1. Seleccione **Contenedores** en la parte izquierda.

1. Seleccione **mycontainer**.

1. Compruebe que puede ver el archivo denominado `order` en el contenedor.

1. Haga clic en el archivo.

1. Haga clic en la pestaña **Editar**.

1. Haga clic en el botón **Actualizar** para ver las actualizaciones.

### <a name="view-logs"></a>Visualización de registros

Los datos registrados mediante una aplicación de contenedor se almacenan en la tabla personalizada `ContainerAppConsoleLogs_CL` del área de trabajo de Log Analytics. Puede ver los registros mediante Azure Portal o con la CLI.

Use el siguiente comando de la CLI para ver los registros en la línea de comandos.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5" `
  --out table
```

---

En la salida siguiente, se muestra el tipo de respuesta que se espera del comando de la CLI.

```console
ContainerAppName_s    Log_s                            TableName      TimeGenerated
--------------------  -------------------------------  -------------  ------------------------
nodeapp               Got a new order! Order ID: 61    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Got a new order! Order ID: 62    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Got a new order! Order ID: 63    PrimaryResult  2021-10-22T22:45:44.618Z
```

> [!TIP]
> ¿Tiene problemas? Háganoslo saber en GitHub abriendo una incidencia en el [repositorio de Azure Container Apps](https://github.com/microsoft/azure-container-apps).

## <a name="clean-up-resources"></a>Limpieza de recursos

Una vez que haya terminado, limpie los recursos de la aplicación de contenedor mediante la ejecución del comando siguiente para eliminar el grupo de recursos.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
    --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
    --resource-group $RESOURCE_GROUP
```

---

Este comando elimina las dos aplicaciones de contenedor, la cuenta de almacenamiento, el entorno de aplicaciones de contenedor y cualquier otro recurso del grupo de recursos.

> [!NOTE]
> Puesto que `pythonapp` realiza llamadas continuas a `nodeapp` con mensajes que se conservan en el almacén de estado configurado, es importante completar estos pasos de limpieza para evitar las operaciones facturables en curso.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración del ciclo de vida de las aplicaciones](application-lifecycle-management.md)
