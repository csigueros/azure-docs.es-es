---
title: Administración de revisiones en la versión preliminar de Azure Container Apps
description: Administre las revisiones y la división del tráfico en Azure Container Apps.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5f90deaa5e3b8e2aaf067f218638390a089142b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093601"
---
# <a name="manage-revisions-azure-container-apps-preview"></a>Administración de revisiones en la versión preliminar de Azure Container Apps

Admitir varias revisiones en Azure Container Apps le permite administrar el control de versiones y la cantidad de [tráfico que se envía a cada revisión](#traffic-splitting). Use los siguientes comandos para controlar cómo administra las revisiones la instancia de Container Apps.

## <a name="list"></a>List

Enumera todas las revisiones asociadas a la instancia de Container Apps con `az containerapp revision list`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision list \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  -o table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp revision list `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  -o table
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

## <a name="show"></a>Mostrar

Muestra detalles sobre una revisión específica mediante `az containerapp revision show`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision show \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp revision show `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

## <a name="update"></a>Actualizar

Para actualizar una instancia de Container Apps, use `az containerapp update`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp update \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp update `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

## <a name="activate"></a>Activar

Activa una revisión mediante `az containerapp revision activate`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision activate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```poweshell
az containerapp revision activate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

## <a name="deactivate"></a>Desactivación

Desactiva las revisiones que ya no están en uso con `az container app revision deactivate`. La desactivación detiene todas las réplicas en ejecución de una revisión.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision deactivate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp revision deactivate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

## <a name="restart"></a>Reinicio

Todas las revisiones de Container Apps existentes no tendrán acceso a este secreto hasta que se reinicien.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision restart \
  --name <REVISION_NAME> \
  --app <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp revision restart `
  --name <REVISION_NAME> `
  --app <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

A medida que interactúe con este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

## <a name="set-active-revision-mode"></a>Establecimiento del modo de revisión activo

Configure si la instancia de Container Apps admite o no varias revisiones activas.

La propiedad `activeRevisionsMode` acepta dos valores:

- `multiple`: configura la instancia de Container Apps para permitir más de una revisión activa.

- `single`: desactiva automáticamente todas las demás revisiones cuando se activa una revisión. La habilitación del modo `single` hace que, cuando se crea un cambio del ámbito de revisión y se crea una nueva revisión, todas las demás revisiones se desactivan automáticamente.

```json
{
  ...
  "resources": [
  {
    ...
    "properties": {
        "configuration": {
          "activeRevisionsMode": "multiple"
      }
    }
  }]
}
```

El siguiente fragmento de configuración muestra cómo establecer la propiedad `activeRevisionsMode`. Los cambios realizados en esta propiedad requieren el contexto de la plantilla completa de ARM de la instancia de Container Apps.

## <a name="traffic-splitting"></a>División del tráfico

Se aplica mediante la asignación de valores de porcentaje, con los cuales puede decidir cómo equilibrar el tráfico entre diferentes revisiones. Las reglas de división de tráfico se asignan estableciendo ponderaciones a distintas revisiones.

En el ejemplo siguiente se muestra cómo dividir el tráfico entre tres revisiones.

```json
{
  ...
  "configuration": {
    "ingress": {
      "traffic": [
        {
          "revisionName": <REVISION1_NAME>,
          "weight": 50
        },
        {
          "revisionName": <REVISION2_NAME>,
          "weight": 30
        },
        {
          "latestRevision": true,
          "weight": 20
        }
      ]
    }
  }
}
```

Cada revisión obtiene el tráfico en función de las reglas siguientes:

- El 50 % de las solicitudes van a REVISION1
- El 30 % de las solicitudes van a REVISION2
- El 20 % de las solicitudes van a la última revisión

El total de la suma de todos los porcentajes de revisión debe ser igual a 100.

En este ejemplo, reemplace los marcadores de posición `<REVISION*_NAME>` por los nombres de las revisiones en la instancia de Container Apps. Puede acceder a los nombres de las revisiones mediante el comando [list](#list).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción](get-started.md)
