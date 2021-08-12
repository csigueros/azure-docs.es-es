---
title: Entidad de servicio de Azure Active Directory con Azure SQL
description: Utilice la aplicaciones de AD (entidades de servicio) para admitir la creación de usuarios de Azure AD en Azure SQL Database y Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 05/11/2021
ms.openlocfilehash: 606197b7dbc327c80da9d8a1ed9f3b933dad176a
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "109810598"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Entidad de servicio de Azure Active Directory con Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Active Directory (Azure AD) admite la creación de usuarios en Azure SQL Database (SQL DB) en nombre de las aplicaciones de Azure AD (entidades de servicio).

> [!NOTE]
> Esta funcionalidad ya se admite en SQL Managed Instance.

## <a name="service-principal-azure-ad-applications-support"></a>Compatibilidad con entidades de servicio (aplicaciones de Azure AD)

Este artículo se aplica a las aplicaciones que están integradas en Azure AD y forman parte del registro de Azure AD. A menudo, estas aplicaciones necesitan acceso de autenticación y autorización a Azure SQL para realizar diversas tareas. Esta característica permite a las entidades de servicio crear usuarios de Azure AD en SQL Database. Había una limitación que impedía la creación de objetos de Azure AD en nombre de aplicaciones de Azure AD, pero se han eliminado.

Cuando se registra una aplicación de Azure AD mediante Azure Portal o un comando de PowerShell, se crean dos objetos en el inquilino de Azure AD:

- Un objeto de aplicación
- Un objeto de entidad de servicio.

Para obtener más información sobre las aplicaciones de Azure AD, consulte[Objetos de aplicación y de entidad de servicio de Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) y [Creación de una entidad de servicio de Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Tanto SQL Database como SQL Managed Instance admiten los siguientes objetos de Azure AD:

- Usuarios de Azure AD (administrados, federados e invitados)
- Grupos de Azure AD (administrados y federados)
- Aplicaciones de Azure AD 

Ahora se admite el comando de T-SQL `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` en nombre de una aplicación de Azure AD para SQL Database.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funcionalidad de la creación de usuarios de Azure AD mediante entidades de servicio

La compatibilidad con esta funcionalidad es útil en los procesos de automatización de aplicaciones de Azure AD donde objetos de Azure AD se crean y mantienen en SQL Database sin interacción humana. Las entidades de servicio pueden ser un administrador de Azure AD para el servidor lógico de SQL, como parte de un grupo o un usuario individual. La aplicación puede automatizar la creación de objetos de Azure AD en SQL Database cuando se ejecuta como administrador del sistema y no requiere ningún privilegio de SQL adicional. Esto permite una automatización completa de la creación de usuarios de base de datos. Esta característica también admite una identidad administrada asignada por el sistema de Azure AD y una identidad administrada asignada por el usuario que se pueden crear como usuarios en SQL Database en nombre de las entidades de servicio. Para obtener más información, consulte [¿Qué son las identidades administradas para recursos de Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Habilitación de entidades de servicio para crear usuarios de Azure AD

Para habilitar la creación de un objeto de Azure AD en SQL Database en nombre de una aplicación de Azure AD, se requiere la siguiente configuración:

1. Asigne la identidad del servidor. La identidad del servidor asignada representa la identidad del servicio administrado (MSI). Actualmente, la identidad del servidor de Azure SQL no admite la identidad administrada por el usuario (UMI).
    - Para un nuevo servidor lógico de Azure SQL, ejecute el siguiente comando de PowerShell:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Para obtener más información, consulte el comando [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver).

    - Para los servidores lógicos existentes de Azure SQL, ejecute el siguiente comando:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Para más información, consulte el comando [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver).

    - Para comprobar si la identidad del servidor está asignada al servidor, ejecute el comando Get-AzSqlServer.

    > [!NOTE]
    > La identidad del servidor se puede asignar mediante los comandos de la API REST y la CLI también. Para más información, consulte [az sql server create](/cli/azure/sql/server#az_sql_server_create), [az sql server update](/cli/azure/sql/server#az_sql_server_update) y [Servidores: API REST](/rest/api/sql/2020-08-01-preview/servers).

2. Conceder el permiso [**Lectores de directorio**](../../active-directory/roles/permissions-reference.md#directory-readers) de Azure AD a la identidad del servidor creada o asignada al servidor
    - Para conceder este permiso, siga la descripción que se usa para SQL Managed Instance que está disponible en el siguiente artículo: [Aprovisionamiento de un administrador de Azure AD (Instancia administrada de SQL)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - El usuario de Azure AD que concede este permiso debe formar parte del rol **Administrador global** o **Administrador de roles con privilegios** de Azure AD.

> [!IMPORTANT]
> Los pasos 1 y 2 se deben ejecutar en el orden anterior. En primer lugar, cree o asigne la identidad del servidor y, a continuación, conceda el permiso [**Lectores de directorio**](../../active-directory/roles/permissions-reference.md#directory-readers). Si se omite uno de estos pasos, o ambos, se producirá un error de ejecución durante la creación de un objeto de Azure AD en Azure SQL en nombre de una aplicación de Azure AD.
>
> En la **versión preliminar pública**, puede asignar el rol **Lectores de directorio** a un grupo en Azure AD. A continuación, los propietarios del grupo pueden agregar la identidad administrada como miembro de este grupo, lo que omitiría la necesidad de que un usuario **Administrador global** o **Administrador de roles con privilegios** concedieran el rol **Lectores de directorio**. Para más información sobre esta característica, vea [Rol Lectores de directorio en Azure Active Directory de Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations"></a>Solución de problemas y limitaciones

- Al crear objetos de Azure AD en Azure SQL en nombre de una aplicación de Azure AD sin habilitar la identidad del servidor y conceder el permiso **Lectores de directorio**, se producirá un error en la operación con los siguientes errores posibles. El siguiente error de ejemplo es para la ejecución de un comando de PowerShell para crear un usuario de SQL Database `myapp` en el artículo [Tutorial: Creación de usuarios de Azure AD mediante aplicaciones de Azure AD](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved. Error message:
    'Server identity is not configured. Please follow the steps in "Assign an Azure AD identity to your server and add
    Directory Reader permission to your identity" (https://aka.ms/sqlaadsetup)'"`
      - Para el error anterior, siga los pasos para [asignar una identidad al servidor lógico de Azure SQL](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) y [asignar el permiso Lectores de directorio a la identidad del servidor lógico de SQL](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    - Se admite la definición de la entidad de servicio (aplicación de Azure AD) como administrador de Azure AD para SQL Database con los comandos de Azure Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse) y la [API REST](/rest/api/sql/2020-08-01-preview/servers) y la [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse).
- El uso de una aplicación de Azure AD con la entidad de servicio de otro inquilino de Azure AD producirá un error al acceder a la instancia de SQL Database o SQL Managed Instance creada en otro inquilino. Una entidad de servicio asignada a esta aplicación debe provenir del mismo inquilino que el servidor lógico de SQL o Managed Instance.
- Se necesita el módulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) o posterior cuando se usa PowerShell para configurar una aplicación de Azure AD individual como administrador de Azure AD para Azure SQL. Asegúrese de que ha actualizado al módulo más reciente.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Creación de usuarios de Azure AD mediante aplicaciones de Azure AD](authentication-aad-service-principal-tutorial.md)
