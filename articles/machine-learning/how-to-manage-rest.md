---
title: Uso de REST para administrar recursos de Machine Learning
titleSuffix: Azure Machine Learning
description: Cómo usar las API REST para crear, ejecutar y eliminar recursos de Azure Machine Learning (como un área de trabajo) o registrar modelos.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 08/10/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: e17f5e53a2ab58ec7fe8edbe1d2b7e64953cf689
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729811"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Creación, ejecución y eliminación de recursos de Azure Machine Learning mediante REST



Hay varias maneras de administrar los recursos de Azure Machine Learning. Puede usar el [portal](https://portal.azure.com/), la [interfaz de la línea de comandos](/cli/azure) o el [SDK de Python](/python/api/overview/azure/ml/intro). O bien, puede elegir la API REST. La API REST utiliza verbos HTTP de una manera estándar para crear, recuperar, actualizar y eliminar recursos. La API REST funciona con cualquier lenguaje o herramienta que pueda hacer solicitudes HTTP. La estructura sencilla de REST suele convertirla en una buena opción en entornos de scripting y en la automatización de MLOps. 

En este artículo aprenderá a:

> [!div class="checklist"]
> * Recuperar un token de autorización
> * Crear una solicitud de REST con formato correcto mediante la autenticación de entidad de servicio
> * Usar solicitudes GET para recuperar información acerca de los recursos jerárquicos de Azure Machine Learning
> * Usar solicitudes PUT y POST para crear y modificar recursos
> * Usar solicitudes DELETE para limpiar recursos 
> * Usar una autorización basada en claves para puntuar los modelos implementados

## <a name="prerequisites"></a>Prerequisites

- Una **suscripción de Azure** en la que tenga derechos administrativos Si no tiene este tipo de suscripción, pruebe con una [suscripción personal gratuita o de pago](https://azure.microsoft.com/free/)
- Un [área de trabajo de Azure Machine Learning](./how-to-manage-workspace.md)
- Las solicitudes administrativas de REST usan la autenticación de entidad de servicio. Siga los pasos que se indican en [Configuración de la autenticación para recursos y flujos de trabajo de Azure Machine Learning](./how-to-setup-authentication.md#service-principal-authentication) para crear una entidad de servicio en el área de trabajo.
- La utilidad **curl**. El programa **curl** está disponible en el [subsistema de Windows para Linux](/windows/wsl/install-win10) o en cualquier distribución de UNIX. En PowerShell, **curl** es un alias de **Invoke-WebRequest** y `curl -d "key=val" -X POST uri` se convierte en `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Recuperación de un token de autenticación de entidad de servicio

Las solicitudes REST administrativas se autentican con un flujo implícito de OAuth2. Este flujo de autenticación usa un token que proporciona la entidad de servicio de la suscripción. Para recuperar este token, necesitará:

- El identificador del inquilino (que identifica la organización a la que pertenece la suscripción)
- El identificador del cliente (que se asociará al token que se cree)
- El secreto de cliente (que debe proteger)

Debe tener estos valores de la respuesta a la creación de la entidad de servicio. En [Configuración de la autenticación para recursos y flujos de trabajo de Azure Machine Learning](./how-to-setup-authentication.md#service-principal-authentication) se aborda cómo obtener estos valores. Si usa la suscripción de la empresa, es posible que no tenga permiso para crear una entidad de servicio. En ese caso, debe usar una [suscripción personal gratuita o de pago](https://azure.microsoft.com/free/).

Para recuperar un token:

1. Abrir una ventana del terminal
1. En la línea de comandos, escriba el siguiente código.
1. Sustituya `<YOUR-TENANT-ID>`, `<YOUR-CLIENT-ID>` y `<YOUR-CLIENT-SECRET>` por sus propios valores. En este artículo, las cadenas entre corchetes angulares son variables que tendrá que reemplazar por los correspondientes valores propios.
1. Ejecute el comando

```bash
curl -X POST https://login.microsoftonline.com/<YOUR-TENANT-ID>/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=<YOUR-CLIENT-ID>&client_secret=<YOUR-CLIENT-SECRET>" \
```

La respuesta debe proporcionar un token de acceso válido durante una hora:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "YOUR-ACCESS-TOKEN"
}
```

Anote el token, ya que lo usará para autenticar todas las solicitudes administrativas adicionales. Para ello, debe establecer un encabezado de autorización en todas las solicitudes:

```bash
curl -h "Authorization:Bearer <YOUR-ACCESS-TOKEN>" ...more args...
```

> [!NOTE]
> El valor comienza por la cadena "Bearer ", que incluye un espacio simple antes del token que va a agregar.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Obtención de una lista de los grupos de recursos asociados a la suscripción

Para recuperar la lista de los grupos de recursos asociados a la suscripción, ejecute:

```bash
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups?api-version=2021-03-01-preview -H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

En Azure, se publican muchas API REST. Cada proveedor de servicios actualiza su API a su propio ritmo, pero lo hace sin interrumpir los programas existentes. El proveedor de servicios utiliza el argumento `api-version` para garantizar la compatibilidad. El argumento `api-version` varía de un servicio a otro. Para el servicio Machine Learning, por ejemplo, la versión actual de la API es `2021-03-01-preview`. Para las cuentas de almacenamiento es `2019-08-01`. Para los almacenes de claves, es `2019-09-01`. Todas las llamadas REST deben establecer el argumento `api-version` en el valor esperado. Puede confiar en la sintaxis y la semántica de la versión especificada aunque la API siga evolucionando. Si envía una solicitud a un proveedor sin el argumento `api-version`, la respuesta contendrá una lista de valores admitidos legible para el usuario. 

La llamada anterior dará como resultado una respuesta JSON compacta con el formato: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Exploración en profundidad de las áreas de trabajo y sus recursos

Para recuperar el conjunto de áreas de trabajo de un grupo de recursos, ejecute lo siguiente y sustituya `<YOUR-SUBSCRIPTION-ID>`, `<YOUR-RESOURCE-GROUP>` y `<YOUR-ACCESS-TOKEN>`: 

```
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

De nuevo, recibirá una lista JSON, una en la que cada elemento detalla un área de trabajo:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Para trabajar con los recursos de un área de trabajo, cambiará del servidor general **management.azure.com** a un servidor de API REST específico en la ubicación del área de trabajo. Observe el valor de la clave `discoveryUrl` en la respuesta JSON anterior. Si obtiene esa dirección URL, recibirá una respuesta similar a la siguiente:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

El valor de la respuesta `api` es la dirección URL del servidor que se va a utilizar para las solicitudes adicionales. Para enumerar los experimentos, por ejemplo, envíe el siguiente comando. Reemplace `REGIONAL-API-SERVER` por el valor de la respuesta de `api` (por ejemplo, `centralus.api.azureml.ms`). Reemplace también `YOUR-SUBSCRIPTION-ID`, `YOUR-RESOURCE-GROUP`, `YOUR-WORKSPACE-NAME` y `YOUR-ACCESS-TOKEN` como de costumbre:

```bash
curl https://<REGIONAL-API-SERVER>/history/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/experiments?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Del mismo modo, para recuperar modelos registrados en el área de trabajo, envíe:

```bash
curl https://<REGIONAL-API-SERVER>/modelmanagement/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/models?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Tenga en cuenta que para enumerar los experimentos, la ruta de acceso empieza por `history/v1.0` mientras que para enumerar los modelos, empieza por `modelmanagement/v1.0`. La API REST se divide en varios grupos operativos, cada uno con una ruta de acceso distinta. 

|Área|Path|
|-|-|
|Artefactos|/rest/api/azureml|
|Almacenes de datos|/azure/machine-learning/how-to-access-data|
|Ajuste de hiperparámetros|hyperdrive/v1.0/|
|Modelos|modelmanagement/v1.0/|
|Historial de ejecuciones|execution/v1.0/ y history/v1.0/|

Puede explorar la API REST mediante el patrón general de:

|Componente de dirección URL|Ejemplo|
|-|-|
| https://| |
| REGIONAL-API-SERVER/ | centralus.api.azureml.ms/ |
| operations-path/ | history/v1.0/ |
| subscriptions/ID_DE_LA_SUSCRIPCIÓN/ | subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/GRUPO_DE_RECURSOS/ | resourceGroups/MyResourceGroup/ |
| providers/operation-provider/ | providers/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | workspaces/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operations-endpoint/ | artifacts/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Creación y modificación de recursos mediante solicitudes PUT y POST

Además de la recuperación de recursos con el verbo GET, la API REST admite la creación de todos los recursos necesarios para entrenar, implementar y supervisar soluciones de Machine Learning. 

El entrenamiento y ejecución de modelos de Machine Learning requiere recursos de proceso. Puede enumerar los recursos de proceso de un área de trabajo con: 

```bash
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/computes?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Para crear o sobrescribir un recurso de proceso con nombre, se usará una solicitud PUT. A continuación, además de las sustituciones ya conocidas de `YOUR-SUBSCRIPTION-ID`, `YOUR-RESOURCE-GROUP`, `YOUR-WORKSPACE-NAME` y `YOUR-ACCESS-TOKEN`, sustituya `YOUR-COMPUTE-NAME` y los valores de `location`, `vmSize`, `vmPriority`, `scaleSettings`, `adminUserName` y `adminUserPassword`. Tal y como se especifica en la referencia de [Proceso de Machine Learning: Creación o actualización de la referencia del SDK](/rest/api/azureml/workspaces/createorupdate), el siguiente comando crea un recurso dedicado Standard_D1 de un solo nodo (un recurso de proceso de CPU básico) que se reducirá verticalmente después de 30 minutos:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/computes/<YOUR-COMPUTE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization:Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "eastus",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "<ADMIN_USERNAME>",
        "adminUserPassword": "<ADMIN_PASSWORD>"
    }
}'
```

> [!Note]
> En los terminales de Windows, es posible que tenga que usar los símbolos de comillas dobles como carácter de escape cuando envíe datos JSON. Es decir, texto como `"location"` se convierte en `\"location\"`. 

Una solicitud correcta obtendrá una respuesta `201 Created`, pero tenga en cuenta que esta respuesta simplemente significa que se ha iniciado el proceso de aprovisionamiento. Deberá sondear (o usar el portal) para confirmar su finalización correcta.

### <a name="train-a-model"></a>Entrenamiento de un modelo

Para entrenar un modelo mediante REST, vea [Entrenamiento de modelos con REST (versión preliminar)](how-to-train-with-rest.md). 

### <a name="delete-resources-you-no-longer-need"></a>Eliminación de los recursos que ya no necesita

Algunos de los recursos, no todos, admiten el verbo DELETE. Consulte la [referencia de la API](/rest/api/azureml/) antes de confirmar la eliminación de los casos de uso de la API REST. Para eliminar un modelo, por ejemplo, puede usar:

```bash
curl
  -X DELETE \
'https://<REGIONAL-API-SERVER>/modelmanagement/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/models/<YOUR-MODEL-ID>?api-version=2021-03-01-preview' \
  -H 'Authorization:Bearer <YOUR-ACCESS-TOKEN>' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Uso de REST para puntuar un modelo implementado

Para puntuar un modelo implementado mediante REST, vea [Consumo de un modelo de Azure Machine Learning que está implementado como un servicio web](how-to-consume-web-service.md).

## <a name="create-a-workspace-using-rest"></a>Creación de un área de trabajo mediante REST 

Cada área de trabajo de Azure Machine Learning tiene una dependencia en otros cuatro recursos de Azure: un registro de contenedor con administración habilitada, un almacén de claves, un recurso de Application Insights y una cuenta de almacenamiento. No se puede crear un área de trabajo hasta que no existan estos recursos. Consulte la referencia de la API REST para más información sobre la creación de cada uno de estos recursos.

Para crear un área de trabajo, realice una llamada similar a la siguiente a `management.azure.com`. Aunque esta llamada requiere que establezca un gran número de variables, es estructuralmente idéntica a otras llamadas que se han analizado en este artículo. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "AZURE-LOCATION>",
    "properties": {
        "friendlyName" : "<YOUR-WORKSPACE-FRIENDLY-NAME>",
        "description" : "<YOUR-WORKSPACE-DESCRIPTION>",
        "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
        keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
        "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
        "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Debe recibir una respuesta `202 Accepted` y, en los encabezados devueltos, un URI de `Location`. Puede obtener este URI para conseguir información sobre la implementación, incluida información útil de depuración si hay un problema con alguno de los recursos dependientes (por ejemplo, si ha olvidado habilitar el acceso de administrador en el registro de contenedor). 

## <a name="troubleshooting"></a>Solución de problemas

### <a name="resource-provider-errors"></a>Errores del proveedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Movimiento del área de trabajo

> [!WARNING]
> No se admite mover el área de trabajo de Azure Machine Learning a otra suscripción ni mover la suscripción propietaria a un nuevo inquilino. Si lo hace, pueden producirse errores.

### <a name="deleting-the-azure-container-registry"></a>Eliminación de la instancia de Azure Container Registry

El área de trabajo de Azure Machine Learning usa Azure Container Registry (ACR) para algunas operaciones. La primera vez que se necesite una instancia de ACR, se creará automáticamente.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Pasos siguientes

- Explore toda la [referencia de API REST de Azure Machine Learning](/rest/api/azureml/).
- Obtenga información sobre el uso del diseñador para la [predicción del precio de un automóvil con el diseñador](./tutorial-designer-automobile-price-train-score.md).
- Explore [Azure Machine Learning con cuadernos de Jupyter](..//machine-learning/samples-notebooks.md).
