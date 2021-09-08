---
title: Autenticación solo de Azure Active Directory con Azure SQL
description: Este artículo proporciona información sobre cómo habilitar la característica de autenticación solo de Azure Active Directory (Azure AD) con Azure SQL Database y Azure SQL Managed Instance.
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 06/30/2021
ms.openlocfilehash: eaf4228dead01e970e25f7c8dc533bfbc06db802
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128523"
---
# <a name="azure-ad-only-authentication-with-azure-sql"></a>Autenticación solo de Azure AD con Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> La característica **Autenticación solo de Azure AD** que se describe en este artículo se encuentra en **versión preliminar pública**. 

La autenticación solo de Azure AD es una característica de [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md) que permite que el servicio solo admita la autenticación de Azure AD y se admite para [Azure SQL Database](sql-database-paas-overview.md) y [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md). La autenticación de SQL se deshabilita al habilitar la autenticación solo de Azure AD en el entorno de Azure SQL, incluidas las conexiones de administradores, inicios de sesión y usuarios de SQL Server. Solo los usuarios que usan la [autenticación de Azure AD](authentication-aad-overview.md) están autorizados para conectarse al servidor o la base de datos.

La autenticación solo de Azure AD se puede habilitar o deshabilitar mediante Azure Portal, la CLI de Azure, PowerShell o la API REST. La autenticación solo de Azure AD también se puede configurar durante la creación del servidor con una plantilla de ARM.

