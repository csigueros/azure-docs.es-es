---
title: Configuración de permisos para restaurar una cuenta de Azure Cosmos DB
description: Aprenda a aislar y a restringir los permisos de restauración de una cuenta de copia de seguridad continua a un rol o una entidad de seguridad concretos. Aquí se muestra cómo asignar un rol integrado mediante Azure Portal, la CLI o la definición de un rol personalizado.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d566a2ee66df4adb810cb5908da3c47657fab418
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733183"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Administración de permisos para restaurar una cuenta de Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB permite aislar y restringir los permisos de restauración de una cuenta de copia de seguridad continua a un rol o una entidad de seguridad concretos. El propietario de la cuenta puede desencadenar una restauración y asignar un rol a otras entidades de seguridad para realizar la operación de restauración. Estos permisos se pueden aplicar en el ámbito de la suscripción o de forma más pormenorizada en el ámbito de la cuenta de origen, como se muestra en la siguiente imagen:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Lista de roles necesarios para realizar una operación de restauración." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

El ámbito es un conjunto de recursos con acceso; para obtener más información sobre los ámbitos, vea la documentación de [RBAC de Azure](../role-based-access-control/scope-overview.md). En Azure Cosmos DB, los ámbitos aplicables son la suscripción de origen y la cuenta de base de datos en la mayoría de los casos de uso. La entidad de seguridad que realiza las acciones de restauración debe tener permisos de escritura en el grupo de recursos de destino.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Asignación de roles para restaurar mediante Azure Portal

Para realizar una restauración, un usuario o una entidad de seguridad necesita el permiso para restaurar (es decir, permiso *restore/action*) y el permiso para aprovisionar una nueva cuenta (es decir, permiso *write*).  Para conceder estos permisos, el propietario puede asignar los roles integrados `CosmosRestoreOperator` y `Cosmos DB Operator` a una entidad de seguridad.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya a la suscripción y, en la pestaña **Control de acceso (IAM)** , seleccione **Agregar** > **Agregar asignación de roles**.

1. En el panel **Agregar asignación de roles**, en el campo **Rol**, seleccione el rol **CosmosRestoreOperator**. Seleccione **Usuario, grupo o entidad de servicio** en el campo **Asignar acceso a** y busque el nombre de un usuario o el identificador de correo electrónico, como se muestra en la siguiente imagen:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Asignación de los roles CosmosRestoreOperator y Operador de Cosmos DB." border="true":::

1. Seleccione **Guardar** para conceder el permiso *restore/action*.

1. Repita el paso 3 con el rol **Operador de Cosmos DB** para conceder el permiso de escritura. Al asignar este rol desde Azure Portal, se concede el permiso de restauración a toda la suscripción.

## <a name="permission-scopes"></a>Ámbitos de permiso

|Ámbito  |Ejemplo  |
|---------|---------|
|Suscripción | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Grupo de recursos | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Recurso de cuenta restaurable de CosmosDB | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

El recurso de cuenta restaurable se puede extraer de la salida del comando `az cosmosdb restorable-database-account list --name <accountname>` de la CLI o el cmdlet `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` de PowerShell. El atributo name de la salida representa al elemento `instanceID` de la cuenta restaurable. 

## <a name="permissions"></a>Permisos

Los siguientes permisos son necesarios para realizar las distintas actividades relativas a la restauración de cuentas en modo de copia de seguridad continua:

> [!NOTE]
> Los permisos se pueden asignar a una cuenta de base de datos restaurable en el ámbito de la cuenta o en el ámbito de la suscripción. No se admite la asignación de permisos en el ámbito del grupo de recursos.

