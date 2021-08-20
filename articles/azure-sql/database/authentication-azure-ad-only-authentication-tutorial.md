---
title: Habilitar la autenticación solo de Azure Active Directory con Azure SQL
description: Este artículo explica cómo habilitar la característica de autenticación solo de Azure Active Directory (Azure AD) con Azure SQL Database y Azure SQL Managed Instance.
ms.service: sql-db-mi
ms.subservice: security
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 06/30/2021
ms.openlocfilehash: 6ea0da3b8a6ddcc2f41bd9e779742f3e2d9cbe4d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114470770"
---
# <a name="tutorial-enable-azure-active-directory-only-authentication-with-azure-sql"></a>Tutorial: Habilitar la autenticación solo de Azure Active Directory con Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> La característica **Autenticación solo de Azure AD** que se describe en este artículo se encuentra en **versión preliminar pública**. 

Este artículo explica cómo habilitar la característica [Autenticación solo de Azure AD](authentication-azure-ad-only-authentication.md) con Azure SQL Database y Azure SQL Managed Instance. Si desea aprovisionar SQL Database o Azure SQL Managed Instance con la autenticación de solo Azure AD habilitada, consulte [Creación de un servidor con autenticación solo de Azure AD habilitada en Azure SQL](authentication-azure-ad-only-authentication-create-server.md).

En este tutorial aprenderá a:

> [!div class="checklist"]
> - Asignar un rol para habilitar la autenticación solo de Azure AD
> - Habilitar la autenticación solo de Azure AD mediante Azure Portal, la CLI de Azure o PowerShell
> - Comprobar si la autenticación solo de Azure AD está habilitada
> - Probar la conexión a Azure SQL
> - Deshabilitar la autenticación solo de Azure AD mediante Azure Portal, la CLI de Azure o PowerShell


## <a name="prerequisites"></a>Requisitos previos

