---
title: Uso de identidades administradas desde una máquina virtual para acceder a Cosmos DB | Microsoft Docs
description: Aprenda a usar identidades administradas con máquinas virtuales Windows mediante Azure Portal, la CLI, PowerShell o una plantilla de Azure Resource Manager.
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: msi
ms.workload: integration
ms.topic: tutorial
ms.date: 10/14/2021
ms.author: barclayn
ms.custom: ep-miar
ms.openlocfilehash: 7ff0ae1578a8b1d637f7baee50ed6d71a63e3996
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070116"
---
# <a name="how-to-use-managed-identities-to-connect-to-cosmos-db-from-an-azure-virtual-machine"></a>Uso de identidades administradas para conectarse a Cosmos DB desde una máquina virtual de Azure

En este artículo, se configura una máquina virtual para usar identidades administradas para conectarse a Cosmos. [Azure Cosmos DB](../../cosmos-db/introduction.md) es una base de datos NoSQL totalmente administrada para el desarrollo de aplicaciones modernas. [Las identidades administradas para recursos de Azure](overview.md) permiten que las aplicaciones se autentiquen al acceder a servicios que admiten la autenticación de Azure AD mediante una identidad administrada por Azure.

## <a name="prerequisites"></a>Prerrequisitos

- Conocimientos básicos sobre identidades administradas. Si desea obtener más información sobre las identidades administradas para los recursos de Azure antes de continuar, revise la [introducción](overview.md) a las identidades administradas.
- Debe disponer de una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/).
- Puede que necesite [PowerShell](/powershell/azure/new-azureps-module-az) o la [CLI](/cli/azure/install-azure-cli).
- [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) o algún otro entorno de desarrollo de su elección. 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un nuevo grupo de recursos denominado **mi-test**. Usaremos este grupo de recursos para todos los recursos que se usan en este tutorial.

