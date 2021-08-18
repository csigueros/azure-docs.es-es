---
title: Uso de identidades administradas para acceder a App Configuration
titleSuffix: Azure App Configuration
description: Autenticación en Azure App Configuration mediante identidades administradas
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: ff7c2b6ced87c8254283923a9163e51f06ae6ef6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114298180"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Uso de identidades administradas para acceder a App Configuration

Las [identidades administradas](../active-directory/managed-identities-azure-resources/overview.md) de Azure Active Directory simplifican la administración de secretos de su aplicación de nube. Con una identidad administrada, el código puede usar la entidad de servicio creada para el servicio de Azure donde se ejecuta. Usar una identidad administrada en lugar de una credencial diferente que se almacenan en Azure Key Vault o una cadena de conexión local.

Azure App Configuration y sus bibliotecas cliente .NET Core, .NET Framework y Java Spring incluyen compatibilidad integrada con la identidad administrada. Aunque su uso no es necesario, la identidad administrada elimina la necesidad de un token de acceso que contenga los secretos. El código solo puede acceder al almacén de App Configuration mediante el punto de conexión de servicio. Puede insertar esta dirección URL en el código directamente sin exponer ningún secreto.

En este artículo se muestra cómo puede aprovechar la identidad administrada para acceder a App Configuration. Se basa en la aplicación web que se introdujo en los inicios rápidos. Antes de continuar, primero [cree una aplicación ASP.NET Core con Azure App Configuration](./quickstart-aspnet-core-app.md).

> [!IMPORTANT]
> Una identidad administrada no se puede usar para autenticar aplicaciones que se ejecutan localmente. La aplicación debe implementarse en un servicio de Azure que admita identidades administradas. En este artículo se usa Azure App Service como ejemplo, pero el mismo concepto se aplica a cualquier otro servicio de Azure compatible con la identidad administrada, por ejemplo, [Azure Kubernetes Service](../aks/use-azure-ad-pod-identity.md), la [Máquina virtual de Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) y [Azure Container Instances](../container-instances/container-instances-managed-identity.md). Si la carga de trabajo está hospedada en uno de esos servicios, también puede aprovechar la compatibilidad con la identidad administrada del servicio.

Para realizar los pasos de este tutorial, puede usar cualquier editor de código. [Visual Studio Code](https://code.visualstudio.com/) es una excelente opción disponible en las plataformas Windows, macOS y Linux.

En este artículo aprenderá a:

> [!div class="checklist"]
> * Conceder a una identidad administrada acceso a App Configuration.
> * Configurar la aplicación para usar una identidad administrada al conectarse a App Configuration.


## <a name="prerequisites"></a>Requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

* [SDK de .NET Core](https://dotnet.microsoft.com/download).
* [Azure Cloud Shell configurado](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Agregar una identidad administrada

Para configurar una identidad administrada en el portal, primero crea una aplicación y luego habilita la característica.

1. Vaya al recurso App Services en [Azure Portal](https://portal.azure.com). Si no tiene un recurso App Services existente con el que trabajar, cree uno. 

1. Desplácese hacia abajo hasta el grupo **Configuración** en el panel de navegación izquierdo y seleccione **Identidad**.

1. En la pestaña **Asignado por el sistema**, cambie **Estado** a **Activado** y seleccione **Guardar**.

1. Responda **Sí** cuando se le pida que habilite la identidad administrada asignada por el sistema.

    ![Configurar la identidad administrada en App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Conceder acceso a App Configuration

1. En [Azure Portal](https://portal.azure.com), seleccione **Todos los recursos** y elija el almacén de App Configuration que creó en el inicio rápido.

1. Seleccione **Access Control (IAM)** .

1. En la pestaña **Comprobar el acceso**, seleccione **Agregar** en la interfaz de usuario de la tarjeta **Agregar una asignación de roles**.

1. En **Rol**, seleccione **Lector de los datos de App Configuration**. En **Asignar acceso**, seleccione **App Service** en **Identidad administrada asignada por el sistema**.

1. En **Suscripción**, seleccione su suscripción de Azure. Seleccione el recurso de App Service para la aplicación.

1. Seleccione **Guardar**.

    ![Agregar una identidad administrada](./media/add-managed-identity.png)


## <a name="use-a-managed-identity"></a>Uso de una identidad administrada

1. Agregue una referencia al paquete *Azure.Identity*.

    ```bash
    dotnet add package Azure.Identity
    ```

1. Busque el punto de conexión en el almacén de App Configuration. Esta dirección URL aparece en la pestaña **Claves de acceso** para el almacén en la Azure Portal.

1. Abra *appsettings.json* y agregue el siguiente script. Reemplace *\<service_endpoint>* , incluidos los corchetes, por la dirección URL del almacén de App Configuration.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Abra *Program.cs* y agregue una referencia a los espacios de nombres `Azure.Identity` y `Microsoft.Azure.Services.AppAuthentication`:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Si desea acceder solo a los valores almacenados directamente en App Configuration, actualice el método `CreateWebHostBuilder` reemplazando el método `config.AddAzureAppConfiguration()` (se encuentra en el paquete `Microsoft.Azure.AppConfiguration.AspNetCore`).

    > [!IMPORTANT]
    > `CreateHostBuilder` reemplaza a `CreateWebHostBuilder` en .NET Core 3.0.  Seleccione la sintaxis correcta en función de su entorno.

    ### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ---

    > [!NOTE]
    > En el caso de que quiera usar una **identidad administrada asignada por el usuario**, asegúrese de especificar el valor de clientId al crear [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential).
    >```csharp
    >config.AddAzureAppConfiguration(options =>
    >       {
    >           options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential("<your_clientId>"))
    >        });
    >```
    >Como se explica en las [preguntas más frecuentes sobre identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/known-issues.md), hay una manera predeterminada de resolver qué identidad administrada se usa. En este caso, la biblioteca de identidades de Azure le exige que especifique la identidad deseada para evitar posibles problemas de tiempo de ejecución en el futuro (por ejemplo, si se agrega una nueva identidad administrada asignada por el usuario o si está habilitada la identidad administrada asignada por el sistema). Por lo tanto, tendrá que especificar el valor de clientId incluso si solo se define una identidad administrada asignada por el usuario y no hay ninguna identidad administrada asignada por el sistema.
 
    

## <a name="deploy-your-application"></a>Implementación de aplicación

El uso de identidades administradas requiere que implemente la aplicación en un servicio de Azure. Las identidades administradas no se pueden usar para la autenticación de aplicaciones que se ejecutan localmente. Para implementar la aplicación de .NET Core que creó en el inicio rápido [Creación de una aplicación de ASP.NET Core con App Configuration](./quickstart-aspnet-core-app.md) y modificó para usar identidades administradas, siga las instrucciones que se indican en [Publicación de la aplicación web](../app-service/quickstart-dotnetcore.md?pivots=development-environment-vs&tabs=netcore31#publish-your-web-app).

Además de App Service, muchos otros servicios de Azure admiten identidades administradas. Para más información, vea [Servicios que admiten identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha agregado una identidad administrada de Azure para optimizar el acceso a App Configuration y mejorar la administración de credenciales de su aplicación. Para más información sobre App Configuration, continúe con los ejemplos de la CLI de Azure.

> [!div class="nextstepaction"]
> [Ejemplos de CLI](./cli-samples.md)