- Una instancia de Azure AD. Para más información, consulte [Configuración y administración de la autenticación de Azure AD con Azure SQL](authentication-aad-configure.md).
- SQL Database o SQL Managed Instance con una base de datos e inicios de sesión o usuarios. Consulte [Inicio rápido: Creación de una base de datos única de Azure SQL Database](single-database-create-quickstart.md) si aún no ha creado una base de datos única de Azure SQL o [Inicio rápido: Creación de Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

## <a name="assign-role-to-enable-azure-ad-only-authentication"></a>Asignar un rol para habilitar la autenticación solo de Azure AD

Para habilitar o deshabilitar la autenticación solo de Azure AD, los roles integrados seleccionados son necesarios para los usuarios de Azure AD que ejecutan estas operaciones en este tutorial. En este tutorial vamos a asignar al usuario el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

Para obtener más información sobre la asignación de roles en una cuenta de Azure AD, consulte [Asignación de roles de administrador y de no administrador a usuarios con Azure Active Directory](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

Para obtener más información sobre el permiso necesario para habilitar o deshabilitar la autenticación solo de Azure AD, consulte la [sección Permisos del artículo sobre la autenticación solo de Azure AD](authentication-azure-ad-only-authentication.md#permissions).

1. En nuestro ejemplo, asignaremos el rol **Administrador de seguridad SQL** al usuario `UserSqlSecurityManager@contoso.onmicrosoft.com`. Con un usuario con privilegios que puede asignar roles Azure AD roles, inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Vaya al recurso del servidor SQL y seleccione **Control de acceso (IAM)** en el menú. Seleccione el botón **Agregar** y, a continuación, **Agregar asignación de roles** en el menú desplegable.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control.png" alt-text="Panel de control de acceso en Azure Portal":::

1. En el panel **Agregar asignación de roles**, seleccione el rol **Administrador de seguridad SQL** y elija el usuario que quiera que tenga la capacidad de habilitar o deshabilitar la autenticación solo de Azure AD.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control-add-role.png" alt-text="Incorporación del panel de asignación de roles en Azure Portal":::

1. Haga clic en **Save**(Guardar).

## <a name="enable-azure-ad-only-authentication"></a>Habilitación de la autenticación solo de Azure AD

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="enable-in-sql-database-using-azure-portal"></a>Habilitación en SQL Database con Azure Portal

Para habilitar la autenticación solo de Azure AD en Azure Portal, consulte los pasos siguientes.

1. Con el usuario con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager), vaya a [Azure Portal](https://portal.azure.com/).
1. Vaya al recurso del servidor SQL y seleccione **Azure Active Directory** en el menú **Configuración**.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal.png" alt-text="Habilitación del menú de autenticación solo de Azure AD":::

1. Si no ha agregado un **administrador de Azure Active Directory**, deberá establecerlo para poder habilitar la autenticación solo de Azure AD.
1. Seleccione la casilla **Solo admite la autenticación de Azure Active Directory para este servidor**.
1. Se mostrará la ventana emergente **Habilitar autenticación solo de Azure AD**. Haga clic en **Sí** para habilitar la característica y **guarde** la configuración.

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

Actualmente no se admite la administración de la autenticación solo de Azure AD para SQL Managed Instance en el portal.

# <a name="the-azure-cli"></a>[La CLI de Azure](#tab/azure-cli)

## <a name="enable-in-sql-database-using-azure-cli"></a>Habilitación en SQL Database con la CLI de Azure

Para habilitar la autenticación solo de Azure AD en Azure SQL Database mediante la CLI de Azure, consulte los comandos siguientes. [Instale la versión más reciente de la CLI de Azure](/cli/azure/install-azure-cli-windows). La versión de la CLI de Azure debe ser **2.14.2** o posterior. Para obtener más información sobre estos comandos, consulte [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth).

Para obtener más información sobre cómo administrar la autenticación solo de Azure AD con API, consulte [Administración de la autenticación solo de Azure AD con API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis).

> [!NOTE]
> Azure AD debe establecerse para el servidor antes de habilitar la autenticación solo de Azure AD. De lo contrario, se producirá un error en el comando de la CLI de Azure.
>
> Para obtener los permisos y las acciones necesarios para que el usuario pueda realizar estos comandos para habilitar la autenticación solo de Azure AD, consulte el artículo sobre la [autenticación solo de Azure AD](authentication-azure-ad-only-authentication.md#permissions).

1. [Inicie sesión en Azure](/cli/azure/authenticate-azure-cli) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```azurecli
   az sql server ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

## <a name="enable-in-sql-managed-instance-using-azure-cli"></a>Habilitar en SQL Managed Instance con la CLI de Azure

Para habilitar la autenticación solo de Azure AD en Azure SQL Managed Instance mediante la CLI de Azure, consulte los comandos siguientes. [Instale la versión más reciente de la CLI de Azure](/cli/azure/install-azure-cli-windows). 

1. [Inicie sesión en Azure](/cli/azure/authenticate-azure-cli) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```azurecli
   az sql mi ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="enable-in-sql-database-using-powershell"></a>Habilitar en SQL Database con PowerShell

Para habilitar la autenticación solo de Azure AD en Azure SQL Database mediante PowerShell, consulte los comandos siguientes. Se requiere el módulo [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) o posterior para ejecutar estos comandos. Para obtener más información sobre estos comandos, consulte [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication).

Para obtener más información sobre cómo administrar la autenticación solo de Azure AD con API, consulte [Administración de la autenticación solo de Azure AD con API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

> [!NOTE]
> Azure AD debe establecerse para el servidor antes de habilitar la autenticación solo de Azure AD. De lo contrario, se producirá un error en el comando de PowerShell.
>
> Para obtener los permisos y las acciones necesarios para que el usuario pueda realizar estos comandos para habilitar la autenticación solo de Azure AD, consulte el artículo sobre la [autenticación solo de Azure AD](authentication-azure-ad-only-authentication.md#permissions). Si el usuario no tiene suficientes permisos, obtendrá el siguiente error:
>
> ```output
> Enable-AzSqlServerActiveDirectoryOnlyAuthentication : The client
> 'UserSqlServerContributor@contoso.onmicrosoft.com' with object id
> '<guid>' does not have authorization to perform
> action 'Microsoft.Sql/servers/azureADOnlyAuthentications/write' over scope
> '/subscriptions/<guid>...'
> ```

1. [Inicie sesión en Azure](/powershell/azure/authenticate-azureps) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```powershell
   Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="enable-in-sql-managed-instance-using-powershell"></a>Habilitar en SQL Managed Instance con PowerShell

Para habilitar la autenticación solo de Azure AD en Azure SQL Managed Instance mediante PowerShell, consulte los comandos siguientes. Se requiere el módulo [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) o posterior para ejecutar estos comandos. 

Para obtener más información sobre cómo administrar la autenticación solo de Azure AD con API, consulte [Administración de la autenticación solo de Azure AD con API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis).


1. [Inicie sesión en Azure](/powershell/azure/authenticate-azureps) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Ejecute el siguiente comando reemplazando `<myinstance>` por el nombre de SQL Managed Instance y `<myresource>` por el recurso de Azure que contenga la instancia administrada de SQL.

   ```powershell
   Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="check-the-azure-ad-only-authentication-status"></a>Comprobación del estado de la autenticación solo de Azure AD

Compruebe si la autenticación solo de Azure AD está habilitada para el servidor o la instancia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Vaya al recurso del **servidor SQL** en [Azure Portal](https://portal.azure.com/). Seleccione **Azure Active Directory** en el menú **Configuración** . La compatibilidad de la autenticación solo de Azure AD está disponible exclusivamente para Azure SQL Database.

# <a name="the-azure-cli"></a>[La CLI de Azure](#tab/azure-cli)

Estos comandos se pueden usar para comprobar si la autenticación solo de Azure AD está habilitada para el servidor lógico de SQL Database o SQL Managed Instance. Los miembros de los roles colaborador de [Colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) y [colaborador de SQL Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) pueden usar estos comandos para comprobar el estado de la autenticación solo de Azure AD, pero no pueden habilitar ni deshabilitar la característica.

## <a name="check-status-in-sql-database"></a>Comprobación del estado en SQL Database

1. [Inicie sesión en Azure](/cli/azure/authenticate-azure-cli) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager). Para obtener más información sobre cómo administrar la autenticación solo de Azure AD con API, consulte [Administración de la autenticación solo de Azure AD con API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

   ```azurecli
   az login
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```azurecli
   az sql server ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. Debería ver la siguiente salida:

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="check-status-in-sql-managed-instance"></a>Comprobación del estado en SQL Managed Instance

1. [Inicie sesión en Azure](/cli/azure/authenticate-azure-cli) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```azurecli
   az sql mi ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. Debería ver la siguiente salida:

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Estos comandos se pueden usar para comprobar si la autenticación solo de Azure AD está habilitada para el servidor lógico de SQL Database o SQL Managed Instance. Los miembros de los roles colaborador de [Colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) y [colaborador de SQL Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) pueden usar estos comandos para comprobar el estado de la autenticación solo de Azure AD, pero no pueden habilitar ni deshabilitar la característica.

El estado volverá a ser **True** si la característica está habilitada y será **False** si se deshabilita.

## <a name="check-status-in-sql-database"></a>Comprobación del estado en SQL Database

1. [Inicie sesión en Azure](/powershell/azure/authenticate-azureps) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager). Para obtener más información sobre cómo administrar la autenticación solo de Azure AD con API, consulte [Administración de la autenticación solo de Azure AD con API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

   ```powershell
   Connect-AzAccount
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```powershell
   Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName <myserver> -ResourceGroupName <myresource>
   ```

## <a name="check-status-in-sql-managed-instance"></a>Comprobación del estado en SQL Managed Instance

1. [Inicie sesión en Azure](/powershell/azure/authenticate-azureps) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Ejecute el siguiente comando reemplazando `<myinstance>` por el nombre de SQL Managed Instance y `<myresource>` por el recurso de Azure que contenga la instancia administrada de SQL.

   ```powershell
   Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-sql-authentication-with-connection-failure"></a>Prueba de autenticación SQL con error de conexión

Después de habilitar la autenticación solo de Azure AD, haga la prueba con [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para [conectarse a SQL Database o Managed Instance](connect-query-ssms.md). Use la autenticación SQL para la conexión.

Debería aparecer un mensaje de error de inicio de sesión similar al siguiente:

```output
Cannot connect to <myserver>.database.windows.net.
Additional information:
  Login failed for user 'username'. Reason: Azure Active Directory only authentication is enabled.
  Please contact your system administrator. (Microsoft SQL Server, Error: 18456)
```

## <a name="disable-azure-ad-only-authentication"></a>Deshabilitar la autenticación solo de Azure AD

Al deshabilitar la característica de autenticación solo de Azure AD, permite la autenticación de SQL y de Azure AD para Azure SQL.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Con el usuario con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager), vaya a [Azure Portal](https://portal.azure.com/).
1. Vaya al recurso del servidor SQL y seleccione **Azure Active Directory** en el menú **Configuración**.
1. Para deshabilitar la característica de autenticación solo de Azure AD, desactive la casilla **Solo admite la autenticación de Azure Active Directory para este servidor** y **guarde** la configuración.

Actualmente no se admite la administración de la autenticación solo de Azure AD para SQL Managed Instance en el portal.

# <a name="the-azure-cli"></a>[La CLI de Azure](#tab/azure-cli)

## <a name="disable-in-sql-database-using-azure-cli"></a>Deshabilitar en SQL Database con la CLI de Azure

Para deshabilitar la autenticación solo de Azure AD en Azure SQL Database mediante la CLI de Azure, consulte los comandos siguientes. 

1. [Inicie sesión en Azure](/cli/azure/authenticate-azure-cli) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```azurecli
   az sql server ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. Después de deshabilitar la autenticación solo de Azure AD, debería ver lo siguiente al comprobar el estado:

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="disable-in-sql-managed-instance-using-azure-cli"></a>Deshabilitar en SQL Managed Instance con la CLI de Azure

Para deshabilitar la autenticación solo de Azure AD en Azure SQL Managed Instance mediante la CLI de Azure, consulte los comandos siguientes. 

1. [Inicie sesión en Azure](/cli/azure/authenticate-azure-cli) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```azurecli
   az login
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```azurecli
   az sql mi ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. Después de deshabilitar la autenticación solo de Azure AD, debería ver lo siguiente al comprobar el estado:

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="disable-in-sql-database-using-powershell"></a>Deshabilitar en SQL Database con PowerShell

Para deshabilitar la autenticación solo de Azure AD en Azure SQL Database mediante PowerShell, consulte los comandos siguientes.

1. [Inicie sesión en Azure](/powershell/azure/authenticate-azureps) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Ejecute el siguiente comando reemplazando `<myserver>` por el nombre de su servidor SQL y `<myresource>` por el recurso de Azure que contenga el servidor SQL.

   ```powershell
   Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="disable-in-sql-managed-instance-using-powershell"></a>Deshabilitar en SQL Managed Instance con PowerShell

Para deshabilitar la autenticación solo de Azure AD en Azure SQL Managed Instance mediante PowerShell, consulte los comandos siguientes.

1. [Inicie sesión en Azure](/powershell/azure/authenticate-azureps) con la cuenta con el rol [Administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

   ```powershell
   Connect-AzAccount
   ```

1. Ejecute el siguiente comando reemplazando `<myinstance>` por el nombre de SQL Managed Instance y `<myresource>` por el recurso de Azure que contenga la instancia administrada de SQL.

   ```powershell
   Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-connecting-to-azure-sql-again"></a>Volver a probar la conexión a Azure SQL

Después de deshabilitar la autenticación solo de Azure AD, pruebe a conectarse mediante inicio de sesión con autenticación SQL. Ahora debería poder conectarse a su servidor o instancia.

## <a name="next-steps"></a>Pasos siguientes

- [Autenticación solo de Azure AD con Azure SQL](authentication-azure-ad-only-authentication.md)
- [Creación de un servidor con autenticación solo con Azure AD habilitada en Azure SQL](authentication-azure-ad-only-authentication-create-server.md)
