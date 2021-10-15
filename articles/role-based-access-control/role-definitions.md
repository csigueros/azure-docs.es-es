---
title: 'Descripción de las definiciones de roles de Azure: RBAC de Azure'
description: Obtenga información acerca de las definiciones de roles de Azure en el control de acceso basado en roles de Azure (RBAC de Azure) para la administración de acceso específico de recursos de Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/28/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: c68849dcb3c0c5683bfc160a0b4a1cb03ae0e13a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356040"
---
# <a name="understand-azure-role-definitions"></a>Descripción de las definiciones de roles de Azure

Tanto si quiere comprender el funcionamiento de un rol de Azure como si va a crear su propio [rol de Azure personalizado](custom-roles.md), le resultará útil entender cómo se definen los roles. En este artículo se describen los detalles de las definiciones de roles y se proporcionan algunos ejemplos.

## <a name="role-definition"></a>Definición de roles

Una *definición de roles* es una recopilación de permisos. A veces, se denomina *rol* simplemente. Una definición de roles enumera las acciones que se pueden realizar; por ejemplo, de lectura, escritura y eliminación. También puede enumerar las acciones excluidas de las acciones permitidas o las acciones relacionadas con datos subyacentes.

A continuación se muestra un ejemplo de las propiedades de una definición de roles cuando se muestran mediante Azure PowerShell:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

A continuación se muestra un ejemplo de las propiedades de una definición de roles cuando se muestran mediante Azure Portal, la CLI de Azure o la API de REST:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

En la tabla siguiente se describe el significado de las propiedades de roles.

| Propiedad | Descripción |
| --- | --- |
| `Name`</br>`roleName` | Nombre para mostrar del rol. |
| `Id`</br>`name` | Identificador único del rol. Los roles integrados tienen el mismo id. de rol en todas las nubes. |
| `IsCustom`</br>`roleType` | Indica si es un rol personalizado. Se establece en `true` o `CustomRole` para los roles personalizados. Se establece en `false` o `BuiltInRole` para los roles integrados. |
| `Description`</br>`description` | Descripción del rol. |
| `Actions`</br>`actions` | Matriz de cadenas que especifica las acciones del plano de control que el rol permite realizar. |
| `NotActions`</br>`notActions` | Matriz de cadenas que especifica las acciones del plano de control que se excluyen de la propiedad `Actions` permitida. |
| `DataActions`</br>`dataActions` | Matriz de cadenas que especifica las acciones del plano de datos que el rol permite realizar en los datos dentro de ese objeto. |
| `NotDataActions`</br>`notDataActions` | Matriz de cadenas que especifica las acciones del plano de datos que se excluyen de la propiedad `DataActions` permitida. |
| `AssignableScopes`</br>`assignableScopes` | Matriz de cadenas que especifica los ámbitos en los que el rol está disponible para la asignación. |

### <a name="actions-format"></a>Formato de acciones

Las acciones se especifican con cadenas que tienen el siguiente formato:

- `{Company}.{ProviderName}/{resourceType}/{action}`

La parte `{action}` de una cadena de acción especifica el tipo de acciones que puede realizar en un tipo de recurso. Por ejemplo, verá las siguientes subcadenas en `{action}`:

| Subcadena de acción    | Descripción         |
| ------------------- | ------------------- |
| `*` | El carácter comodín concede acceso a todas las operaciones que coinciden con la cadena. |
| `read` | Permite acciones de lectura (GET). |
| `write` | Permite acciones de escritura (PUT o PATCH). |
| `action` | Permite acciones personalizadas, como reiniciar máquinas virtuales (POST). |
| `delete` | Permite acciones de eliminación (DELETE). |

### <a name="role-definition-example"></a>Ejemplo de definición de roles

