---
title: Creación de un servidor con autenticación solo de Azure Active Directory habilitada
description: Este artículo le guía por la creación de un servidor lógico o una instancia administrada de Azure SQL con autenticación solo de Azure Active Directory (Azure AD) habilitada, lo que deshabilita la conectividad mediante la autenticación de SQL.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/31/2021
ms.openlocfilehash: 1519573670b3c97e1c47404ed457bf68c488108e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643201"
---
# <a name="create-server-with-azure-ad-only-authentication-enabled-in-azure-sql"></a>Creación de un servidor con autenticación solo de Azure AD habilitada en Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> La característica **Autenticación solo de Azure AD** que se describe en este artículo se encuentra en **versión preliminar pública**. Para obtener información detallada sobre esta característica, consulte [Autenticación solo de Azure AD con Azure SQL ](authentication-azure-ad-only-authentication.md). La autenticación solo de Azure AD no está disponible actualmente para Azure Synapse Analytics.

En esta guía paso a paso se describen los pasos para crear un [servidor lógico](logical-servers.md) para Azure SQL Database o [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) con la [autenticación solo de Azure AD](authentication-azure-ad-only-authentication.md) habilitada durante el aprovisionamiento. La autenticación solo de Azure AD impide que los usuarios se conecten al servidor o a la instancia administrada mediante la autenticación SQL y solo permite la conexión mediante autenticación de Azure AD.

## <a name="prerequisites"></a>Requisitos previos

