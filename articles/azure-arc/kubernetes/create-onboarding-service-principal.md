---
title: Creación de una entidad de servicio de incorporación para Kubernetes habilitado para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: 'Creación de una entidad de servicio de incorporación habilitada para Azure Arc '
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: 5c102ce73672528810bbb35b2a74a5de2a3bf81e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056252"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Creación de una entidad de servicio de incorporación para Kubernetes habilitado para Azure Arc

## <a name="overview"></a>Información general

Los clústeres de Kubernetes se pueden conectar a Azure Arc mediante entidades de servicio con asignaciones de roles con privilegios limitados. Esta funcionalidad resulta útil en las canalizaciones de integración continua e implementación continua (CI/CD), como Azure Pipelines y las Acciones de GitHub.

Siga los pasos que se indican a continuación para aprender a usar entidades de servicio para conectar clústeres de Kubernetes a Azure Arc.

## <a name="create-a-new-service-principal"></a>Creación de una entidad de servicio

Cree una nueva entidad de servicio con un nombre informativo que sea único para su inquilino de Azure Active Directory.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Salida:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Asignación de permisos

Asigne el rol "Clúster de Kubernetes: incorporación de Azure Arc" a la entidad de seguridad recién creada. Se trata de un rol integrado de Azure con permisos limitados, que solo permite a la entidad de seguridad registrar clústeres en Azure. La entidad de seguridad con este rol asignado no puede actualizar, eliminar ni modificar ningún otro clúster o recurso de la suscripción.

Dado que las capacidades son limitadas, los clientes pueden volver a usar fácilmente esta entidad de seguridad para incorporar varios clústeres.

Los permisos se pueden limitar aún más si se pasa el argumento `--scope` adecuado al asignar el rol. Esto permite a los administradores restringir el registro de clústeres al ámbito de la suscripción o del grupo de recursos. Los distintos parámetros `--scope` admiten los siguientes escenarios:

| Resource  | Argumento `scope`| Efecto |
| ------------- | ------------- | ------------- |
| Subscription | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | La entidad de servicio puede registrar un clúster en cualquier grupo de recursos de la suscripción en que se encuentre. |
| Grupo de recursos | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | La entidad de servicio __solo__ puede registrar clústeres en el grupo de recursos `myGroup`. |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Salida:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Uso de una entidad de servicio con la CLI de Azure

Haga referencia a la entidad de servicio recién creada con los siguientes comandos:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Pasos siguientes

Controle la configuración del clúster [mediante Azure Policy](./use-azure-policy.md).
