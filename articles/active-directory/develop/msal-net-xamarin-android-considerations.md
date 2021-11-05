---
title: Solución de problemas y configuración de código de Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Conozca las consideraciones para usar Xamarin Android con la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 04194dc73b7c91bd1e59140a032232246b72420e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052718"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Requisitos de configuración y sugerencias para la solución de problemas para Xamarin Android con MSAL.NET

Hay varios cambios de configuración que es necesario realizar en el código al usar Xamarin Android con la biblioteca de autenticación de Microsoft para .NET (MSAL.NET). En las secciones siguientes se describen las modificaciones necesarias, seguidas de una sección [Solución de problemas](#troubleshooting) para ayudarle a evitar algunos de los problemas más comunes.

## <a name="set-the-parent-activity"></a>Definición de la actividad primaria

En Xamarin Android, establezca la actividad primaria para que el token se devuelva después de la interacción:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

En MSAL.NET 4.2 y versiones posteriores, también puede establecer esta funcionalidad en el nivel de [PublicClientApplication][PublicClientApplication]. Para ello, use una devolución de llamada:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Si usa [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), el código del generador [`PublicClientApplication`][PublicClientApplication] debe ser similar a este fragmento de código:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Asegurarse de que el control vuelve a MSAL

Cuando la parte interactiva del flujo de autenticación termine, devuelva el control a MSAL invalidando [`Activity`][Activity].[`OnActivityResult()`][OnActivityResult] .

En la invalidación, llame al método `AuthenticationContinuationHelper`.`SetAuthenticationContinuationEventArgs()` de MSAL.NET para devolver el control a MSAL al final de la parte interactiva del flujo de autenticación.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>Actualización del manifiesto de Android para la compatibilidad con System WebView 

Para admitir System WebView , el archivo *AndroidManifest.xml* debe contener los siguientes valores:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

El valor `android:scheme` se crea a partir del URI de redireccionamiento que se configura en el portal de la aplicación. Por ejemplo, si el URI de redireccionamiento es `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` , la entrada `android:scheme` del manifiesto tendría un aspecto similar al de este ejemplo:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

Como alternativa, [cree la actividad en el código](/xamarin/android/platform/android-manifest#the-basics) en lugar de editar manualmente el archivo *AndroidManifest.xml*. Para crear la actividad en el código, cree primero una clase que incluya los atributos `Activity` y `IntentFilter`.

A continuación se muestra un ejemplo de una clase que representa los valores del archivo XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>Uso de System WebView en la autenticación asíncrona

Para usar System WebView como reserva para la autenticación interactiva cuando ha configurado la aplicación para usar la autenticación asíncrona y el dispositivo no tiene ningún agente instalado, habilite MSAL para capturar la respuesta de autenticación mediante el URI de redireccionamiento del agente. MSAL intentará realizar la autenticación mediante la instancia de System WebView predeterminada en el dispositivo cuando detecte que el agente no está disponible. Con este valor predeterminado, se producirá un error porque el URI de redireccionamiento está configurado para usar un agente, y System WebView no sabe cómo usarlo para volver a MSAL. Para resolverlo, cree un _filtro de intención_ mediante el URI de redireccionamiento del agente que configuró anteriormente. Agregue el filtro de intención modificando el manifiesto de la aplicación como en este ejemplo:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Sustituya el nombre del paquete que registró en Azure Portal por el valor `android:host=`. Sustituya el hash de clave que registró en Azure Portal por el valor `android:path=`. El hash de firma **no** debe estar codificado como dirección URL. Asegúrese de que aparece un carácter de barra inclinada (`/`) inicial al principio del hash de firma.

### <a name="xamarinforms-43x-manifest"></a>Manifiesto Xamarin.Forms 4.3.x

Xamarin.Forms 4.3.x genera código que establece el atributo `package` en `com.companyname.{appName}` en el archivo *AndroidManifest.xml*. Si usa `DataScheme` como `msal{client_id}`, es posible que desee cambiar el valor para que coincida con el del espacio de nombres `MainActivity.cs`.

## <a name="android-11-support"></a>Compatibilidad con Android 11

Para usar el explorador del sistema y la autenticación asíncrona en Android 11, primero debe declarar estos paquetes para que sean visibles para la aplicación. Las aplicaciones que tienen como destino Android 10 (API 29) y las versiones anteriores pueden consultar el sistema operativo para obtener una lista de los paquetes que están disponibles en el dispositivo en un momento dado. Para admitir la privacidad y la seguridad, Android 11 reduce la visibilidad del paquete a una lista predeterminada de paquetes de sistema operativo y los paquetes que se especifican en el archivo de *AndroidManifest.xml* de la aplicación. 

Para permitir que la aplicación se autentique mediante el explorador del sistema y el agente, agregue la siguiente sección a *AndroidManifest.xml*:

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

Reemplace `{Package Name}` por el nombre del paquete de la aplicación. 

El manifiesto actualizado, que ahora incluye compatibilidad con el explorador del sistema y la autenticación asíncrona, debe tener un aspecto similar a este ejemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>Uso de la vista web incrustada (opcional)

De forma predeterminada, MSAL.NET usa el explorador web del sistema. Este explorador permite obtener el inicio de sesión único (SSO) mediante aplicaciones web y otras aplicaciones. En casos excepcionales, es posible que desee que el sistema use una vista web insertada.

En este ejemplo de código se muestra cómo configurar una vista web insertada:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Para obtener más información, consulte [Uso de exploradores web (MSAL.NET)](msal-net-web-browsers.md) y [Consideraciones del explorador del sistema de Xamarin Android](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Solución de problemas

### <a name="general-tips"></a>Sugerencias generales

- Actualice el paquete NuGet de MSAL.NET existente a la [versión más reciente de MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Verifique que Xamarin.Forms se encuentra en la versión más reciente.
- Verifique que Xamarin.Android.Support.v4 se encuentra en la versión más reciente.
- Asegúrese de que todos los paquetes de Xamarin.Android.Support tienen como destino la versión más reciente.
- Limpie o vuelva a compilar la aplicación.
- En Visual Studio, pruebe a establecer el número máximo de compilaciones de proyecto paralelas en **1**. Para ello, seleccione **Opciones** > **Proyectos y soluciones** > **Compilar y ejecutar** > **Número máximo de compilaciones de proyecto paralelas**.
- Si realiza la compilación desde la línea de comandos y el comando usa `/m`, intente quitar este elemento del comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Error: El nombre AuthenticationContinuationHelper no existe en el contexto actual.

Si un error indica que `AuthenticationContinuationHelper` no existe en el contexto actual, es posible que Visual Studio haya actualizado incorrectamente el archivo *Android.csproj\** . A veces, la ruta de acceso del archivo en el elemento `<HintPath>` contiene incorrectamente `netstandard13` en lugar de `monoandroid90`.

Este ejemplo contiene una ruta de archivo correcta:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el ejemplo de una [aplicación móvil de Xamarin que usa la Plataforma de identidad de Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). En la tabla siguiente se resume la información relevante del archivo LÉAME.

| Muestra | Plataforma | Descripción |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Aplicación sencilla de Xamarin.Forms que muestra cómo usar MSAL para autenticar cuentas personales de Microsoft y Azure AD a través del punto de conexión de Azure AD 2.0. La aplicación también muestra cómo acceder a Microsoft Graph y el token resultante. <br>![Diagrama de flujo de autenticación](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