|Permiso  |Impacto  |Ámbito mínimo  |Ámbito máximo  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | Estos permisos son necesarios para que la implementación de la plantilla de ARM cree la cuenta restaurada. Vea el permiso de ejemplo [RestorableAction](#custom-restorable-action) siguiente para aprender a establecer este rol. | No aplicable | No aplicable  |
|`Microsoft.DocumentDB/databaseAccounts/write` | Este permiso es necesario para restaurar una cuenta en un grupo de recursos. | Grupo de recursos en el que se crea la cuenta restaurada. | Suscripción en la que se crea la cuenta restaurada. |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` </br> No puede elegir el grupo de recursos como ámbito de permiso. |Este permiso es necesario en el ámbito de la cuenta de base de datos restaurable de origen para permitir la realización de acciones de restauración en él.  | Recurso *RestorableDatabaseAccount* perteneciente a la cuenta de origen que se va a restaurar. Este valor también lo proporciona la propiedad `ID` del recurso de cuenta de base de datos restaurable. Un ejemplo de cuenta restaurable es */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/\<guid-instanceid\>* | Suscripción que contiene la cuenta de base de datos restaurable.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` </br> No puede elegir el grupo de recursos como ámbito de permiso. |Este permiso es necesario en el ámbito de la cuenta de base de datos restaurable de origen para indicar las cuentas de base de datos que se pueden restaurar.  | Recurso *RestorableDatabaseAccount* perteneciente a la cuenta de origen que se va a restaurar. Este valor también lo proporciona la propiedad `ID` del recurso de cuenta de base de datos restaurable. Un ejemplo de cuenta restaurable es */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/\<guid-instanceid\>*| Suscripción que contiene la cuenta de base de datos restaurable. |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` </br> No puede elegir el grupo de recursos como ámbito de permiso. | Este permiso es necesario en el ámbito de la cuenta restaurable de origen para permitir la lectura de recursos restaurables, como listas de bases de datos y contenedores de una cuenta restaurable.  | Recurso *RestorableDatabaseAccount* perteneciente a la cuenta de origen que se va a restaurar. Este valor también lo proporciona la propiedad `ID` del recurso de cuenta de base de datos restaurable. Un ejemplo de cuenta restaurable es */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/\<guid-instanceid\>*| Suscripción que contiene la cuenta de base de datos restaurable. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Escenarios de asignación de roles de la CLI de Azure para restaurar en ámbitos diferentes

Los roles con permiso se pueden asignar a distintos ámbitos para lograr un control pormenorizado sobre quién puede realizar la operación de restauración en una suscripción o una cuenta determinada.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Asignación de la capacidad de restaurar desde cualquier cuenta restaurable de una suscripción

Asigne el rol integrado `CosmosRestoreOperator` en el nivel de suscripción.

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Asignación de la capacidad de restaurar desde una cuenta determinada

* Asigne una acción de escritura de usuario en el grupo de recursos específico. Esta acción es necesaria para crear una cuenta nueva en el grupo de recursos.

* Asigne el rol integrado *CosmosRestoreOperator* a la cuenta de base de datos restaurable específica que debe restaurarse. En el siguiente comando, el ámbito de *RestorableDatabaseAccount* se recupera de la propiedad `ID` de la salida de `az cosmosdb restorable-database-account` (si se usa la CLI) o `Get-AzCosmosDBRestorableDatabaseAccount` (si se usa PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Asignación de la capacidad de restaurar desde cualquier cuenta de origen de un grupo de recursos
Esta operación no se admite actualmente.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>Creación de roles personalizados para la acción de restauración con la CLI

El propietario de la suscripción puede proporcionar el permiso para restaurar a cualquier otra identidad de Azure AD. El permiso de restauración se basa en la acción: `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action`, y debe incluirse en el permiso de restauración. Hay un rol integrado denominado *CosmosRestoreOperator* que tiene este rol incluido. Puede asignar el permiso mediante este rol integrado o crear uno personalizado.

El elemento RestorableAction siguiente representa un rol personalizado. Este rol debe crearse de manera explícita. La siguiente plantilla JSON crea un rol personalizado *RestorableAction* con permiso de restauración:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Luego, use el siguiente comando de implementación de plantillas para crear un rol con permiso de restauración mediante la plantilla de ARM:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Pasos siguientes

* Aprovisione la copia de seguridad continua mediante [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), la [CLI](provision-account-continuous-backup.md#provision-cli) o [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* Restaure una cuenta mediante [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), la [CLI](restore-account-continuous-backup.md#restore-account-cli) o [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Realice la migración a una cuenta desde una copia de seguridad periódica a una copia de seguridad continua](migrate-continuous-backup.md).
* [Modelo de recursos del modo de copia de seguridad continua](continuous-backup-restore-resource-model.md)
