---
title: Inicio rápido de Azure App Configuration con .NET Framework | Microsoft Docs
description: En este artículo se crea una aplicación de .NET Framework con Azure App Configuration para centralizar el almacenamiento y la administración de la configuración de la aplicación de forma independiente del código.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: f5bd0809de5aa7e7e585662ee0c98feaf9be2e79
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245102"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Inicio rápido: Creación de una aplicación de .NET Framework con Azure App Configuration

Hay dos maneras de incorporar Azure App Configuration en una aplicación basada en .NET Framework.
- El generador de configuración para App Configuration permite que los datos de App Configuration se carguen en Configuración de aplicaciones. La aplicación accede a la configuración como siempre lo hace a través de `ConfigurationManager`. No es necesario realizar ningún cambio de código que no sean las actualizaciones de los archivos de *app.config* o *web.config*. Esta guía de inicio rápido le guiará a través de esta opción.
- Tal y como está diseñado en .NET Framework, la configuración de la aplicación solo se puede actualizar tras el reinicio de la aplicación. El proveedor .NET de App Configuration es una biblioteca .NET Standard. Admite el almacenamiento en caché y la actualización dinámica de la configuración sin reiniciar la aplicación. Si la configuración dinámica es esencial para usted y está dispuesto a realizar cambios en el código, consulte los tutoriales sobre cómo implementar actualizaciones de configuración dinámica en una aplicación de consola de [.NET Framework](./enable-dynamic-configuration-dotnet.md) o en una [aplicación web de ASP.NET](./enable-dynamic-configuration-aspnet-netfx.md).

En este inicio rápido, se usa una aplicación de consola .NET Framework como ejemplo, pero la misma técnica se usa para una aplicación ASP.NET Web Forms o MVC.

## <a name="prerequisites"></a>Prerrequisitos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/dotnet)
- [Visual Studio](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2 o una versión posterior](https://dotnet.microsoft.com/download/dotnet-framework)

## <a name="create-an-app-configuration-store"></a>Creación de un almacén de App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Seleccione **Explorador de configuración** >  **+ Crear** > **Clave-valor** para agregar los siguientes clave-valor:

    | Key                        | Value                               |
    |----------------------------|-------------------------------------|
    | *TestApp:Settings:Message* | *Datos de Azure App Configuration* |

    Deje **Etiqueta** y **Tipo de contenido** vacíos.

## <a name="create-a-net-framework-console-app"></a>Creación de una aplicación de consola de .NET Framework

1. Inicie Visual Studio y seleccione **Crear un proyecto**.

1. En **Crear un proyecto**, filtre por el tipo de proyecto **Consola** y seleccione **Aplicación de consola (.NET Framework)** con C# de la lista de plantillas de proyecto. Presione **Siguiente**.

1. En **Configurar el nuevo proyecto**, asígnele al proyecto un nombre. En **Marco**, seleccione **.NET Framework 4.7.2** o una versión posterior. Pulse **Crear**.

## <a name="connect-to-an-app-configuration-store"></a>Conexión a un almacén de App Configuration

1. Haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**. En la pestaña **Examinar**, busque y agregue los siguientes paquetes NuGet al proyecto.

    - Versión 1.0.0 o posterior de *Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration*
    - Versión 2.0.0 o posterior de *Microsoft.Configuration.ConfigurationBuilders.Environment*
    - Versión 4.6.0 o posterior de *System.Configuration.ConfigurationManager*

1. Actualice el archivo *App.config* del proyecto como sigue:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   La cadena de conexión del almacén de App Configuration se lee en la variable de entorno `ConnectionString`. Agregue el generador de configuración `Environment` antes de `MyConfigStore` en la propiedad `configBuilders` de la sección `appSettings`.

1. Abra *Program.cs* y actualice el método `Main` para usar App Configuration mediante una llamada a `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app"></a>Compilación y ejecución de la aplicación

1. Establezca una variable de entorno llamada **ConnectionString** en la cadena de conexión de clave de solo lectura obtenida durante la creación del almacén de App Configuration. 

    Si usa el símbolo del sistema de Windows, ejecute el siguiente comando:
    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si usa Windows PowerShell, ejecute el siguiente comando:
    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Reinicie Visual Studio para permitir que el cambio surta efecto. 

1. Presione Ctrl + F5 para compilar y ejecutar la aplicación de consola. Debería ver el mensaje de las salidas de App Configuration en la consola.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado un almacén de App Configuration y lo ha usado con una aplicación de consola de .NET Framework. Para aprender a habilitar la aplicación de .NET Framework para actualizar dinámicamente la configuración, vaya a los siguientes tutoriales.

> [!div class="nextstepaction"]
> [Habilitación de la configuración dinámica en una aplicación de .NET Framework](./enable-dynamic-configuration-dotnet.md)

> [!div class="nextstepaction"]
> [Habilitación de la configuración dinámica en la aplicación web de ASP.NET](./enable-dynamic-configuration-aspnet-netfx.md)
