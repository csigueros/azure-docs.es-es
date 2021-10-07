---
title: Registro de errores y excepciones en MSAL.NET
titleSuffix: Microsoft identity platform
description: Aprenda a registrar errores y excepciones en MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 518fc85aff920ffece511e383b2322d8e81266ee
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091870"
---
# <a name="logging-in-msalnet"></a>Registro en MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Configuración del registro en MSAL.NET

En MSAL, el registro se establece en el momento de crear la aplicación mediante el modificador de generador `.WithLogging`. Este método toma parámetros opcionales:

- `Level` le permite decidir el nivel de registro deseado. Si se establece en Errors solo se reciben errores.
- `PiiLoggingEnabled` le permite registrar datos personales y organizativos si se establece en true. De forma predeterminada, se establece en false, por lo que la aplicación no registra datos personales.
- `LogCallback` establece en un delegado que realiza el registro. Si `PiiLoggingEnabled` se establece en true, este método recibirá mensajes que pueden tener datos personales, en cuyo caso la marca `containsPii` se establecerá en true.
- `DefaultLoggingEnabled` habilita el registro predeterminado para la plataforma. De forma predeterminada, es "false". Si se establece en true se usa el seguimiento de eventos en aplicaciones de escritorio o UWP, NSLog en iOS y logcat en Android.

```csharp
class Program
{
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
}
```

> [!TIP]
 > Consulte en la [wiki de MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) ejemplos del registro de MSAL.NET, entre otros temas.

## <a name="next-steps"></a>Pasos siguientes

Para ver más ejemplos de código, consulte [Ejemplos de código de la Plataforma de identidad de Microsoft](sample-v2-code.md).
