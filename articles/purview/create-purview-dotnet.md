---
title: 'Inicio rápido: Creación de una cuenta de Purview mediante el SDK de .NET'
description: Cree una cuenta de Azure Purview mediante el SDK de .NET.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/27/2021
ms.openlocfilehash: 0bbecc10139616bcf33f1d553536f3bfc674c5a1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215821"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Inicio rápido: Creación de una cuenta de Purview mediante el SDK de .NET

En esta guía de inicio rápido, usará el [SDK de .NET](/dotnet/api/overview/azure/purviewresourceprovider) para crear una cuenta de Azure Purview.

Azure Purview es un servicio de gobernanza de datos que le ayuda a administrar y gobernar el escenario de datos. Al conectarse a los datos de sus orígenes locales, multinube y de software como servicio (SaaS), Purview crea un mapa actualizado de la información. Identifica y clasifica los datos confidenciales y proporciona un linaje completo. Los consumidores de datos pueden descubrir datos en la organización y los administradores de datos pueden auditar, proteger y garantizar el uso correcto de los datos.

Para más información sobre Purview, [consulte nuestra página de información general](overview.md). Para más información sobre la implementación de Purview en la organización, [consulte nuestros procedimientos recomendados sobre implementación](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### <a name="visual-studio"></a>Visual Studio

En el tutorial de este artículo se usa Visual Studio 2019. Los procedimientos para Visual Studio 2013, 2015 o 2017 son ligeramente diferentes.

### <a name="azure-net-sdk"></a>SDK de .NET para Azure

Descargue e instale el [SDK de .NET para Azure](https://azure.microsoft.com/downloads/) en la máquina.

## <a name="create-an-application-in-azure-active-directory"></a>Creación de una aplicación en Azure Active Directory

1. En [Crear una aplicación de Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal), cree una aplicación que represente la aplicación .NET que se va a crear en este tutorial. Para la dirección URL de inicio de sesión, puede proporcionar una dirección URL ficticia, tal como se muestra en el artículo (`https://contoso.org/exampleapp`).
1. En [Obtener valores para iniciar sesión](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), obtenga el **identificador de aplicación** y el **identificador de inquilino** y tome nota de estos valores que se usa más adelante en este tutorial.
1. En [Certificados y secretos](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options), obtenga la **clave de autenticación** y tome nota de este valor que se usa más adelante en este tutorial.
1. En [Asignar la aplicación a un rol](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application), asigne la aplicación al rol **Colaborador** en el nivel de suscripción para que la aplicación pueda crear factorías de datos en la suscripción.

## <a name="create-a-visual-studio-project"></a>Creación de un proyecto de Visual Studio

A continuación, cree una aplicación de consola .NET de C# en Visual Studio:

1. Inicie **Visual Studio**.
2. En la ventana Inicio, seleccione **Crear un nuevo proyecto** > **Aplicación de consola (.NET Framework)** . Se requiere .NET versión 4.5.2 o posterior.
3. En **Nombre del proyecto**, escriba **PurviewQuickStart**.
4. Seleccione **Crear** para crear el proyecto.

## <a name="install-nuget-packages"></a>Instalación de paquetes NuGet

1. Seleccione **Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.
2. En el panel **Consola del Administrador de paquetes**, ejecute los comandos siguientes para instalar los paquetes. Para más información, consulte el [paquete NuGet Microsoft.Azure.Management.Purview](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Creación de un cliente de Purview

1. Abra **Program.cs** e incluya las siguientes instrucciones para agregar referencias a espacios de nombres.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Agregue el siguiente código al método **main** que define las variables. Reemplace los marcadores de posición por sus propios valores. Para encontrar una lista de las regiones de Azure en las que Purview está disponible actualmente, busque en **Azure Purview** y seleccione las regiones que le interesen en la página siguiente: [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/).

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Agregue el código siguiente al método **Main**, que crea una instancia de la clase **PurviewManagementClient**. Este objeto se usa para crear una cuenta de Purview.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Creación de una cuenta de Purview

Agregue el código siguiente al método **Main**, que crea una **cuenta de Purview**.

```csharp
// Create a purview Account
Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
Account account = new Account()
{
Location = region,
Identity = new Identity(type: "SystemAssigned"),
Sku = new AccountSku(name: "Standard", capacity: 4)
};            
try
{
  client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
  Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
}
catch (ErrorResponseModelException purviewException)
{
Console.WriteLine(purviewException.StackTrace);
  }
  Console.WriteLine(
    SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
  while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
         "PendingCreation")
  {
    System.Threading.Thread.Sleep(1000);
  }
Console.WriteLine("\nPress any key to exit...");
Console.ReadKey();
```

## <a name="run-the-code"></a>Ejecución del código

Compile e inicie la aplicación y, luego, compruebe la ejecución.

La consola imprime el progreso de la creación de la cuenta de Purview.

### <a name="sample-output"></a>Salida de ejemplo

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Comprobación del resultado

Vaya a la página **Purview accounts** (Cuentas de Purview) en [Azure Portal](https://portal.azure.com) y compruebe la cuenta creada con el código anterior.

## <a name="delete-purview-account"></a>Eliminación de la cuenta de Purview

Para eliminar una cuenta de Purview mediante programación, agregue las líneas de código siguientes al programa:

```csharp
Console.WriteLine("Deleting the Purview Account");
client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Comprobación de si el nombre de la cuenta de Purview está disponible

Para comprobar la disponibilidad de una cuenta Purview, use el código siguiente:

```csharp
CheckNameAvailabilityRequest checkNameAvailabilityRequest = newCheckNameAvailabilityRequest()
{
    Name = purviewAccountName,
    Type =  "Microsoft.Purview/accounts"
};
Console.WriteLine("Check Purview account name");
Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

El código anterior imprime "true" si el nombre está disponible y "false" si el nombre no está disponible.

## <a name="next-steps"></a>Pasos siguientes

Con el código de este tutorial se crea y elimina una cuenta de Purview y se comprueba la disponibilidad del nombre de la cuenta. Ahora puede descargar el SDK de .NET y conocer otras acciones del proveedor de recursos que puede realizar para una cuenta de Purview.

Consulte los artículos siguientes para aprender a navegar por Purview Studio, crear una colección y conceder acceso a Purview.

* [Uso de Purview Studio](use-purview-studio.md)
* [Crear una colección](quickstart-create-collection.md)
* [Adición de usuarios a una cuenta de Azure Purview](catalog-permissions.md)
