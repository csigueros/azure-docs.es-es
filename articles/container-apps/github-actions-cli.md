---
title: Publicación de revisiones con Acciones de GitHub en la versión preliminar de Azure Container Apps
description: Aprenda a crear revisiones automáticamente mediante Acciones de GitHub en la versión preliminar de Azure Container Apps
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.openlocfilehash: d83c6904f721d429488bca50359c3b32877ad50b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478253"
---
# <a name="publish-revisions-with-github-actions-in-azure-container-apps-preview"></a>Publicación de revisiones con Acciones de GitHub en la versión preliminar de Azure Container Apps

Azure Container Apps permite usar Acciones de GitHub para publicar [revisiones](revisions.md) en la aplicación de contenedor. A medida que se insertan confirmaciones en el repositorio de GitHub, se desencadena una acción de GitHub que actualiza la imagen de [contenedor](containers.md) en el registro de contenedor. Una vez que el contenedor se actualiza en el Registro, Azure Container Apps crea una revisión basada en la imagen de contenedor actualizada.

:::image type="content" source="media/github-actions/azure-container-apps-github-actions.png" alt-text="Los cambios realizados en un repositorio de GitHub desencadenan una acción para crear una revisión.":::

La acción de GitHub se desencadena como consecuencia de las confirmaciones en una rama específica del repositorio. Al crear el vínculo de integración, usted decide qué rama desencadena la acción.

## <a name="authentication"></a>Authentication

Al agregar o quitar una integración de Acciones de GitHub, puede autenticarse pasando un [token de acceso personal](https://docs.github.com/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) de GitHub o usando la experiencia interactiva de inicio de sesión de GitHub. La experiencia interactiva abre un formulario en el explorador web y le ofrece la oportunidad de iniciar sesión en GitHub. Una vez que se haya autenticado correctamente, se pasa de nuevo un token a la CLI que GitHub usará para el resto de la sesión actual.

- Para pasar un token de acceso personal, use el parámetro `--token` y proporcione un valor de token.
- Si decide usar el inicio de sesión interactivo, use el parámetro `--login-with-github` sin ningún valor.

## <a name="add"></a>Sumar

El comando `containerapp github-action add` crea una integración de Acciones de GitHub con la aplicación contenedora.

La primera vez que asocie Acciones de GitHub a la aplicación contenedora, deberá proporcionar un contexto de entidad de servicio. El comando siguiente le muestra cómo crear una entidad de servicio.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az ad sp create-for-rbac \
  --name <SERVICE_PRINCIPAL_NAME> \
  --role "contributor" \
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> /subscriptions/<SUBSCRIPTION_ID> \
  --sdk-auth
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az ad sp create-for-rbac `
  --name <SERVICE_PRINCIPAL_NAME> `
  --role "contributor" `
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> /subscriptions/<SUBSCRIPTION_ID> `
  --sdk-auth
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

El valor devuelto de este comando es una carga de JSON, que incluye los valores `tenantId`, `cliendId` y `clientSecret` de la entidad de servicio.

En el ejemplo siguiente se muestra cómo agregar una integración mientras se usa un token de acceso personal.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action add \
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" \
  --docker-file-path "./dockerfile" \
  --branch <BRANCH_NAME> \
  --registry-url <URL_TO_CONTAINER_REGISTRY> \
  --registry-user-name <REGISTRY_USER_NAME> \
  --registry-password <REGISTRY_PASSWORD> \
  --service-principal-client-id <CLIENT_ID> \
  --service-principal-client-secret <CLIENT_SECRET> \
  --service-principal-tenant-id <TENANT_ID> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action add `
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" `
  --docker-file-path "./dockerfile" `
  --branch <BRANCH_NAME> `
  --registry-url <URL_TO_CONTAINER_REGISTRY> `
  --registry-user-name <REGISTRY_USER_NAME> `
  --registry-password <REGISTRY_PASSWORD> `
  --service-principal-client-id <CLIENT_ID> `
  --service-principal-client-secret <CLIENT_SECRET> `
  --service-principal-tenant-id <TENANT_ID> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

## <a name="show"></a>Mostrar

El comando `containerapp github-action show` devuelve los valores de configuración de Acciones de GitHub para una aplicación contenedora.

En este ejemplo se muestra cómo agregar una integración mientras se usa el token de acceso personal.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME>
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

Este comando devuelve una carga de JSON con los valores de configuración de la integración de Acciones de GitHub.

## <a name="delete"></a>Eliminar

El comando `containerapp github-action remove` quita las Acciones de GitHub de la aplicación contenedora.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action remove \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action remove `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.