Esta es la definición del rol [Colaborador](built-in-roles.md#contributor) como se muestra en Azure PowerShell y la CLI de Azure. Las acciones del carácter comodín (`*`) en `Actions` indican que la entidad de seguridad asignada a este rol puede realizar todas las acciones o, en otras palabras, administrar todo el contenido. Esto incluye las acciones definidas en el futuro, a medida que Azure agregue nuevos tipos de recursos. Las operaciones de `NotActions` se restan de `Actions`. En el caso del rol [Colaborador](built-in-roles.md#contributor), `NotActions` le quita la capacidad para administrar el acceso a los recursos, así como de administrar las asignaciones de Azure Blueprint.

Rol Colaborador tal como se muestra en Azure PowerShell:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rol Colaborador tal como se muestra en la CLI de Azure:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="control-and-data-actions"></a>Acciones de control y datos

El control de acceso basado en rol para las acciones del plano de datos se especifica en las propiedades `Actions` y `NotActions` de una definición de rol. Estos son algunos ejemplos de acciones del plano de control en Azure:

- Administrar el acceso a una cuenta de almacenamiento
- Crear, actualizar o eliminar un contenedor de blobs
- Eliminar un grupo de recursos y todos sus recursos

El acceso del plano de control no se hereda al plano de datos, dado que el método de autenticación de contenedor se establece en "Cuenta de usuario de Azure AD", no en "Clave de acceso". Esta separación impide que los roles con caracteres comodín (`*`) tengan acceso no restringido a los datos. Por ejemplo, si un usuario tiene el rol [Lector](built-in-roles.md#reader) en una suscripción, podrá ver la cuenta de almacenamiento, pero, de forma predeterminada, no podrá ver los datos subyacentes.

Anteriormente, el control de acceso basado en rol no se usaba para acciones de datos. La autorización para las acciones de datos variaba de un proveedor de recursos a otro. El mismo modelo de autorización de control de acceso basado en rol que se usa para las acciones del plano de control se ha ampliado a las acciones del plano de datos.

Para admitir las acciones del plano de datos, se han agregado nuevas propiedades de datos a la definición de roles. Las acciones del plano de datos se especifican en las propiedades `DataActions` y `NotDataActions`. Al agregar estas propiedades de datos, se mantiene la separación entre el plano de control y el plano de datos. Esto impide que las asignaciones de roles actuales con caracteres comodín (`*`) de repente tengan acceso a los datos. Estas son algunas acciones del plano de datos que se pueden especificar en `DataActions` y `NotDataActions`:

- Leer una lista de blobs en un contenedor
- Escribir un blob de almacenamiento en un contenedor
- Eliminar un mensaje de una cola

Esta es la definición del rol [Lector de datos de blobs de almacenamiento](built-in-roles.md#storage-blob-data-reader), que incluye operaciones en las propiedades `Actions` y `DataActions`. Esta función permite leer el contenedor de blobs y también los datos de blob subyacentes.

Rol Lector de datos de Storage Blob tal como se muestra en Azure PowerShell:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rol Lector de datos de Storage Blob tal como se muestra en la CLI de Azure:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Solo se pueden agregar acciones del plano de datos a las propiedades `DataActions` y `NotDataActions`. Los proveedores de recursos identifican qué acciones son acciones de datos, para lo que establecen la propiedad `isDataAction` en `true`. Para ver una lista de las acciones en las que `isDataAction` es `true`, consulte [Operaciones del proveedor de recursos de Azure](resource-provider-operations.md). Los roles que no tienen acciones de datos no necesitan las propiedades `DataActions` y `NotDataActions` en la definición de roles.

Azure Resource Manager controla la autorización de todas las llamadas API del plano de control. Un proveedor de recursos o Azure Resource Manager controlan la autorización de las llamadas API del plano de datos.

### <a name="data-actions-example"></a>Ejemplo de acciones de datos

Para conocer mejor el funcionamiento de las acciones del plano de control y del plano de datos, veamos un ejemplo concreto. A Alice se le ha asignado el rol [Propietario](built-in-roles.md#owner) en el ámbito de la suscripción. A Bob se le ha asignado el rol [Colaborador de datos de blobs de almacenamiento](built-in-roles.md#storage-blob-data-contributor) en un ámbito de la cuenta de almacenamiento. En el siguiente diagrama se muestra este ejemplo.

![El control de acceso basado en rol se ha ampliado para admitir acciones tanto del plano de control como del plano de datos](./media/role-definitions/rbac-data-plane.png)

El rol [Propietario](built-in-roles.md#owner) de Alice y el rol [Colaborador de datos de blobs de almacenamiento](built-in-roles.md#storage-blob-data-contributor) de Bob tienen las siguientes acciones:

Propietario

&nbsp;&nbsp;&nbsp;&nbsp;Acciones<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Colaborador de datos de blobs de almacenamiento

&nbsp;&nbsp;&nbsp;&nbsp;Acciones<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Puesto que Alice tiene una acción de carácter comodín (`*`) en un ámbito de suscripción, sus permisos se heredan para poder realizar todas las acciones del plano de control. Alice puede leer, escribir y eliminar contenedores. Sin embargo, Alice no puede realizar acciones del plano de datos sin pasos adicionales. Por ejemplo, de forma predeterminada, Alice no puede leer los blobs de un contenedor. Para leer los blobs, Alice tendría que recuperar las claves de acceso de almacenamiento y usarlas para acceder a los blobs.

Los permisos de Bob se limitan a solo los valores de `Actions` y `DataActions` especificados en el rol [Colaborador de datos de blobs de almacenamiento](built-in-roles.md#storage-blob-data-contributor). Según su rol, Bob puede realizar acciones tanto del plano de control como del plano de datos Por ejemplo, Bob puede leer, escribir y eliminar contenedores de la cuenta de almacenamiento especificada y también puede leer, escribir y eliminar los blobs.

Para más información acerca de la seguridad de los planos de control y de datos para el almacenamiento, consulte la [guía de seguridad de Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-actions"></a>¿Qué herramientas admiten el uso de roles de Azure en las acciones de datos?

Para visualizar y trabajar con acciones de datos, debe tener las versiones correctas de las herramientas o de los SDK:

| Herramienta  | Versión  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 o posterior |
| [CLI de Azure](/cli/azure/install-azure-cli) | 2.0.30 o posterior |
| [Azure para .NET](/dotnet/azure/) | 2.8.0-versión preliminar o posterior |
| [Azure SDK para Go](/azure/go/azure-sdk-go-install) | 15.0.0 o posterior |
| [Azure para Java](/java/azure/) | 1.9.0 o posterior |
| [Azure para Python](/azure/python/) | 0.40.0 o posterior |
| [SDK de Azure para Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 o posterior |

Para ver y usar las acciones de datos en la API REST, el valor del parámetro **api-version** debe ser la siguiente versión, o las versiones posteriores:

- 2018-07-01

## <a name="actions"></a>Acciones

El permiso `Actions` especifica las acciones del plano de control que el rol permite realizar. Se trata de una colección de cadenas que identifican acciones protegibles de proveedores de recursos de Azure. Estos son algunos ejemplos de acciones del plano de control que se pueden usar en `Actions`.

> [!div class="mx-tableFixed"]
> | Cadena de acción    | Descripción         |
> | ------------------- | ------------------- |
> | `*/read` | Concede acceso a las acciones de lectura a todos los tipos de recursos de todos los proveedores de recursos de Azure.|
> | `Microsoft.Compute/*` | Concede acceso a todas las acciones a todos los tipos de recursos del proveedor de recursos Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Concede acceso a las acciones de lectura a todos los tipos de recursos del proveedor de recursos Microsoft.Network.|
> | `Microsoft.Compute/virtualMachines/*` | Concede acceso a todas las acciones de las máquinas virtuales y a sus tipos de recursos secundarios.|
> | `microsoft.web/sites/restart/Action` | Concede acceso para reiniciar una aplicación web.|

## <a name="notactions"></a>NotActions

El permiso `NotActions` especifica las acciones del plano de control que se restan o excluyen de la `Actions` permitida que tienen un carácter comodín (`*`). Use el permiso `NotActions` si el conjunto de acciones que quiere permitir se define más fácilmente mediante la resta de los `Actions` que tienen un carácter comodín (`*`). El acceso concedido por un rol (permisos vigentes) se calcula restando las acciones de `NotActions` de las de `Actions`.

`Actions - NotActions = Effective control plane permissions`

En la tabla siguiente se muestran dos ejemplos de los permisos del plano de control efectivos para una acción con carácter comodín de [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement):

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Permisos del plano de control efectivos |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *Ninguna* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Si un usuario tiene asignado un rol que excluye una acción en `NotActions` y se le asigna un segundo rol que sí que concede acceso a esa acción, el usuario puede realizar dicha acción. `NotActions` no es una regla de denegación, es simplemente una manera cómoda de crear un conjunto de acciones permitidas cuando es necesario excluir acciones específicas.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>Diferencias entre NotActions y asignaciones de denegación

`NotActions` y las asignaciones de denegación no son iguales y tienen diferentes fines. `NotActions` son una manera práctica de restar acciones específicas de una acción con un carácter comodín (`*`).

Las asignaciones de denegación impiden que los usuarios realicen acciones concretas, aunque una asignación de roles les conceda acceso. Para más información, consulte [Descripción de las asignaciones de denegación de Azure](deny-assignments.md).

## <a name="dataactions"></a>DataActions

El permiso `DataActions` especifica las acciones del plano de datos que el rol permite realizar en los datos dentro de ese objeto. Por ejemplo, si un usuario tiene acceso para leer datos de blobs de una cuenta de almacenamiento, puede leer los blobs en esa cuenta de almacenamiento. A continuación, se incluyen algunos ejemplos de acciones de datos que se pueden usar en `DataActions`.

> [!div class="mx-tableFixed"]
> | Cadena de acción de datos    | Descripción         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Devuelve un blob o una lista de blobs. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Devuelve el resultado de la escritura de un blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Devuelve un mensaje. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Devuelve un mensaje o el resultado de la escritura o eliminación de un mensaje. |

## <a name="notdataactions"></a>NotDataActions

El permiso `NotDataActions` especifica las operaciones de datos que se restan o excluyen de la `DataActions` permitida que tienen un carácter comodín (`*`). Use el permiso `NotDataActions` si el conjunto de acciones que quiere permitir se define más fácilmente mediante la resta de los `DataActions` que tienen un carácter comodín (`*`). El acceso concedido por un rol (permisos vigentes) se calcula restando las acciones de `NotDataActions` de las de `DataActions`. Cada proveedor de recursos proporciona su conjunto respectivo de API para completar las acciones de datos.

`DataActions - NotDataActions = Effective data plane permissions`

En la tabla siguiente se muestran dos ejemplos de los permisos del plano de datos efectivos para una acción con carácter comodín de [Microsoft.Storage](resource-provider-operations.md#microsoftstorage):

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | Permisos del plano de datos de efectivos |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *Ninguna* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Si un usuario tiene asignado un rol que excluye una acción de datos en `NotDataActions` y se le asigna un segundo rol que sí que concede acceso a esa operación de datos, el usuario puede realizar dicha acción de datos. `NotDataActions` no es una regla de denegación, es simplemente una manera cómoda de crear un conjunto de acciones de datos permitidas cuando es necesario excluir acciones de datos específicas.
>

## <a name="assignablescopes"></a>Ámbitos asignables

La propiedad `AssignableScopes` especifica los ámbitos (grupos de administración, suscripciones o grupos de recursos) en los que se puede asignar la definición de rol. Puede hacer que el rol esté disponible para su asignación solo en los grupos de administración, las suscripciones o los grupos de recursos que lo necesiten. Hay que usar al menos un grupo de administración, una suscripción o un grupo de recursos.

Los roles integrados tienen `AssignableScopes` establecido en el ámbito raíz (`"/"`). El ámbito raíz indica que el rol está disponible para la asignación en todos los ámbitos. Ejemplos de ámbitos asignables válidos son:

> [!div class="mx-tableFixed"]
> | Rol disponible para su asignación | Ejemplo |
> |----------|---------|
> | Una suscripción | `"/subscriptions/{subscriptionId1}"` |
> | Dos suscripciones | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Grupo de recursos de red | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Un grupo de administración | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Grupo de administración y suscripción | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Todos los ámbitos (se aplica solo a los roles integrados) | `"/"` |

Para más información acerca de `AssignableScopes` para roles personalizados, consulte [Roles personalizados en los recursos de Azure](custom-roles.md).

## <a name="next-steps"></a>Pasos siguientes

* [Roles integrados en los recursos de Azure](built-in-roles.md)
* [Roles personalizados en los recursos de Azure](custom-roles.md)
* [Operaciones del proveedor de recursos de Azure](resource-provider-operations.md)
