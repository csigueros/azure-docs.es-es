---
title: 'Tutorial de .NET Framework: Configuración dinámica de Azure App Configuration'
description: En este tutorial aprenderá a actualizar dinámicamente los datos de configuración de aplicaciones de .NET Framework mediante Azure App Configuration.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: d6c3bc0ee45c214419820208598e5721309ea144
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261051"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Tutorial: Uso de la configuración dinámica en una aplicación de .NET Framework

Los datos de App Configuration pueden cargarse como configuración de aplicación en una aplicación de .NET Framework. Para obtener más información, consulte el [inicio rápido](./quickstart-dotnet-app.md). Sin embargo, según el diseño de .NET Framework, la configuración de la aplicación solo se puede actualizar tras el reinicio de la aplicación. El proveedor .NET de App Configuration es una biblioteca .NET Standard. Admite el almacenamiento en caché y la actualización dinámica de la configuración sin reiniciar la aplicación. Este tutorial le muestra cómo puede implementar las actualizaciones de configuración dinámica en una aplicación de consola de .NET Framework.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar la aplicación de .NET Framework para actualizar su configuración en respuesta a los cambios en un almacén de App Configuration.
> * Insertar la configuración más reciente en la aplicación.

## <a name="prerequisites"></a>Prerrequisitos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
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

## <a name="reload-data-from-app-configuration"></a>Recarga de datos de App Configuration
1. Haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**. En la pestaña **Examinar**, busque y agregue la versión más reciente del siguiente paquete NuGet al proyecto.

   *Microsoft.Extensions.Configuration.AzureAppConfiguration*

1. Abra el archivo *Program.cs* y agregue los siguientes espacios de nombres.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Agregue dos variables para almacenar los objetos relacionados con la configuración.

    ```csharp
    private static IConfiguration _configuration;
    private static IConfigurationRefresher _refresher;
    ```

1. Actualice el método `Main` para conectarse a App Configuration con las opciones de actualización especificadas.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    // Load all keys that start with `TestApp:`.
                    .Select("TestApp:*")
                    // Configure to reload the key 'TestApp:Settings:Message' if it is modified.
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```

    En el método `ConfigureRefresh`, se registra una clave dentro del almacén de App Configuration para la supervisión de cambios. El método `Register` tiene un parámetro booleano `refreshAll` opcional que se puede usar para indicar si se deben actualizar todos los valores de configuración si cambia la clave registrada. En este ejemplo, solo se actualizará la clave *TestApp:Settings:Message*. El método `SetCacheExpiration` especifica el tiempo mínimo que debe transcurrir antes de que se realice una nueva solicitud a App Configuration para comprobar si hay cambios de configuración. En este ejemplo, se reemplaza el tiempo de expiración predeterminado de 30 segundos y se especifica un tiempo de 10 minutos en su lugar para fines de demostración.

1. Agregue un método denominado `PrintMessage()` que desencadene una actualización de los datos de configuración de App Configuration.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

    La llamada al método `ConfigureRefresh` por sí sola no hará que la configuración se actualice automáticamente. El método `TryRefreshAsync` se llama desde la interfaz `IConfigurationRefresher` para desencadenar una actualización. Esto es así para evitar que se envíen solicitudes fantasma a App Configuration incluso cuando la aplicación está inactiva. Puede incluir la llamada a `TryRefreshAsync` donde considere que la aplicación está activa. Por ejemplo, puede ser cuando se procesa un mensaje entrante, un pedido o una iteración de una tarea compleja. También puede ser en un temporizador si la aplicación está activa todo el tiempo. En este ejemplo, llamará a `TryRefreshAsync` cuando presione la tecla Entrar. Tenga en cuenta que, aunque se produzca un error en la llamada a `TryRefreshAsync` por cualquier motivo, la aplicación seguirá usando la configuración almacenada en caché. Se realizará otro intento cuando haya pasado el tiempo de expiración de la caché configurada y la actividad de la aplicación vuelva a desencadenar la llamada a `TryRefreshAsync`. Llamar a `TryRefreshAsync` es una operación sin efecto antes de que pase el tiempo de expiración de la caché configurada, por lo que su impacto en el rendimiento es mínimo, aunque se llame con frecuencia.

## <a name="build-and-run-the-app-locally"></a>Compilación y ejecución de la aplicación en un entorno local

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

1. Presione Ctrl + F5 para compilar y ejecutar la aplicación de consola.

    ![Inicio local de la aplicación](./media/dotnet-app-run.png)

1. En Azure Portal, vaya al **Explorador de configuración** del almacén de App Configuration y actualice el valor de las claves siguientes.

    | Key                        | Value                                         |
    |----------------------------|-----------------------------------------------|
    | *TestApp:Settings:Message* | *Datos de Azure App Configuration, actualizados* |

1. De vuelta en la aplicación en ejecución, presione la tecla Entrar para desencadenar una actualización e imprimir el valor actualizado en la ventana del símbolo del sistema o de PowerShell.

    ![Actualización local de la aplicación](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Dado que el tiempo de expiración de la caché se estableció en 10 segundos mediante el método `SetCacheExpiration` al especificar la configuración para la operación de actualización, el valor de la configuración solo se actualizará si han transcurrido al menos 10 segundos desde la última actualización de dicha configuración.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha habilitado la aplicación de .NET Framework para actualizar dinámicamente la configuración a partir de App Configuration. Para obtener información sobre cómo habilitar la configuración dinámica en una aplicación web de ASP.NET (.NET Framework), continúe con el siguiente tutorial:

> [!div class="nextstepaction"]
> [Habilitar la configuración dinámica en ASP.NET Web Applications](./enable-dynamic-configuration-aspnet-netfx.md)

Para aprender a usar una identidad administrada de Azure para simplificar el acceso a App Configuration, vaya al siguiente tutorial:

> [!div class="nextstepaction"]
> [Integración de identidades administradas](./howto-integrate-azure-managed-service-identity.md)