Para más información sobre la autenticación de Azure SQL, consulte [Autenticación y autorización](logins-create-manage.md#authentication-and-authorization).

> [!IMPORTANT]
> Actualmente, no puede administrar la autenticación solo de Azure AD en Azure Portal para Azure SQL Managed Instance. Para ver un tutorial sobre los distintos métodos para habilitar la autenticación solo de Azure AD, consulte [Tutorial: Habilitar la autenticación solo de Azure Active Directory con Azure SQL](authentication-azure-ad-only-authentication-tutorial.md).

## <a name="feature-description"></a>Descripción de la característica

Al habilitar la autenticación solo de Azure AD, se deshabilita la [autenticación de SQL](logins-create-manage.md#authentication-and-authorization) en el nivel de servidor y se impide cualquier autenticación basada en cualquier credencial de autenticación de SQL. Los usuarios con autenticación de SQL no podrán conectarse al servidor lógico de Azure SQL, incluidas todas sus bases de datos. Aunque se deshabilita la autenticación de SQL, se pueden crear nuevos inicios de sesión y usuarios de autenticación de SQL mediante cuentas de Azure AD con los permisos adecuados. Las cuentas con autenticación de SQL recién creadas no podrán conectarse al servidor. Al habilitar la autenticación de solo Azure AD no se quitan las cuentas de usuario y los inicios de sesión con autenticación de SQL existentes. La característica solo impide que estas cuentas se conecten al servidor y a cualquier base de datos creada para este servidor.

## <a name="permissions"></a>Permisos

Los usuarios de Azure AD pueden habilitar o deshabilitar la autenticación solo de Azure AD si son miembros de [roles integrados de Azure AD](../../role-based-access-control/built-in-roles.md) con privilegios elevados, como [propietarios](../../role-based-access-control/built-in-roles.md#owner), [colaboradores](../../role-based-access-control/built-in-roles.md#contributor) y [administradores globales](../../active-directory/roles/permissions-reference.md#global-administrator) de suscripciones de Azure. Además, el rol [Administrador de seguridad de SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) también puede habilitar o deshabilitar la característica de autenticación solo de Azure AD.

Los roles [Colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) y [Colaborador de SQL Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) no tendrán permisos para habilitar o deshabilitar la característica de autenticación solo de Azure AD. Esto es coherente con el enfoque de [separación de tareas](security-best-practice.md#implement-separation-of-duties), en el que los usuarios que pueden crear un servidor de Azure SQL o crear un administrador de Azure AD no pueden habilitar ni deshabilitar las características de seguridad.

### <a name="actions-required"></a>Acciones requeridas

Se han agregado las siguientes acciones al rol [Administrador de seguridad de SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) para permitir la administración de la característica de autenticación solo de Azure AD.

- Microsoft.Sql/servers/azureADOnlyAuthentications/*
- Microsoft.Sql/servers/administrators/read: solo es necesario para los usuarios que acceden al menú de **Azure Active Directory** de Azure Portal.
- Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*
- Microsoft.Sql/managedInstances/read

Las acciones anteriores también se pueden agregar a un rol personalizado para administrar la autenticación solo de Azure AD. Para obtener más información, vea [Creación y asignación de un rol personalizado en Azure Active Directory](../../active-directory/roles/custom-create.md).

## <a name="managing-azure-ad-only-authentication-using-apis"></a>Administración de la autenticación solo de Azure AD mediante las API

> [!IMPORTANT]
> Se debe establecer el administrador de Azure AD antes de habilitar la autenticación solo de Azure AD.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

La versión de la CLI de Azure debe ser la versión **2.14.2** o posterior.

`name` corresponde al prefijo del servidor o el nombre de instancia (por ejemplo, `myserver`), y `resource-group` corresponde al recurso al que pertenece el servidor (por ejemplo, `myresource`).

## <a name="azure-sql-database"></a>Azure SQL Database

Para más información, consulte [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth).

### <a name="enable-or-disable-in-sql-database"></a>Habilitación o deshabilitación en SQL Database

**Habilitación**

```azurecli
az sql server ad-only-auth enable --resource-group myresource --name myserver
```

**Deshabilitar**

```azurecli
az sql server ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-database"></a>Comprobación del estado en SQL Database

```azurecli
az sql server ad-only-auth get --resource-group myresource --name myserver
```

## <a name="azure-sql-managed-instance"></a>Instancia administrada de Azure SQL

Para más información, consulte [az sql mi ad-only-auth](/cli/azure/sql/mi/ad-only-auth).

**Habilitación**

```azurecli
az sql mi ad-only-auth enable --resource-group myresource --name myserver
```

**Deshabilitar**

```azurecli
az sql mi ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-managed-instance"></a>Comprobación del estado en SQL Managed Instance

```azurecli
az sql mi ad-only-auth get --resource-group myresource --name myserver
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se requiere el módulo [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) o posterior.

`ServerName` o `InstanceName` corresponden al prefijo del nombre del servidor (por ejemplo, `myserver` o `myinstance`), y `ResourceGroupName` corresponde al recurso al que pertenece el servidor (por ejemplo, `myresource`).

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="enable-or-disable-in-sql-database"></a>Habilitación o deshabilitación en SQL Database

**Habilitar**

Para más información, consulte [Enable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlserveractivedirectoryonlyauthentication). También puede ejecutar `get-help Enable-AzSqlServerActiveDirectoryOnlyAuthentication -full`.

```powershell
Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

Además, puede utilizar el siguiente comando:

```powershell
Get-AzSqlServer -ServerName myserver | Enable-AzSqlServerActiveDirectoryOnlyAuthentication
```

**Deshabilitar**

Para más información, consulte [Disable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlserveractivedirectoryonlyauthentication).

```powershell
Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-database"></a>Comprobación del estado en SQL Database

```powershell
Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName myserver -ResourceGroupName myresource
```

Además, puede utilizar el siguiente comando:

```powershell
Get-AzSqlServer -ServerName myserver | Get-AzSqlServerActiveDirectoryOnlyAuthentication
```

## <a name="azure-sql-managed-instance"></a>Instancia administrada de Azure SQL

### <a name="enable-or-disable-in-sql-managed-instance"></a>Habilitación o deshabilitación en SQL Managed Instance

**Habilitar**

Para más información, consulte [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication).

```powershell
Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

Además, puede utilizar el siguiente comando:

```powershell
Get-AzSqlInstance -InstanceName myinstance | Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

Para más información sobre estos comandos de PowerShell, ejecute `get-help  Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -full`.

**Deshabilitar**

Para más información, consulte [Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlinstanceactivedirectoryonlyauthentication).

```powershell
Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-managed-instance"></a>Comprobación del estado en SQL Managed Instance

```powershell
Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

Además, puede utilizar el siguiente comando:

```powershell
Get-AzSqlInstance -InstanceName myinstance | Get-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

# <a name="rest-api"></a>[REST API](#tab/rest-api)

Es necesario definir los parámetros siguientes:

- `<subscriptionId>` se puede encontrar en **Suscripciones** en Azure Portal.
- `<myserver>` corresponde al prefijo del servidor o el nombre de instancia (por ejemplo, `myserver`).
- `<myresource>` corresponde al recurso al que pertenece el servidor (por ejemplo, `myresource`).

Para usar la versión más reciente de MSAL, descárguela de https://www.powershellgallery.com/packages/MSAL.PS.

```rest
$subscriptionId = '<subscriptionId>'
$serverName = "<myserver>"
$resourceGroupName = "<myresource>"
```

## <a name="azure-sql-database"></a>Azure SQL Database

Para más información, consulte la documentación de la API REST sobre [Autenticaciones solo de Azure AD de servidor](/rest/api/sql/2021-02-01-preview/serverazureadonlyauthentications).

### <a name="enable-or-disable-in-sql-database"></a>Habilitación o deshabilitación en SQL Database

**Habilitación**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 1 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Deshabilitar**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 0 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-database"></a>Comprobación del estado en SQL Database

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

## <a name="azure-sql-managed-instance"></a>Instancia administrada de Azure SQL

Para más información, consulte la documentación de la API REST sobre [Autenticaciones solo de Azure AD de Managed Instance](/rest/api/sql/2021-02-01-preview/managedinstanceazureadonlyauthentications).

### <a name="enable-or-disable-in-sql-managed-instance"></a>Habilitación o deshabilitación en SQL Managed Instance

**Habilitación**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 1 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Deshabilitar**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 0 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-managed-instance"></a>Comprobación del estado en SQL Managed Instance

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[Plantilla de ARM](#tab/arm)

- Escriba el administrador de Azure AD de la implementación. Para encontrar el identificador del objeto de usuario, vaya a [Azure Portal](https://portal.azure.com) y vaya al recurso de **Azure Active Directory**. En **Administrar**, seleccione **Usuarios**. Busque el usuario que desea establecer como administrador de Azure AD para el servidor de Azure SQL. Seleccione el usuario y, en la página **Perfil**, verá el **identificador de objeto**.
- El identificador de inquilino se puede encontrar en la página **Información general** del recurso de **Azure Active Directory**.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="enable"></a>Habilitar

La siguiente plantilla de ARM habilita la autenticación solo de Azure AD en Azure SQL Database. Para deshabilitar la autenticación solo de Azure AD, establezca la propiedad `azureADOnlyAuthentication` en `false`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "sqlServer_name": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },        
        {
            "type": "Microsoft.Sql/servers/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/administrators', parameters('sqlServer_name'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}
```

Para más información, consulte [Microsoft.Sql servers/azureADOnlyAuthentications](/azure/templates/microsoft.sql/servers/azureadonlyauthentications).

## <a name="azure-sql-managed-instance"></a>Instancia administrada de Azure SQL

### <a name="enable"></a>Habilitar

La siguiente plantilla de ARM habilita la autenticación solo de Azure AD en Azure SQL Managed Instance. Para deshabilitar la autenticación solo de Azure AD, establezca la propiedad `azureADOnlyAuthentication` en `false`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/managedInstances/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/managedInstances/administrators', parameters('instance'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}

```

Para más información, consulte [Microsoft.Sql managedInstances/azureADOnlyAuthentications](/azure/templates/microsoft.sql/managedinstances/azureadonlyauthentications).

---

### <a name="checking-azure-ad-only-authentication-using-t-sql"></a>Comprobación de la autenticación solo de Azure AD mediante T-SQL

Se ha agregado [SEVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql) `IsExternalAuthenticationOnly` para comprobar si la autenticación solo de Azure AD está habilitada para el servidor o la instancia administrada. `1` indica que la característica está habilitada y `0` representa que la característica está deshabilitada.

```sql
SELECT SERVERPROPERTY('IsExternalAuthenticationOnly') 
```

## <a name="remarks"></a>Comentarios

- Un [Colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) puede establecer o quitar un administrador de Azure AD, pero no puede establecer la configuración de **autenticación solo de Azure Active Directory**. El [Administrador de seguridad de SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) no puede establecer o quitar un administrador de Azure AD, pero puede establecer la configuración de **autenticación solo de Azure Active Directory**. Solo las cuentas con roles de RBAC de Azure superiores o roles personalizados que contengan ambos permisos pueden establecer o quitar un administrador de Azure AD y establecer la configuración de **autenticación solo de Azure Active Directory**. Uno de estos roles es el rol [Colaborador](../../role-based-access-control/built-in-roles.md#contributor).
- Después de habilitar o deshabilitar la **autenticación solo de Azure Active Directory** en Azure Portal, se puede ver una entrada del **registro de actividad** en el menú del **servidor SQL**.
    :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal-sql-server-activity-log.png" alt-text="Entrada del registro de actividad en Azure Portal":::
- La **autenticación solo de Azure Active Directory** solo la pueden habilitar o deshabilitar los usuarios con los permisos adecuados si se especifica el **administrador de Azure Active Directory**. Si el administrador de Azure AD no está establecido, la configuración de la **autenticación solo de Azure Active Directory** permanece inactiva y no se puede habilitar ni deshabilitar. El uso de las API para habilitar la autenticación solo de Azure AD también producirá un error si no se ha establecido el administrador de Azure AD.
- El cambio de un administrador de Azure AD cuando está habilitada la autenticación solo de Azure AD se admite para los usuarios con los permisos adecuados.
- El cambio de un administrador de Azure AD y la habilitación o deshabilitación de la autenticación solo de Azure AD se permite en Azure Portal para los usuarios con los permisos adecuados. Ambas operaciones se pueden completar con una operación **Guardar** en Azure Portal. Se debe establecer el administrador de Azure AD para habilitar la autenticación solo de Azure AD.
- No se admite la eliminación de un administrador de Azure AD cuando la característica de autenticación solo de Azure AD está habilitada. El uso de una API para quitar un administrador de Azure AD no se realizará correctamente si la autenticación solo de Azure AD está habilitada.
    - Si la configuración de **autenticación solo de Azure Active Directory** está habilitada, el botón **Quitar administrador** está inactivo en Azure Portal.
- Se permite quitar un administrador de Azure AD y deshabilitar la configuración de **autenticación solo de Azure Active Directory**, pero requiere el permiso de usuario adecuado para completar las operaciones. Ambas operaciones se pueden completar con una operación **Guardar** en Azure Portal.
- Los usuarios de Azure AD con los permisos adecuados pueden suplantar a los usuarios de SQL existentes.
    - La suplantación sigue funcionando entre los usuarios con autenticación de SQL, incluso cuando la característica de autenticación solo de Azure AD está habilitada.

## <a name="known-issues"></a>Problemas conocidos

- Cuando la autenticación solo de Azure AD está habilitada, no se puede restablecer la contraseña del administrador del servidor. Actualmente, la operación de restablecer la contraseña se realiza correctamente en el portal, pero se produce un error en el motor de SQL. El error se indica en el registro de actividad del servidor. Para restablecer la contraseña del administrador del servidor, se debe deshabilitar la característica de autenticación solo de Azure AD.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Habilitar la autenticación solo de Azure Active Directory con Azure SQL](authentication-azure-ad-only-authentication-tutorial.md)

> [!div class="nextstepaction"]
> [Creación de un servidor con la autenticación solo de Azure AD habilitada en Azure SQL](authentication-azure-ad-only-authentication-create-server.md)