- Se necesita la versión 2.26.1 o posterior cuando se usa la CLI de Azure. Para más información sobre la instalación la versión más reciente, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).
- Se necesita el módulo [Az 6.1.0](https://www.powershellgallery.com/packages/Az/6.1.0) o posterior cuando se usa PowerShell.
- Si va a aprovisionar una instancia administrada mediante la CLI de Azure, PowerShell o API REST, es necesario crear una red virtual y una subred antes de empezar. Para más información, consulte [Creación de una red virtual para Azure SQL Managed Instance](../managed-instance/virtual-network-subnet-create-arm-template.md).

## <a name="permissions"></a>Permisos

Para aprovisionar una instancia administrada o un servidor lógico, deberá tener los permisos adecuados para crear estos recursos. Los usuarios de Azure con permisos superiores, como [Propietarios](../../role-based-access-control/built-in-roles.md#owner), [Colaboradores](../../role-based-access-control/built-in-roles.md#contributor), [Administradores de servicios](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) y [Coadministradores](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) de la suscripción tienen el privilegio de crear un servidor SQL o una instancia administrada. Para crear estos recursos con el rol RBAC de Azure con menos privilegios, use el rol [Colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) para SQL Database y el rol [Colaborador de Instancia administrada de SQL Database](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor).

El rol RBAC [Administrador de seguridad de SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) no tiene permisos suficientes para crear un servidor o una instancia con la autenticación solo de Azure AD habilitada. El [administrador de seguridad de SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) será necesario para administrar la característica de autenticación de solo Azure AD después de la creación del servidor o la instancia.

## <a name="provision-with-azure-ad-only-authentication-enabled"></a>Aprovisionamiento con autenticación solo con Azure AD habilitada

En la sección siguiente se proporcionan ejemplos y scripts sobre cómo crear un servidor lógico o una instancia administrada con un conjunto de administradores de Azure AD para el servidor o la instancia, y tener habilitada la autenticación solo de Azure AD durante la creación del servidor. Para más información sobre la característica, consulte [Autenticación solo de Azure AD](authentication-azure-ad-only-authentication.md).

En nuestros ejemplos, vamos a habilitar la autenticación de solo Azure AD durante la creación del servidor o de la instancia administrada, con un administrador y una contraseña de servidor asignados por el sistema. Esto impedirá el acceso de administrador del servidor cuando esté habilitada la autenticación de solo Azure AD y solo permite que el administrador de Azure AD acceda al recurso. Es opcional agregar parámetros a las API para incluir su propio administrador de servidor y contraseña durante la creación del servidor. Sin embargo, la contraseña no se puede restablecer hasta que se deshabilite la autenticación de solo Azure AD.

Para cambiar las propiedades existentes después de crear el servidor o la instancia administrada, se deben usar otras API existentes. Consulte [Administración de la autenticación solo de Azure AD con API](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis) y [Configuración y administración de la autenticación de Azure AD con Azure SQL](authentication-aad-configure.md) para obtener más información.

> [!NOTE]
> Si la autenticación de solo Azure AD se establece en false, que es la opción predeterminada, se deben incluir un administrador del servidor y una contraseña en todas las API durante la creación del servidor o de la instancia administrada.

## <a name="azure-sql-database"></a>Azure SQL Database

# <a name="the-azure-cli"></a>[La CLI de Azure](#tab/azure-cli)

El comando de la CLI de Azure `az sql server create` se usa para aprovisionar un nuevo servidor lógico. El comando siguiente aprovisionará un nuevo servidor con la autenticación de solo Azure AD habilitada.

El inicio de sesión de administrador de SQL del servidor se creará automáticamente y la contraseña se establecerá en una contraseña aleatoria. Puesto que la conectividad de autenticación de SQL está deshabilitada con la creación de este servidor, no se usará el inicio de sesión de administrador de SQL.

El administrador de Azure AD de servidor será la cuenta que establezca para `<AzureADAccount>` y se puede usar para administrar el servidor.

Reemplace los valores siguientes del ejemplo:

- `<AzureADAccount>`: puede ser un usuario o grupo de Azure AD. Por ejemplo, `DummyLogin`.
- `<AzureADAccountSID>`: identificador de objeto de Azure AD del usuario
- `<ResourceGroupName>`: nombre del grupo de recursos para el servidor lógico.
- `<ServerName>`: use un nombre de servidor lógico único.

```azurecli
az sql server create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <ServerName>
```

Para más información, consulte [az sql server create](/cli/azure/sql/server#az_sql_server_create).

Para comprobar el estado del servidor después de la creación, vea el siguiente comando:

```azurecli
az sql server show --name <ServerName> --resource-group <ResourceGroupName> --expand-ad-admin
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

El comando de PowerShell `New-AzSqlServer` se usa para aprovisionar un nuevo servidor lógico de Azure SQL. El comando siguiente aprovisionará un nuevo servidor con la autenticación de solo Azure AD habilitada. 

El inicio de sesión de administrador de SQL del servidor se creará automáticamente y la contraseña se establecerá en una contraseña aleatoria. Puesto que la conectividad de autenticación de SQL está deshabilitada con la creación de este servidor, no se usará el inicio de sesión de administrador de SQL.

El administrador de Azure AD de servidor será la cuenta que establezca para `<AzureADAccount>` y se puede usar para administrar el servidor.

Reemplace los valores siguientes del ejemplo:

- `<ResourceGroupName>`: nombre del grupo de recursos para el servidor lógico.
- `<Location>`: ubicación del servidor, como `West US` o `Central US`
- `<ServerName>`: use un nombre de servidor lógico único.
- `<AzureADAccount>`: puede ser un usuario o grupo de Azure AD. Por ejemplo, `DummyLogin`.

```powershell
New-AzSqlServer -ResourceGroupName "<ResourceGroupName>" -Location "<Location>" -ServerName "<ServerName>" -ServerVersion "12.0" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication
```

Para más información, consulte [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver).

# <a name="rest-api"></a>[API DE REST](#tab/rest-api)

La API REST [Servidores: creación o actualización](/rest/api/sql/2020-11-01-preview/servers/create-or-update) se puede usar para crear un servidor lógico con la autenticación solo de Azure AD habilitada durante el aprovisionamiento. 

El script siguiente aprovisionará un servidor lógico, establecerá el administrador de Azure AD como `<AzureADAccount>` y habilitará la autenticación de Azure AD de solo ejecución. El inicio de sesión de administrador de SQL del servidor también se creará automáticamente y la contraseña se establecerá en una contraseña aleatoria. Puesto que la conectividad de autenticación de SQL está deshabilitada con este aprovisionamiento, no se usará el inicio de sesión de administrador de SQL.

El administrador de Azure AD, `<AzureADAccount>`, se puede usar para administrar el servidor una vez completado el aprovisionamiento.

Reemplace los valores siguientes del ejemplo:

- `<tenantId>`: se puede encontrar en [Azure Portal](https://portal.azure.com), en el recurso de **Azure Active Directory**. En el panel **Información general**, debe ver el **Identificador de inquilino**.
- `<subscriptionId>`: su identificador de suscripción se puede encontrar en Azure Portal.
- `<ServerName>`: use un nombre de servidor lógico único.
- `<ResourceGroupName>`: nombre del grupo de recursos para el servidor lógico.
- `<AzureADAccount>`: puede ser un usuario o grupo de Azure AD. Por ejemplo, `DummyLogin`.
- `<Location>`: ubicación del servidor, como `westus2` o `centralus`.
- `<objectId>`: se puede encontrar en [Azure Portal](https://portal.azure.com), en el recurso de **Azure Active Directory**. En el panel **Usuario**, busque el usuario Azure AD y su **identificador de objeto**.

```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$authUrl = "https://login.windows.net/$tenantId"
$serverName = "<ServerName>"
$resourceGroupName = "<ResourceGroupName>"

Login-AzAccount -tenantId $tenantId

# login as a user with SQL Server Contributor role or higher 

# Get a token

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes "https://management.core.windows.net/.default"

#Authetication header
$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

# Enable Azure AD-only auth 
# No server admin is specified, and only Azure AD admin and Azure AD-only authentication is set to true
# Server admin (login and password) is generated by the system

# Authentication body
# The sid is the Azure AD Object ID for the user 

$body = '{ 
"location": "<Location>",
"properties": { "administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true }
  }
}'

# Provision the server

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

Para comprobar el estado del servidor, puede usar el siguiente script:

```rest
$uri = 'https://management.azure.com/subscriptions/'+$subscriptionId+'/resourceGroups/'+$resourceGroupName+'/providers/Microsoft.Sql/servers/'+$serverName+'?api-version=2020-11-01-preview&$expand=administrators/activedirectory'

$responce=Invoke-WebRequest -Uri $uri -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

$responce.statuscode

$responce.content
```

# <a name="arm-template"></a>[Plantilla de ARM](#tab/arm-template)

Para más información y las plantillas de ARM, consulte [Plantillas de Azure Resource Manager para Azure SQL Database e Instancia administrada de Azure SQL](arm-templates-content-guide.md).

Para aprovisionar un servidor lógico con un conjunto de administradores de Azure AD para el servidor y la autenticación solo de Azure AD habilitada mediante una plantilla de ARM, consulte nuestra plantilla de inicio rápido [Servidor lógico de Azure SQL con autenticación solo de Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server-aad-only-auth).

También se puede utilizar la siguiente plantilla. Use una [implementación personalizada en Azure Portal](https://portal.azure.com/#create/Microsoft.Template) y **cree su propia plantilla en el editor**. A continuación, **guarde** la configuración una vez pegada en el ejemplo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "server": {
            "type": "string",
            "defaultValue": "[uniqueString('sql', resourceGroup().id)]",
            "metadata": {
                "description": "The name of the logical server."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL server."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('server')]",
            "location": "[parameters('location')]",
            "properties": {
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

## <a name="azure-sql-managed-instance"></a>Instancia administrada de Azure SQL

# <a name="the-azure-cli"></a>[La CLI de Azure](#tab/azure-cli)

El comando de la CLI de Azure `az sql mi create` se usa para aprovisionar una nueva instancia de Azure SQL Managed Instance. El comando siguiente aprovisionará una nueva instancia administrada con la autenticación de solo Azure AD habilitada.

> [!NOTE]
> El script requiere que se creen una red virtual y una subred como requisito previo.

El inicio de sesión de administrador de SQL de la instancia administrada se creará automáticamente y la contraseña se establecerá en una contraseña aleatoria. Puesto que la conectividad de autenticación de SQL está deshabilitada con este aprovisionamiento, no se usará el inicio de sesión de administrador de SQL.

El administrador de Azure AD será la cuenta que establezca para `<AzureADAccount>` y se puede usar para administrar la instancia cuando se complete el aprovisionamiento.

Reemplace los valores siguientes del ejemplo:

- `<AzureADAccount>`: puede ser un usuario o grupo de Azure AD. Por ejemplo, `DummyLogin`.
- `<AzureADAccountSID>`: identificador de objeto de Azure AD del usuario
- `<managedinstancename>`: asigne un nombre a la instancia administrada que desea crear.
- `<ResourceGroupName>`: abra el grupo de recursos de la instancia administrada. El grupo de recursos también debe incluir la red virtual y la subred creadas.
- El parámetro `subnet` debe actualizarse con `<Subscription ID>`, `<ResourceGroupName>`, `<VNetName>` y `<SubnetName>`. Su identificador de suscripción se puede encontrar en Azure Portal.

```azurecli
az sql mi create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <managedinstancename> --subnet /subscriptions/<Subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>
```

Para más información, consulte [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

El comando de PowerShell `New-AzSqlInstance` se usa para aprovisionar una nueva instancia de Azure SQL Managed Instance. El comando siguiente aprovisionará una nueva instancia administrada con la autenticación de solo Azure AD habilitada.

> [!NOTE]
> El script requiere que se creen una red virtual y una subred como requisito previo.

El inicio de sesión de administrador de SQL de la instancia administrada se creará automáticamente y la contraseña se establecerá en una contraseña aleatoria. Puesto que la conectividad de autenticación de SQL está deshabilitada con este aprovisionamiento, no se usará el inicio de sesión de administrador de SQL.

El administrador de Azure AD será la cuenta que establezca para `<AzureADAccount>` y se puede usar para administrar la instancia cuando se complete el aprovisionamiento.

Reemplace los valores siguientes del ejemplo:

- `<managedinstancename>`: asigne un nombre a la instancia administrada que desea crear.
- `<ResourceGroupName>`: abra el grupo de recursos de la instancia administrada. El grupo de recursos también debe incluir la red virtual y la subred creadas.
- `<Location>`: ubicación del servidor, como `West US` o `Central US`
- `<AzureADAccount>`: puede ser un usuario o grupo de Azure AD. Por ejemplo, `DummyLogin`.
- El parámetro `SubnetId` debe actualizarse con `<Subscription ID>`, `<ResourceGroupName>`, `<VNetName>` y `<SubnetName>`. Su identificador de suscripción se puede encontrar en Azure Portal.


```powershell
New-AzSqlInstance -Name "<managedinstancename>" -ResourceGroupName "<ResourceGroupName>" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication -Location "<Location>" -SubnetId "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4
```

Para más información, consulte [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

# <a name="rest-api"></a>[API DE REST](#tab/rest-api)

La API REST [Instancias administradas: creación o actualización](/rest/api/sql/2020-11-01-preview/managed-instances/create-or-update) se puede usar para crear una instancia administrada con la autenticación de solo Azure AD habilitada durante el aprovisionamiento.

> [!NOTE]
> El script requiere que se cree una red virtual y una subred como requisito previo.

El script siguiente aprovisionará una instancia administrada, establecerá el administrador de Azure AD como `<AzureADAccount>` y habilitará la autenticación solo de Azure AD. El inicio de sesión de administrador de SQL de la instancia también se creará automáticamente y la contraseña se establecerá en una contraseña aleatoria. Puesto que la conectividad de autenticación de SQL está deshabilitada con este aprovisionamiento, no se usará el inicio de sesión de administrador de SQL.

El administrador de Azure AD, `<AzureADAccount>`, se puede usar para administrar la instancia una vez completado el aprovisionamiento.

Reemplace los valores siguientes del ejemplo:

- `<tenantId>`: se puede encontrar en [Azure Portal](https://portal.azure.com), en el recurso de **Azure Active Directory**. En el panel **Información general**, debe ver el **Identificador de inquilino**.
- `<subscriptionId>`: su identificador de suscripción se puede encontrar en Azure Portal.
- `<instanceName>`: use un nombre de instancia administrada único.
- `<ResourceGroupName>`: nombre del grupo de recursos para el servidor lógico.
- `<AzureADAccount>`: puede ser un usuario o grupo de Azure AD. Por ejemplo, `DummyLogin`.
- `<Location>`: ubicación del servidor, como `westus2` o `centralus`.
- `<objectId>`: se puede encontrar en [Azure Portal](https://portal.azure.com), en el recurso de **Azure Active Directory**. En el panel **Usuario**, busque el usuario Azure AD y su **identificador de objeto**.
- El parámetro `subnetId` debe actualizarse con `<ResourceGroupName>`, `Subscription ID`, `<VNetName>` y `<SubnetName>`.


```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$instanceName = "<instanceName>"
$resourceGroupName = "<ResourceGroupName>"
$scopes ="https://management.core.windows.net/.default"

Login-AzAccount -tenantId $tenantId

# Login as an Azure AD user with permission to provision a managed instance

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes $scopes

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

$body = '{
"name": "<instanceName>", "type": "Microsoft.Sql/managedInstances", "identity": { "type": "SystemAssigned"},"location": "<Location>", "sku": {"name": "GP_Gen5", "tier": "GeneralPurpose", "family":"Gen5","capacity": 8},
"properties": {"administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true },
"subnetId": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>",
"licenseType": "LicenseIncluded", "vCores": 8, "storageSizeInGB": 2048, "collation": "SQL_Latin1_General_CP1_CI_AS", "proxyOverride": "Proxy", "timezoneId": "UTC", "privateEndpointConnections": [], "storageAccountType": "GRS", "zoneRedundant": false 
  }
}'

# To provision the instance, execute the `PUT` command

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

```

Para comprobar los resultados, ejecute el comando `GET`:

```rest
Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[Plantilla de ARM](#tab/arm-template)

Para aprovisionar una nueva instancia administrada, una red virtual y una subred, con un conjunto de administradores de Azure AD para la instancia y la autenticación de solo Azure AD habilitada, use la plantilla siguiente.

Use una [implementación personalizada en Azure Portal](https://portal.azure.com/#create/Microsoft.Template) y **cree su propia plantilla en el editor**. A continuación, **guarde** la configuración una vez pegada en el ejemplo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "managedInstanceName": {
            "type": "String",
            "metadata": {
                "description": "Enter managed instance name."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL managed instance."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "Enter location. If you leave this field blank resource group location would be used."
            }
        },
        "virtualNetworkName": {
            "type": "String",
            "defaultValue": "SQLMI-VNET",
            "metadata": {
                "description": "Enter virtual network name. If you leave this field blank name will be created by the template."
            }
        },
        "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "String",
            "metadata": {
                "description": "Enter virtual network address prefix."
            }
        },
        "subnetName": {
            "type": "String",
            "defaultValue": "ManagedInstances",
            "metadata": {
                "description": "Enter subnet name. If you leave this field blank name will be created by the template."
            }
        },
        "subnetPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "String",
            "metadata": {
                "description": "Enter subnet address prefix."
            }
        },
        "skuName": {
            "defaultValue": "GP_Gen5",
            "allowedValues": [
                "GP_Gen5",
                "BC_Gen5"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter sku name."
            }
        },
        "vCores": {
            "defaultValue": 16,
            "allowedValues": [
                8,
                16,
                24,
                32,
                40,
                64,
                80
            ],
            "type": "Int",
            "metadata": {
                "description": "Enter number of vCores."
            }
        },
        "storageSizeInGB": {
            "defaultValue": 256,
            "minValue": 32,
            "maxValue": 8192,
            "type": "Int",
            "metadata": {
                "description": "Enter storage size."
            }
        },
        "licenseType": {
            "defaultValue": "LicenseIncluded",
            "allowedValues": [
                "BasePrice",
                "LicenseIncluded"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter license type."
            }
        }
    },
    "variables": {
        "networkSecurityGroupName": "[concat('SQLMI-', parameters('managedInstanceName'), '-NSG')]",
        "routeTableName": "[concat('SQLMI-', parameters('managedInstanceName'), '-Route-Table')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-06-01",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "allow_tds_inbound",
                        "properties": {
                            "description": "Allow access to data",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "1433",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1000,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "allow_redirect_inbound",
                        "properties": {
                            "description": "Allow inbound redirect traffic to Managed Instance inside the virtual network",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "11000-11999",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1100,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_inbound",
                        "properties": {
                            "description": "Deny all other inbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_outbound",
                        "properties": {
                            "description": "Deny all other outbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Outbound"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/routeTables",
            "apiVersion": "2020-06-01",
            "name": "[variables('routeTableName')]",
            "location": "[parameters('location')]",
            "properties": {
                "disableBgpRoutePropagation": false
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2020-06-01",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('routeTableName')]",
                "[variables('networkSecurityGroupName')]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[parameters('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetPrefix')]",
                            "routeTable": {
                                "id": "[resourceId('Microsoft.Network/routeTables', variables('routeTableName'))]"
                            },
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
                            },
                            "delegations": [
                                {
                                    "name": "miDelegation",
                                    "properties": {
                                        "serviceName": "Microsoft.Sql/managedInstances"
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('managedInstanceName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('virtualNetworkName')]"
            ],
            "sku": {
                "name": "[parameters('skuName')]"
            },
            "identity": {
                "type": "SystemAssigned"
            },
            "properties": {
                "subnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
                "storageSizeInGB": "[parameters('storageSizeInGB')]",
                "vCores": "[parameters('vCores')]",
                "licenseType": "[parameters('licenseType')]",
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

### <a name="grant-directory-readers-permissions"></a>Concesión de permisos de lectores de directorios

Una vez completada la implementación de la instancia administrada, es posible que observe que el Instancia administrada necesita permisos de **lectura** para acceder a Azure Active Directory. Los permisos de lectura se pueden conceder haciendo clic en el mensaje mostrado en Azure Portal a través de una persona con privilegios suficientes. Para más información, consulte [Rol Lectores de directorio en Azure Active Directory de Azure SQL](authentication-aad-directory-readers-role.md).

:::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-read-permissions.png" alt-text="Captura de pantalla del menú de administración de Active Directory de Azure Portal que muestra los permisos de lectura necesarios":::

## <a name="limitations"></a>Limitaciones

- Actualmente no se admite la creación de un servidor o instancia mediante Azure Portal con la autenticación solo de Azure AD habilitada durante el aprovisionamiento.
- Para restablecer la contraseña del administrador del servidor, la autenticación de solo Azure AD debe estar deshabilitada.
- Si la autenticación de solo Azure AD está deshabilitada, debe crear un servidor con un administrador del servidor y una contraseña cuando use todas las API.

## <a name="next-steps"></a>Pasos siguientes

- Si ya tiene un servidor de SQL o una instancia administrada y solo desea habilitar la autenticación de solo Azure AD, consulte [Tutorial: Habilitar la autenticación solo de Azure Active Directory con Azure SQL](authentication-azure-ad-only-authentication-tutorial.md).
- Para más información sobre la característica de autenticación solo de Azure AD, consulte [Autenticación solo de Azure AD con Azure SQL](authentication-azure-ad-only-authentication.md).
- Para aplicar la creación de servidores con la autenticación solo de Azure AD habilitada, consulte [Azure Policy para autenticación exclusiva de Azure Active Directory con Azure SQL](authentication-azure-ad-only-authentication-policy.md).