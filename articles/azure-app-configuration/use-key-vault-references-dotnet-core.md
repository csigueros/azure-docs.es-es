---
title: Tutorial sobre el uso de las referencias de Key Vault de Azure App Configuration en una aplicación de ASP.NET Core | Microsoft Docs
description: En este tutorial, aprenderá a usar las referencias de Key Vault de Azure App Configuration desde una aplicación de ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 9f095bbadf8f395b809d46c8beea5f6665932d12
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357900"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Uso de referencias de Key Vault en una aplicación de ASP.NET Core

En este tutorial aprenderá a usar el servicio Azure App Configuration junto con Azure Key Vault. App Configuration y Key Vault son servicios complementarios que se usan de forma conjunta en la mayoría de las implementaciones de aplicaciones.

App Configuration ayuda a usar juntos los servicios, mediante la creación de claves que hacen referencia a valores almacenados en Key Vault. Cuando App Configuration crea estas claves, almacena los URI de los valores de Key Vault, en lugar de los propios valores.

La aplicación usa el proveedor de cliente de App Configuration para recuperar las referencias de Key Vault, tal como hace para cualquier otra clave almacenada en App Configuration. En este caso, los valores almacenados en App Configuration son los URI que hacen referencia a los valores de Key Vault. No son valores ni credenciales de Key Vault. Dado que el proveedor de cliente reconoce las claves como referencias de Key Vault, utiliza Key Vault para recuperar sus valores.

La aplicación es responsable de autenticarse correctamente en App Configuration y en Key Vault. Los dos servicios no se comunican directamente.

En este tutorial se muestra cómo se implementan las referencias de Key Vault en el código. Se basa en la aplicación web que se introdujo en los inicios rápidos. Antes de continuar, finalice primero el tutorial [Creación de una aplicación ASP.NET Core con Azure App Configuration](./quickstart-aspnet-core-app.md).

