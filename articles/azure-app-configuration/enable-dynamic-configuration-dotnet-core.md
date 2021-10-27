---
title: 'Tutorial: Uso de la configuración dinámica en una aplicación de .NET Core'
titleSuffix: Azure App Configuration
description: En este tutorial aprenderá a actualizar dinámicamente los datos de configuración de aplicaciones de .NET Core
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: jken
ms.openlocfilehash: 9a2a8f88cd39b452a54d6fc668737fc1b5be68cc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996974"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Tutorial: Uso de la configuración dinámica en una aplicación de .NET Core

La biblioteca de proveedores .NET de App Configuration admite la actualización de la configuración a petición sin provocar el reinicio de una aplicación. Este tutorial le muestra cómo puede implementar las actualizaciones de configuración dinámica en el código. Se basa en la aplicación que se introdujo en el inicio rápido. Antes de continuar, debe finalizar el tutorial [Creación de una aplicación .NET Core con Azure App Configuration](./quickstart-dotnet-core-app.md).

Para realizar los pasos de este tutorial, puede usar cualquier editor de código. [Visual Studio Code](https://code.visualstudio.com/) es una excelente opción que está disponible en las plataformas Windows, macOS y Linux.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar la aplicación de .NET Core para actualizar su configuración en respuesta a los cambios en un almacén de App Configuration.
> * Consuma en la aplicación la configuración más reciente.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Finalice el inicio rápido [Creación de una aplicación .NET Core con Azure App Configuration](./quickstart-dotnet-core-app.md).

## <a name="activity-driven-configuration-refresh"></a>Actualización de configuración por actividad

Abra *Program.cs* y actualice el código de la siguiente manera.

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private static IConfiguration _configuration = null;
        private static IConfigurationRefresher _refresher = null;

        static void Main(string[] args)
        {
            var builder = new ConfigurationBuilder();
            builder.AddAzureAppConfiguration(options =>
            {
                options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
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

        private static async Task PrintMessage()
        {
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

            // Wait for the user to press Enter
            Console.ReadLine();

            await _refresher.TryRefreshAsync();
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
        }
    }
}
```

En el método `ConfigureRefresh`, se registra una clave dentro del almacén de App Configuration para la supervisión de cambios. El método `Register` tiene un parámetro booleano `refreshAll` opcional que se puede usar para indicar si se deben actualizar todos los valores de configuración si cambia la clave registrada. En este ejemplo, solo se actualizará la clave *TestApp:Settings:Message*. El método `SetCacheExpiration` especifica el tiempo mínimo que debe transcurrir antes de que se realice una nueva solicitud a App Configuration para comprobar si hay cambios de configuración. En este ejemplo, se reemplaza el tiempo de expiración predeterminado de 30 segundos y se especifica un tiempo de 10 minutos en su lugar para fines de demostración.

La llamada al método `ConfigureRefresh` por sí sola no hará que la configuración se actualice automáticamente. El método `TryRefreshAsync` se llama desde la interfaz `IConfigurationRefresher` para desencadenar una actualización. Esto es así para evitar que se envíen solicitudes fantasma a App Configuration incluso cuando la aplicación está inactiva. Es conveniente incluir la llamada a `TryRefreshAsync` donde considere que la aplicación está activa. Por ejemplo, puede ser cuando se procesa un mensaje entrante, un pedido o una iteración de una tarea compleja. También puede ser en un temporizador si la aplicación está activa todo el tiempo. En este ejemplo, llamará a `TryRefreshAsync` cada vez que presione la tecla Entrar. Tenga en cuenta que, aunque se produzca un error en la llamada a `TryRefreshAsync` por cualquier motivo, la aplicación seguirá usando la configuración almacenada en caché. Se realizará otro intento cuando haya pasado el tiempo de expiración de la caché configurada y la actividad de la aplicación vuelva a desencadenar la llamada a `TryRefreshAsync`. Llamar a `TryRefreshAsync` es una operación sin efecto antes de que pase el tiempo de expiración de la caché configurada, por lo que su impacto en el rendimiento es mínimo, aunque se llame con frecuencia.

## <a name="build-and-run-the-app-locally"></a>Compilación y ejecución de la aplicación en un entorno local

1. Establezca una variable de entorno llamada **ConnectionString** y defínala como la clave de acceso a su almacén de App Configuration. Si usa el símbolo del sistema de Windows, ejecute el siguiente comando y reinícielo para que se aplique el cambio:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si usa Windows PowerShell, ejecute el siguiente comando:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Si usa macOS o Linux, ejecute el siguiente comando:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Ejecute el siguiente comando para compilar la aplicación de consola:

    ```console
     dotnet build
    ```

1. Una vez que la compilación se haya realizado correctamente, ejecute el siguiente comando para ejecutar la aplicación localmente:

    ```console
     dotnet run
    ```

    ![Inicio de la aplicación del artículo de inicio rápido en un entorno local](./media/quickstarts/dotnet-core-app-run.png)

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Todos los recursos** y seleccione la instancia de almacén de App Configuration que creó en el inicio rápido.

1. Seleccione **Explorador de configuración** y actualice los valores de las claves siguientes:

    | Clave | Value |
    |---|---|
    | TestApp:Settings:Message | Datos de Azure App Configuration, actualizados |

1. Presione la tecla Entrar para desencadenar una actualización e imprimir el valor actualizado en la ventana del símbolo del sistema o de PowerShell.

    ![Inicio rápido de actualización de la aplicación en el entorno local](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Dado que el tiempo de expiración de la caché se estableció en 10 segundos mediante el método `SetCacheExpiration` al especificar la configuración para la operación de actualización, el valor de la configuración solo se actualizará si han transcurrido al menos 10 segundos desde la última actualización de dicha configuración.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha habilitado la aplicación de .NET Core para actualizar dinámicamente la configuración a partir de App Configuration. Para obtener información sobre cómo usar una identidad administrada de Azure para simplificar el acceso a App Configuration, vaya al siguiente tutorial.

> [!div class="nextstepaction"]
> [Integración de identidades administradas](./howto-integrate-azure-managed-service-identity.md)