- [Creación de un grupo de recursos mediante Azure Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)
- [Creación de un grupo de recursos mediante la CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md#create-resource-groups)
- [Creación de un grupo de recursos mediante PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md#create-resource-groups)

## <a name="create-an-azure-vm-with-a-managed-identity"></a>Creación de una máquina virtual de Azure con una identidad administrada

Para este tutorial, necesita una máquina virtual de Azure. Cree un conjunto de escalado de máquinas virtuales con una identidad administrada asignada por el sistema habilitada denominada **mi-vm-01**.  También puede [crear una identidad administrada asignada por el usuario](how-manage-user-assigned-managed-identities.md) denominada **mi-ua-01** en el grupo de recursos que creamos anteriormente (**mi-test**). Si usa una identidad administrada asignada por el usuario, puede asignarla a una máquina virtual durante la creación.

### <a name="create-a-vm-with-a-system-assigned-managed-identity"></a>Creación de una máquina virtual con una identidad administrada asignada por el sistema

Para crear una máquina virtual de Azure que tenga habilitada la identidad administrada asignada por el sistema, la cuenta debe tener la asignación de roles [Colaborador de la máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  No es necesaria ninguna otra asignación de roles de Azure AD.

# <a name="portal"></a>[Portal](#tab/azure-portal)

- En **Azure Portal**, busque **máquinas virtuales**.
- Elija **Crear**.
- En la pestaña Aspectos básicos, proporcione la información necesaria.
- Elija **Siguiente: Discos >** .
- Rellene la información según sea necesario y, en la pestaña **Administración**, busque la sección **Identidad** y active la casilla situada junto a **Identidad administrada asignada por el sistema**.

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/create-vm-system-assigned-managed-identities.png" alt-text="Imagen que muestra cómo habilitar identidades administradas asignadas por el sistema al crear una máquina virtual.":::

Para más información, consulte la documentación sobre máquinas virtuales de Azure:

- [Linux](../../virtual-machines/linux/quick-create-portal.md)
- [Windows](../../virtual-machines/windows/quick-create-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AZVM](/powershell/module/az.compute/new-azvm) crea recursos a los que se hace referencia si no existen. Para crear una máquina virtual con una identidad administrada asignada por el sistema habilitada, pase el parámetro **-SystemAssignedIdentity** como se muestra a continuación. 


```powershell

New-AzVm `
    -ResourceGroupName "My VM" `
    -Name "My resource group" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -SystemAssignedIdentity
    -OpenPorts 80,3389
```

- [Inicio rápido: Creación de una máquina virtual Windows en Azure con PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
- [Inicio rápido: Creación de una máquina virtual Linux en Azure con PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Cree una máquina virtual mediante [az vm create](/cli/azure/vm/#az_vm_create). En el ejemplo siguiente, se crea una VM denominada *myVM* con una identidad administrada asignada por el sistema, como ha solicitado el parámetro `--assign-identity`. Los parámetros `--admin-username` y `--admin-password` especifican el nombre de usuario administrativo y la contraseña de la cuenta para el inicio de sesión en la máquina virtual. Actualice estos valores según convenga para su entorno: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

- [Creación de una máquina virtual Linux con una identidad administrada asignada por el sistema](../../virtual-machines/linux/quick-create-cli.md)
- [Creación de una máquina virtual Windows con una identidad administrada asignada por el sistema](../../virtual-machines/windows/quick-create-cli.md)

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

Para habilitar la identidad administrada asignada por el sistema, cargue la plantilla en un editor, busque el recurso de interés `Microsoft.Compute/virtualMachines` en la sección `resources` y agregue la propiedad `"identity"` en el mismo nivel que la propiedad `"type": "Microsoft.Compute/virtualMachines"`. Use la sintaxis siguiente:

   ```json
   "identity": {
       "type": "SystemAssigned"
   },
   ```

Cuando haya terminado, deben agregarse las siguientes secciones a la sección `resource` de la plantilla. La plantilla debe ser similar a la del ejemplo que se muestra a continuación:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('myVM')]",
            "location": "[myResourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

---


### <a name="create-a-vm-with-a-user-assigned-managed-identity"></a>Creación de una máquina virtual con una identidad administrada asignada por el usuario

En los pasos siguientes se muestra cómo crear una máquina virtual con una identidad administrada asignada por el usuario configurada.

# <a name="portal"></a>[Portal](#tab/azure-portal)

En la actualidad, Azure Portal no permite asignar una identidad administrada asignada por el usuario durante la creación de una máquina virtual. Debe crear una máquina virtual y, a continuación, asignarle una identidad administrada asignada por el usuario.

[Configurar identidades administradas para recursos de Azure en una VM mediante Azure Portal](qs-configure-portal-windows-vm.md#user-assigned-managed-identity)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cree una máquina virtual Windows con una identidad administrada asignada por el usuario especificada.

```powershell
New-AzVm `
    -ResourceGroupName "<Your resource group>" `
    -Name "<Your VM name>" `
    -Location "East US" `
    -VirtualNetworkName "<myVnet>" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 80,3389

```

Cree una máquina virtual Linux con una identidad administrada asignada por el usuario especificada.

```powershell
New-AzVm `
    -Name "<Linux VM name>" `
    -image CentOS
    -ResourceGroupName "<Your resource group>" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -Linux `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 22


```

La identidad administrada asignada por el usuario debe especificarse mediante su [resourceID](how-manage-user-assigned-managed-identities.md
). 

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az vm create --resource-group <MyResourceGroup> --name <myVM> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
```

[Configuración de identidades administradas para recursos de Azure en una máquina virtual mediante la CLI de Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

En función de la versión de la API, debe realizar [diferentes pasos](qs-configure-template-windows-vm.md#user-assigned-managed-identity). Si apiVersion es 2018-06-01, las identidades administradas asignadas por el usuario se almacenan en el formato de diccionario userAssignedIdentities y el valor ```<identityName>``` es el nombre de una variable que se define en la sección variables de la plantilla. En la variable, apunta a la identidad administrada asignada por el usuario que desea asignar.

```json
    "variables": {
     "identityName": "my-user-assigned" 
        
    },
```

En el elemento de recursos, agregue la siguiente entrada para asignar una identidad administrada asignada por el usuario a la máquina virtual. No olvide reemplazar ```<identityName>``` con el nombre de la identidad administrada asignada por el usuario que ha creado.

```json

"resources": [
     {
         //other resource provider properties...
         "apiVersion": "2018-06-01",
         "type": "Microsoft.Compute/virtualMachines",
         "name": "[variables('vmName')]",
         "location": "[resourceGroup().location]",
         "identity": {
             "type": "userAssigned",
             "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<identityName>'))]": {}
             }
         }
     }
 ]
```

---

## <a name="create-a-cosmos-db-account"></a>Creación de una cuenta de Cosmos DB

Ahora que tenemos una máquina virtual con una identidad administrada asignada por el usuario o por el sistema, necesitamos una cuenta de Cosmos DB disponible donde tenga derechos administrativos. Si necesita crear una cuenta de Cosmos DB para este tutorial, el [inicio rápido de Cosmos DB](../..//cosmos-db/sql/create-cosmosdb-resources-portal.md) proporciona pasos detallados sobre cómo hacerlo.

>[!NOTE]
> Las identidades administradas se pueden usar para acceder a cualquier recurso de Azure que admita la autenticación de Azure Active Directory. En este tutorial se da por supuesto que la cuenta de Cosmos DB tendrá la siguiente configuración.

 |Configuración|Value|Descripción |
   |---|---|---|
   |Subscription|Nombre de suscripción|Seleccione la suscripción de Azure que desea usar para esta cuenta de Azure Cosmos. |
   |Grupo de recursos|Definición de un nombre de grupo de recursos|Seleccione **mi-test** o seleccione **Crear nuevo** y escriba un nombre único para el grupo de recursos nuevo. |
   |Nombre de cuenta|Un nombre único|Escriba un nombre para identificar la cuenta de Azure Cosmos. Dado que *documents.azure.com* se anexa al nombre que se proporciona para crear el identificador URI, debe usar un nombre único.<br><br>El nombre solo puede contener letras minúsculas, números y el carácter de guion (-). Debe tener una longitud de entre 3 y 44 caracteres.|
   |API|El tipo de cuenta que se va a crear|Seleccione **Core(SQL)** para crear una base de datos de documentos y consultarla mediante la sintaxis SQL. <br><br>[Más información acerca de SQL API](../../cosmos-db/introduction.md).|
   |Location|Región más cercana a los usuarios|Seleccione una ubicación geográfica para hospedar la cuenta de Azure Cosmos DB. Use la ubicación más cercana a los usuarios para que puedan acceder de la forma más rápida posible a los datos.|

   > [!NOTE]
   > Si va a realizar pruebas, puede que desee aplicar el descuento por nivel Gratis de Azure Cosmos DB. Con el nivel Gratis de Azure Cosmos DB, recibirá los primeros 1000 RU/s y 25 GB de almacenamiento gratis en una cuenta. Más información acerca del [nivel Gratis](https://azure.microsoft.com/pricing/details/cosmos-db/). Tenga en cuenta que, para este tutorial, esta opción no supone ninguna diferencia.

## <a name="grant-access"></a>Conceder acceso

En este punto, deberíamos tener una máquina virtual configurada con una identidad administrada y una cuenta de Cosmos DB. Antes de continuar, es necesario conceder a la identidad administrada un par de roles diferentes.

- En primer lugar, conceda acceso al plano de administración de Cosmos mediante [RBAC de Azure](../../cosmos-db/role-based-access-control.md). La identidad administrada debe tener asignado el rol Colaborador de cuenta de DocumentDB para crear bases de datos y contenedores.

- También debe conceder a la identidad administrada un rol de colaborador mediante [RBAC de Cosmos](../../cosmos-db/how-to-setup-rbac.md). Puede ver los pasos concretos a continuación. 

> [!NOTE] 
> Usaremos el rol **Colaborador de datos integrado de Cosmos DB**. Para conceder acceso, debe asociar la definición de rol a la identidad. En nuestro caso, la identidad administrada asociada a nuestra máquina virtual.

# <a name="portal"></a>[Portal](#tab/azure-portal)

**En este momento no hay ninguna opción de asignación de roles disponible en Azure Portal**


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>" 
$contributorRoleDefinitionId = "00000000-0000-0000-0000-000000000002" # This is the ID of the Cosmos DB Built-in Data contributor role definition
$principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $contributorRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

Cuando se complete el paso de asignación de roles, debería ver resultados similares a los que se muestran a continuación.

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/results-role-assignment.png" alt-text="Esto muestra los resultados de la asignación de roles.":::

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli

resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '00000000-0000-0000-0000-000000000002' # This is the ID of the Cosmos DB Built-in Data contributor role definition
principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId

```

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

```JSON
{
  "id": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleAssignments/00000000-0000-0000-0000-000000000002",
  "name": "myRoleAssignmentId",
  "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments",
  "properties": {
    "roleDefinitionId": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleDefinitions/00000000-0000-0000-0000-000000000002",
    "scope": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/dbs/purchases/colls/redmond-purchases",
    "principalId": "myPrincipalId"
  }
}

```

---

## <a name="access-data"></a>Acceso a los datos

El acceso a Cosmos mediante identidades administradas se puede lograr mediante la biblioteca Azure.identity para habilitar la autenticación en la aplicación. Puede llamar directamente a [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) o usar [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential).

La clase ManagedIdentityCredential intenta realizar la autenticación mediante una identidad administrada asignada al entorno de implementación. La clase [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme) pasa por diferentes opciones de autenticación en orden. La segunda opción de autenticación que DefaultAzureCredential intenta son las identidades administradas. 

En el ejemplo que se muestra a continuación se crea una base de datos, un contenedor, un elemento en el contenedor y se lee el elemento recién creado mediante la identidad administrada asignada por el sistema de la máquina virtual. Si desea usar una identidad administrada asignada por el usuario, debe especificarla mediante el identificador de cliente de la identidad administrada. 

```csharp
string userAssignedClientId = "<your managed identity client Id>";
var tokenCredential = new DefaultAzureCredential(new DefaultAzureCredentialOptions { ManagedIdentityClientId = userAssignedClientId });
```

Para usar el ejemplo siguiente, debe tener los siguientes paquetes NuGet:

- Azure.Identity
- Microsoft.Azure.Cosmos
- Microsoft.Azure.Management.CosmosDB

Además de los paquetes NuGet anteriores, también debe habilitar **Incluir versión preliminar** y, a continuación, agregar **Azure.ResourceManager.CosmosDB**. 

```csharp
using Azure.Identity;
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;
using Microsoft.Azure.Cosmos;
using System;
using System.Threading.Tasks;

namespace MITest
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var subscriptionId = "Your subscription ID";
            var resourceGroupName = "You resource group";
            var accountName = "Cosmos DB Account name";
            var databaseName = "mi-test";
            var containerName = "container01";

            var tokenCredential = new DefaultAzureCredential();

            // create the management clientSS
            var managementClient = new CosmosDBManagementClient(subscriptionId, tokenCredential);

            // create the data client
            var dataClient = new CosmosClient("https://[Account].documents.azure.com:443/", tokenCredential);

            // create a new database 
            var createDatabaseOperation = await managementClient.SqlResources.StartCreateUpdateSqlDatabaseAsync(resourceGroupName, accountName, databaseName,
                new SqlDatabaseCreateUpdateParameters(new SqlDatabaseResource(databaseName), new CreateUpdateOptions()));
            await createDatabaseOperation.WaitForCompletionAsync();

            // create a new container
            var createContainerOperation = await managementClient.SqlResources.StartCreateUpdateSqlContainerAsync(resourceGroupName, accountName, databaseName, containerName,
                new SqlContainerCreateUpdateParameters(new SqlContainerResource(containerName), new CreateUpdateOptions()));
            await createContainerOperation.WaitForCompletionAsync();


            // create a new item 
            var partitionKey = "pkey";
            var id = Guid.NewGuid().ToString();
            await dataClient.GetContainer(databaseName, containerName)
                .CreateItemAsync(new { id = id, _partitionKey = partitionKey }, new PartitionKey(partitionKey));


            // read back the item
            var pointReadResult = await dataClient.GetContainer(databaseName, containerName)
                .ReadItemAsync<dynamic>(id, new PartitionKey(partitionKey));


            // run a query
            await dataClient.GetContainer(databaseName, containerName)
                .GetItemQueryIterator<dynamic>("SELECT * FROM c")
                .ReadNextAsync();
        }
    }
}

```

Ejemplos específicos del lenguaje mediante ManagedIdentityCredential:

### <a name="net"></a>.NET

Inicialice el cliente de Cosmos DB:

```csharp
CosmosClient client = new CosmosClient("<account-endpoint>", new ManagedIdentityCredential());
```

A continuación, [lea y escriba los datos](../../cosmos-db/sql/sql-api-dotnet-v3sdk-samples.md).

### <a name="java"></a>Java

Inicialice el cliente de Cosmos DB:

```java
CosmosAsyncClient Client = new CosmosClientBuilder().endpoint("<account-endpoint>") .credential(new ManagedIdentityCredential()) .build();
```

A continuación, lea y escriba los datos como se indica en [estos ejemplos](../../cosmos-db/sql/sql-api-java-sdk-samples.md).

### <a name="javascript"></a>JavaScript

Inicialice el cliente de Cosmos DB:

```javascript
const client = new CosmosClient({ "<account-endpoint>", aadCredentials: new ManagedIdentityCredential() });
```

A continuación, lea y escriba los datos como se indica en [estos ejemplos](../../cosmos-db/sql/sql-api-nodejs-samples.md).

## <a name="clean-up-steps"></a>Pasos de limpieza

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. En el [portal](https://portal.azure.com), seleccione el recurso que quiere eliminar.

1. Seleccione **Eliminar**.

1. Cuando se le solicite, confirme la eliminación.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

Sección que se deja vacía a propósito

---

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las identidades administradas para recursos de Azure:

- [¿Qué son las identidades administradas de recursos de Azure?](overview.md)
- [Plantillas del Administrador de recursos de Azure](https://github.com/Azure/azure-quickstart-templates)

Más información sobre Azure Cosmos

- [Modelo de recursos de Azure Cosmos DB](../../cosmos-db/account-databases-containers-items.md)
- [Tutorial: Compilación de una aplicación de consola .NET para administrar datos de la cuenta de API de SQL de Azure Cosmos DB](../../cosmos-db/sql/sql-api-get-started.md)