Para realizar los pasos de este tutorial, puede usar cualquier editor de código. Por ejemplo, [Visual Studio Code](https://code.visualstudio.com/) es un editor de código multiplataforma que está disponible para los sistemas operativos Windows, macOS y Linux.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una clave de App Configuration que hace referencia a un valor almacenado en Key Vault.
> * Acceder al valor de esta clave desde una aplicación web de ASP.NET Core.

## <a name="prerequisites"></a>Prerrequisitos

Antes de iniciar este tutorial, instale el [SDK de .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Creación de un almacén

1. Seleccione la opción **Crear un recurso** en la esquina superior izquierda de Azure Portal:

    ![Captura de pantalla que muestra la opción Crear un recurso de Azure Portal.](./media/quickstarts/search-services.png)
1. En el cuadro de búsqueda, escriba **Key Vault** y seleccione **Key Vault** en la lista desplegable.
1. En la lista de resultados, seleccione **Key Vaults** a la izquierda.
1. En **Key Vaults**, seleccione **Agregar**.
1. En la sección **Crear almacén de claves**, a la derecha, proporcione la siguiente información:
    - Seleccione **Suscripción** para elegir una suscripción.
    - En **Grupo de recursos**, escriba el nombre de un grupo de recursos existente o seleccione **Crear nuevo** y escriba un nombre de grupo de recursos.
    - En **Nombre del almacén de claves** se requiere un nombre único.
    - En la lista desplegable **Región**, elija una ubicación.
1. Deje las demás opciones de **Crear almacén de claves** con sus valores predeterminados.
1. Haga clic en **Revisar + crear**.
1. El sistema validará y mostrará los datos que ha escrito. Haga clic en **Crear**.

En este momento, su cuenta de Azure es la única autorizada para acceder a este nuevo almacén.


## <a name="add-a-secret-to-key-vault"></a>Incorporación de un secreto a Key Vault

Para agregar un secreto al almacén, debe llevar a cabo algunos pasos adicionales. En este caso, agregará un mensaje que podrá usar para probar la recuperación de Key Vault. El mensaje se denominará **Message** y en él se almacenará el valor "Hello from Key Vault".

1. En las páginas de propiedades de Key Vault, seleccione **Secretos**.
1. Seleccione **Generar o importar**.
1. En el panel **Crear un secreto**, escriba los valores siguientes:
    - **Opciones de carga**: escriba **Manual**.
    - **Name**: escriba **Message**.
    - **Valor**: escriba **Hello from Key Vault**.
1. Deje las demás propiedades de **Crear un secreto** con sus valores predeterminados.
1. Seleccione **Crear**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Incorporación de una referencia de Key Vault a App Configuration

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Todos los recursos** y después seleccione la instancia de almacén de App Configuration que creó en el inicio rápido.

1. Seleccione **Explorador de configuración**.

1. Seleccione **+ Crear** > **Referencia del almacén de claves** y, a continuación, especifique los valores siguientes:
    - **Clave**: seleccione **TestApp:Settings:KeyVaultMessage**.
    - **Etiqueta**: deje este valor en blanco.
    - **Suscripción**, **Grupo de recursos** y **Key Vault**: escriba los valores correspondientes a los del almacén de claves que creó en la sección anterior.
    - **Secreto**: seleccione el secreto llamado **Message** que creó en la sección anterior.

![Captura de pantalla de la creación de un formulario de referencia de Key Vault](./media/create-key-vault-reference.png)

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Actualización del código para usar una referencia de Key Vault

1. Para agregar una referencia a los paquetes de NuGet necesarios, ejecute el siguiente comando:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Abra *Program.cs* y agregue referencias a los siguientes paquetes necesarios:

    ```csharp
    using Azure.Identity;
    ```

1. Actualice el método `CreateWebHostBuilder` para usar App Configuration; para ello, llame al método `config.AddAzureAppConfiguration`. Incluya la opción `ConfigureKeyVault` y pase las credenciales correctas a Key Vault.

     #### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

1. Al inicializar la conexión a App Configuration, configura la conexión a Key Vault llamando al método `ConfigureKeyVault`. Después de la inicialización, puede acceder a los valores de las referencias de Key Vault de la misma manera que accede a los valores de las claves normales de App Configuration.

    Para ver este proceso en acción, abra *Index.cshtml* en la carpeta **Views** > **Home**. Reemplace su contenido por el código siguiente:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Puede acceder al valor de la referencia de Key Vault **TestApp:Settings:KeyVaultMessage** de la misma forma que al valor de configuración **TestApp:Settings:Message**.


## <a name="grant-your-app-access-to-key-vault"></a>Concesión a la aplicación de acceso a Key Vault

Azure App Configuration no accederá al almacén de claves. La aplicación leerá directamente del almacén de claves, por lo que tendrá que conceder a la aplicación acceso de lectura a los secretos del almacén de claves. De este modo, el secreto siempre permanece con la aplicación. El acceso se puede conceder mediante una [directiva de acceso de Key Vault](../key-vault/general/assign-access-policy-portal.md) o el [control de acceso basado en rol de Azure](../key-vault/general/rbac-guide.md).

En el código anterior se usa `DefaultAzureCredential`. Se trata de una credencial de token agregada que prueba de forma automática varios tipos de credenciales, como `EnvironmentCredential`, `ManagedIdentityCredential`, `SharedTokenCacheCredential` y `VisualStudioCredential`. Para obtener más información, vea [DefaultAzureCredential (Clase)](/dotnet/api/azure.identity.defaultazurecredential). Puede reemplazar `DefaultAzureCredential` por cualquier tipo de credencial explícitamente. Pero el uso de `DefaultAzureCredential` permite tener el mismo código que se ejecuta en entornos locales y de Azure. Por ejemplo, concede a la credencial propia acceso al almacén de claves. `DefaultAzureCredential` vuelve automáticamente a `SharedTokenCacheCredential` o `VisualStudioCredential` cuando se usa Visual Studio para el desarrollo local.

Como alternativa, puede establecer las variables de entorno AZURE_TENANT_ID, AZURE_CLIENT_ID y AZURE_CLIENT_SECRET, y `DefaultAzureCredential` usará el secreto de cliente que tiene por medio de `EnvironmentCredential` para autenticarse con el almacén de claves. Después de implementar la aplicación en un servicio de Azure con la identidad administrada habilitada, como Azure App Service, Azure Kubernetes Service o Azure Container Instance, conceda a la identidad administrada del servicio de Azure permiso para acceder al almacén de claves. `DefaultAzureCredential` usa automáticamente `ManagedIdentityCredential` cuando la aplicación se ejecuta en Azure. Puede usar la misma identidad administrada para autenticarse con App Configuration y Key Vault. Para obtener más información, vea [Uso de identidades administradas para acceder a App Configuration](howto-integrate-azure-managed-service-identity.md).

## <a name="build-and-run-the-app-locally"></a>Compilación y ejecución de la aplicación en un entorno local

1. Para compilar la aplicación mediante la CLI de .NET Core, ejecute el siguiente comando en el shell de comandos:

    ```dotnetcli
    dotnet build
    ```

1. Una vez finalizada la compilación, use el siguiente comando para ejecutar la aplicación web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Inicie una ventana del explorador y vaya a `http://localhost:5000`, que es la dirección URL predeterminada de la aplicación web hospedada localmente.

    ![Inicio de la aplicación local del inicio rápido](./media/key-vault-reference-launch-local.png)



## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha creado una clave de App Configuration que hace referencia a un secreto almacenado en Key Vault.
Para obtener información sobre cómo volver a cargar automáticamente secretos y certificados desde Key Vault, continúe con el siguiente tutorial:

> [!div class="nextstepaction"]
> [Volver a cargar secretos y certificados desde Key Vault automáticamente](./reload-key-vault-secrets-dotnet.md)

Para obtener información sobre cómo usar Identidad administrada para simplificar el acceso a App Configuration y Key Vault, consulte el siguiente tutorial:

> [!div class="nextstepaction"]
> [Integración de identidades administradas](./howto-integrate-azure-managed-service-identity.md)
