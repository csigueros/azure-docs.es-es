---
title: Adquisición de un token para llamar a una API web mediante el administrador de cuentas web (aplicación de escritorio) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo crear una aplicación de escritorio que llame a las API web para adquirir un token para la aplicación mediante el administrador de cuentas web.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: a6b57ac40c124b2290642e452ef9c9f294acc9da
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837917"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-wam"></a>Aplicación de escritorio que llama a las API web: adquisición de un token mediante el Administrador de cuentas web

MSAL puede llamar al Administrador de cuentas web, un componente de Windows 10 que se distribuye con el sistema operativo. Este componente actúa como agente de autenticación y los usuarios de la aplicación se benefician de la integración con cuentas conocidas de Windows, como la cuenta con la que ha iniciado sesión en la sesión de Windows.

## <a name="availability"></a>Disponibilidad

MSAL 4.25+ admite WAM en UWP, .NET Classic, .NET Core 3.x y .NET 5.

La funcionalidad de WAM es totalmente compatible con .NET Classic y .NET Core 3.x, pero tiene que agregar una referencia al paquete [Microsoft.Identity.Client.Desktop](https://www.nuget.org/packages/Microsoft.Identity.Client.Desktop/) junto con MSAL y, en lugar de `WithBroker()`, llamar a `.WithWindowsBroker()`.

Para .NET 5, seleccione como destino `net5.0-windows10.0.17763.0` (o superior) y no solo `net5.0`. La aplicación todavía se ejecutará en versiones anteriores de Windows si agrega `<SupportedOSPlatformVersion>7</SupportedOSPlatformVersion>` a csproj. MSAL usará un explorador cuando WAM no esté disponible.

## <a name="wam-value-proposition"></a>Propuesta de valor de WAM

El uso de un agente de autenticación como WAM tiene numerosas ventajas.

- Seguridad mejorada (la aplicación no tiene que administrar el potente token de actualización)
- Mejor compatibilidad para las claves FIDO, el acceso condicional y Windows Hello
- Integración con la vista "Correo electrónico y cuentas" de Windows
- Mejor inicio de sesión único (los usuarios no tienen que volver a escribir las contraseñas)
- La mayoría de mejoras y correcciones de errores se incluirán con Windows

## <a name="wam-limitations"></a>Limitaciones de WAM

- No se admiten las autoridades de B2C.
- Disponible en Win10, Win Server 2016 y Win Server 2019. En Mac, Linux y versiones anteriores de Windows, MSAL recurrirá a un explorador.

## <a name="wam-calling-pattern"></a>Patrón de llamada de WAM

Puede emplear el siguiente patrón para usar WAM.

```csharp
// 1. Configuration - read below about redirect URI
var pca = PublicClientApplicationBuilder.Create("client_id")
              .WithBroker()
              .Build();

// Add a token cache, see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-net-token-cache-serialization?tabs=desktop

// 2. GetAccounts
var accounts = await pca.GetAccountsAsync();
var accountToLogin = // choose an account, or null, or use PublicClientApplication.OperatingSystemAccount for the default OS account

try
{
    // 3. AcquireTokenSilent 
    var authResult = await pca.AcquireTokenSilent(new[] { "User.Read" }, accountToLogin)
                              .ExecuteAsync();
}
catch (MsalUiRequiredException) // no change in the pattern
{
    // 4. Specific: Switch to the UI thread for next call . Not required for console apps.
    await SwitchToUiThreadAsync(); // not actual code, this is different on each platform / tech

    // 5. AcquireTokenInteractive
    var authResult = await pca.AcquireTokenInteractive(new[] { "User.Read" })
                              .WithAccount(accountToLogin)  // this already exists in MSAL, but it is more important for WAM
                              .WithParentActivityOrWindow(myWindowHandle) // to be able to parent WAM's windows to your app (optional, but highly recommended; not needed on UWP)
                              .ExecuteAsync();
}
```

Llame a `.WithBroker(true)`. Si un agente no está presente (por ejemplo, Win8.1, Mac o Linux), MSAL recurrirá a un explorador. Las reglas del URI de redireccionamiento se aplican al explorador.

## <a name="redirect-uri"></a>URI de redireccionamiento

No es necesario configurar los URI de redireccionamiento de WAM en MSAL, pero deben configurarse en el registro de la aplicación.

### <a name="win32-net-framework--net-5"></a>Win32 (.NET Framework/.NET 5)

```
ms-appx-web://microsoft.aad.brokerplugin/{client_id}
```

### <a name="uwp"></a>UWP
```csharp
            // returns smth like S-1-15-2-2601115387-131721061-1180486061-1362788748-631273777-3164314714-2766189824
            string sid = WebAuthenticationBroker.GetCurrentApplicationCallbackUri().Host.ToUpper();

            // the redirect uri you need to register
            string redirectUri = $"ms-appx-web://microsoft.aad.brokerplugin/{sid}";
```

## <a name="token-cache-persistence"></a>Persistencia de la memoria caché de tokens

Es importante conservar la memoria caché de tokens de MSAL porque MSAL debe guardar allí los identificadores de cuenta de WAM internos. Sin ella, reiniciar la aplicación significa que la API `GetAccounts` perderá algunas de las cuentas. Tenga en cuenta que en la UWP, MSAL sabe dónde guardar la memoria caché de tokens.

## <a name="getaccounts"></a>GetAccounts

`GetAccounts` devuelve las cuentas de los usuarios que han iniciado sesión previamente de forma interactiva en la aplicación.

Además de esto, WAM puede enumerar las cuentas profesionales y educativas de todo el sistema operativo configuradas en Windows (para aplicaciones Win32, pero no para aplicaciones UWP). Para adoptar esta característica, establezca `ListWindowsWorkAndSchoolAccounts` en `WindowsBrokerOptions` a **true**. Puede habilitarla como se indica a continuación.

```csharp
.WithWindowsBrokerOptions(new WindowsBrokerOptions()
{
    // GetAccounts will return Work and School accounts from Windows
    ListWindowsWorkAndSchoolAccounts = true,

    // Legacy support for 1st party apps only
    MsaPassthrough = true
})
```

>[!NOTE]
> Las cuentas de Microsoft (es decir, outlook.com, etc.) no aparecerán en Win32 ni en UWP por motivos de privacidad.

Las aplicaciones no pueden quitar cuentas de Windows. 

## <a name="removeasync"></a>RemoveAsync

- Quita toda la información de la cuenta de la memoria caché de tokens de MSAL (esto incluye MSA, es decir, cuentas personales, información de la cuenta y otra información de la cuenta copiada por MSAL en su memoria caché).
- Quita las cuentas de solo aplicación (no de todo el sistema operativo).

>[!NOTE]
> Las aplicaciones no pueden quitar cuentas de sistema operativo. Solo los usuarios pueden hacerlo. Si se pasa una cuenta de sistema operativo a `RemoveAsync` y después se llama a `GetAccounts` con `ListWindowsWorkAndSchoolAccounts` habilitado, se devolverá la misma cuenta de sistema operativo.

## <a name="other-considerations"></a>Otras consideraciones

- Las operaciones interactivas de WAM deben estar en el subproceso de interfaz de usuario. MSAL produce una excepción significativa cuando no está en el subproceso de interfaz de usuario. Esto NO se aplica a las aplicaciones de consola.
- `WithAccount` proporciona una experiencia de autenticación acelerada si la cuenta de MSAL se obtuvo originalmente a través de WAM, o bien si WAM puede encontrar una cuenta profesional y educativa en Windows.
- WAM no puede rellenar previamente el campo de nombre de usuario con una sugerencia de inicio de sesión, a menos que se encuentre una cuenta profesional y educativa con el mismo nombre de usuario en Windows.
- Si WAM no puede ofrecer una experiencia de autenticación acelerada, mostrará un selector de cuenta. Los usuarios pueden agregar nuevas cuentas.

!["Selector de cuenta de WAM"](media/scenario-desktop-acquire-token-wam/wam-account-picker.png)

- Windows recuerda automáticamente las cuentas nuevas. En las cuentas profesionales y educativas se puede decidir entre unirse o no al directorio de la organización. En caso de no hacerlo, la cuenta no aparecerá en "Correo electrónico y cuentas". Las cuentas de Microsoft se agregan automáticamente a Windows. Las aplicaciones no pueden enumerar estas cuentas mediante programación (solo a través del selector de cuentas).

## <a name="troubleshooting"></a>Solución de problemas

Cuando una aplicación que usa MSAL se ejecuta como un proceso con privilegios elevados, algunas de estas llamadas dentro de WAM pueden producir un error debido a los distintos niveles de seguridad del proceso. Internamente, MSAL.NET usa métodos nativos de Windows ([COM](/windows/win32/com/the-component-object-model)) para integrarse con WAM. A partir de la versión 4.32.0, MSAL mostrará un mensaje de error descriptivo cuando detecte que el proceso de la aplicación tiene privilegios elevados y WAM no devuelve ninguna cuenta.

Una solución es no ejecutar la aplicación con privilegios elevados, si es posible. Otra posible solución alternativa es llamar al método `WindowsNativeUtils.InitializeProcessSecurity` cuando se inicia la aplicación. Esto establecerá la seguridad de los procesos usados por WAM en los mismos niveles. Consulte [esta aplicación](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/devapps/WAM/NetCoreWinFormsWam/Program.cs#L18-L21) a modo de ejemplo. Pero tenga en cuenta que no se garantiza que esta solución alternativa se realice correctamente debido a factores externos como el comportamiento CLR subyacente. En ese caso, se producirá una excepción `MsalClientException`. Consulte la incidencia [#2560](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/2560) para obtener más información.

## <a name="sample"></a>Muestra

[Ejemplo de WPF que usa WAM](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2)

[Ejemplo de UWP que usa WAM, junto con Xamarin](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/2-With-broker)

---
## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Llamada a una API web desde la aplicación de escritorio](scenario-desktop-call-api.md).
