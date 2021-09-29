---
title: Procedimiento para habilitar SSO entre aplicaciones en Android mediante MSAL | Azure
titleSuffix: Microsoft identity platform
description: Procedimiento para usar la biblioteca de autenticación de Microsoft (MSAL) para Android con el fin de habilitar el inicio de sesión único en las aplicaciones.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: marsma
ms.reviewer: marsma
ms.openlocfilehash: 877ebb2ab58af978896e4cb61989e12bb5731a7c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628921"
---
# <a name="enable-cross-app-sso-on-android-using-msal"></a>Habilitación de SSO entre aplicaciones en Android mediante MSAL

El inicio de sesión único (SSO) permite a los usuarios escribir sus credenciales una sola vez y que las credenciales funcionen automáticamente en otras aplicaciones.

La [plataforma de identidad de Microsoft](./index.yml) y la biblioteca de autenticación de Microsoft (MSAL) le ayudan a habilitar el inicio de sesión único en su propio conjunto de aplicaciones. Con las aplicaciones de capacidad de agente y Authenticator, puede extender el inicio de sesión único a todo el dispositivo.

En este procedimiento aprenderá a configurar los SDK usados por la aplicación para proporcionar SSO a sus clientes.

## <a name="prerequisites"></a>Requisitos previos

En este procedimiento se supone que sabe cómo:

- Aprovisione su aplicación mediante Azure Portal. Para más información sobre este tema, consulte las instrucciones para crear una aplicación en [el tutorial de Android](./tutorial-v2-android.md#create-a-project).
- Integre su aplicación con la [biblioteca de autenticación de Microsoft para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Métodos del inicio de sesión único

Hay dos maneras de que las aplicaciones que usan MSAL para Android consigan el inicio de sesión único:

* A través de una [aplicación de agente](#sso-through-brokered-authentication)
* A través del [explorador del sistema](#sso-through-system-browser)


   Se recomienda usar una aplicación de agente para obtener ventajas como el inicio de sesión único en todo el dispositivo, la administración de cuentas y el acceso condicional. Sin embargo, requiere que los usuarios descarguen aplicaciones adicionales.

## <a name="sso-through-brokered-authentication"></a>Inicio de sesión único a través de la autenticación con agente

Recomendamos usar uno de los agentes de autenticación de Microsoft para participar en el inicio de sesión único (SSO) en todo el dispositivo y para cumplir las directivas de acceso condicional de la organización. La integración con un agente proporciona las siguientes ventajas:

- Inicio de sesión único del dispositivo
- Acceso condicional para:
  - Intune App Protection
  - Registro de dispositivos (Workplace Join)
  - Administración de dispositivos móviles
- Administración de cuentas en todo el dispositivo
  -  Mediante la configuración y administración de cuentas de Android
  - "Cuenta profesional": tipo de cuenta personalizada

En Android, el agente de autenticación de Microsoft es un componente que se incluye con [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) y el [Portal de empresa de Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal).

En el siguiente diagrama se ilustra la relación entre la aplicación, la biblioteca de autenticación de Microsoft (MSAL) y los agentes de autenticación de Microsoft.

![Diagrama que muestra cómo se relaciona una aplicación con MSAL, las aplicaciones de agente y el administrador de cuentas de Android.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Instalación de aplicaciones que hospedan un agente

El propietario del dispositivo puede instalar aplicaciones que hospeden un agente desde su tienda de aplicaciones (normalmente Google Play Store) en cualquier momento. Sin embargo, algunas API (recursos) están protegidas por las directivas de acceso condicional que requieren que los dispositivos estén:

- registrados (unidos al área de trabajo); o
- inscritos en la administración de dispositivos; o
- inscritos en Intune App Protection.

Si un dispositivo aún no tiene instalada una aplicación de agente, MSAL indica al usuario que instale una en cuanto la aplicación intente obtener un token de forma interactiva. A continuación, la aplicación deberá guiar al usuario por los pasos necesarios para que el dispositivo sea compatible con la directiva requerida.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efectos de la instalación y desinstalación de un agente

#### <a name="when-a-broker-is-installed"></a>Cuando se instala un agente

Cuando se instala un agente en un dispositivo, es el agente el que administra todas las solicitudes de token interactivas posteriores (llamadas a `acquireToken()`), en lugar de MSAL localmente. Cualquier estado de SSO disponible anteriormente para MSAL no está disponible para el agente. Como resultado, el usuario tendrá que autenticarse de nuevo o seleccionar una cuenta de la lista de cuentas existente que conoce el dispositivo.

La instalación de un agente no requiere que el usuario vuelva a iniciar sesión. Solo cuando el usuario necesite resolver una excepción `MsalUiRequiredException`, la siguiente solicitud pasará al agente. `MsalUiRequiredException` se puede iniciar por varias razones y debe resolverse de forma interactiva. Por ejemplo:

- El usuario cambió la contraseña asociada a su cuenta.
- La cuenta del usuario ya no cumple una directiva de acceso condicional.
- El usuario ha revocado su consentimiento para que la aplicación esté asociada a su cuenta.

**Varios agentes**: si hay varios agentes instalados en un dispositivo, el agente que se instaló primero siempre será el agente activo. Solo un agente puede estar activo en un dispositivo.

#### <a name="when-a-broker-is-uninstalled"></a>Cuando se desinstala un agente

Si solo hay instalada una aplicación que hospeda un agente y se quita, el usuario deberá volver a iniciar sesión. Al desinstalar el agente activo, se quitan del dispositivo la cuenta y los tokens asociados.

Si el Portal de empresa de Intune está instalado y funcionando como agente activo, y Microsoft Authenticator también está instalado, al desinstalar el Portal de empresa de Intune (agente activo), el usuario deberá volver a iniciar sesión. Cuando vuelva a iniciar sesión, la aplicación Microsoft Authenticator se convierte en el agente activo.

### <a name="integrating-with-a-broker"></a>Integración con un agente

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Generación de un URI de redirección para un agente

Debe registrar un URI de redirección que sea compatible con el agente. El URI de redirección del agente debe incluir el nombre del paquete de la aplicación, así como la representación codificada en Base64 de la firma de la aplicación.

El formato del URI de redirección es: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Puede usar el valor [keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) para generar un hash de firma codificada en Base64 con las claves de firma de la aplicación y, a continuación, usar Azure Portal para generar el URI de redirección con ese hash.

Linux y macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Una vez que haya generado un hash de firma con el valor *keytool*, use Azure Portal para generar el URI de redirección:

1. Inicie sesión en <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> y seleccione la aplicación Android en **Registros de aplicaciones**.
1. Seleccione **Autenticación** > **Agregar una plataforma** > **Android**.
1. En el panel para **Configurar la aplicación para Android** que se abrirá, escriba el **hash de firma** que generó anteriormente y un **nombre de paquete**.
1. Seleccione el botón **Configurar**.

Azure Portal genera el URI de redireccionamiento automáticamente y lo muestra en el campo del **URI de redirección** del panel de **configuración de Android**.

Para obtener más información sobre cómo firmar la aplicación, consulte [Firmar la aplicación](https://developer.android.com/studio/publish/app-signing) en la guía de usuario de Android Studio.

> [!IMPORTANT]
> Use su clave de firma de producción para la versión de producción de la aplicación.

#### <a name="configure-msal-to-use-a-broker"></a>Configuración de MSAL para usar un agente

Para usar un agente en la aplicación, debe confirmar que ha configurado la redirección del agente. Por ejemplo, para incluir el URI de redirección habilitado para el agente (e indicar que lo ha registrado), incluya las siguientes opciones en el archivo de configuración de MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Excepciones relacionadas con el agente

MSAL se comunica con el agente de dos maneras:

- Servicio enlazado al agente
- Administrador de cuentas de Android

MSAL usa primero el servicio enlazado al agente porque la llamada a este servicio no requiere ningún permiso de Android. Si se produce un error al enlazar con el servicio enlazado, MSAL usará la API de administrador de cuentas de Android. MSAL solo lo hace si la aplicación ya tiene concedido el permiso `"READ_CONTACTS"`.

Si obtiene una excepción `MsalClientException` con el código de error `"BROKER_BIND_FAILURE"`, hay dos opciones:

- Pida al usuario que deshabilite la optimización de energía para la aplicación Microsoft Authenticator y el Portal de empresa de Intune.
- Pida al usuario que conceda el permiso `"READ_CONTACTS"`.

### <a name="verify-broker-integration"></a>Comprobación de la integración del agente

Es posible que no quede claro inmediatamente que la integración del agente funcione, pero puede seguir estos los pasos siguientes para comprobarlo:

1. En el dispositivo Android, complete una solicitud con el agente.
1. En la configuración del dispositivo Android, busque una cuenta recién creada que se corresponda con la cuenta con la que se autenticó. La cuenta debe ser del tipo *Cuenta profesional*.

Puede quitar la cuenta de la configuración si desea repetir la prueba.

## <a name="sso-through-system-browser"></a>Inicio de sesión único a través del explorador del sistema

Las aplicaciones para Android tienen la opción de usar las pestañas personalizadas de la vista web, el explorador del sistema o Chrome para la experiencia de usuario de autenticación. Si la aplicación no usa la autenticación con agente, tendrá que usar el explorador del sistema en lugar de la vista web nativa para lograr el inicio de sesión único (SSO).

### <a name="authorization-agents"></a>Agentes de autorización

La elección de una estrategia específica para los agentes de autorización es opcional y representa una funcionalidad adicional que las aplicaciones pueden personalizar. La mayoría de las aplicaciones usarán los valores predeterminados de MSAL (consulte [Información acerca del archivo de configuración de MSAL para Android](msal-configuration.md) para ver los distintos valores predeterminados).

MSAL admite la autorización mediante `WebView` o el explorador del sistema.  En la imagen siguiente se muestra cómo se utiliza `WebView` o el explorador del sistema con CustomTabs o sin CustomTabs:

![Ejemplos de inicio de sesión de MSAL](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Implicaciones del inicio de sesión único

De forma predeterminada, las aplicaciones integradas con MSAL utilizan Custom Tabs del explorador del sistema para la autorización. A diferencia de WebWiews, Custom Tabs comparte un archivo jar de cookies con el explorador del sistema predeterminado, lo que permite menos inicios de sesión con aplicaciones web u otras aplicaciones nativas integradas con Custom Tabs.

Si la aplicación usa una estrategia de `WebView` sin integrar la compatibilidad con Microsoft Authenticator o el Portal de empresa en su aplicación, los usuarios no tendrán una experiencia de inicio de sesión único en el dispositivo o entre aplicaciones nativas y aplicaciones web.

Si la aplicación usa MSAL con un agente como Microsoft Authenticator o el Portal de empresa de Intune, los usuarios pueden tener una experiencia de inicio de sesión único entre aplicaciones si tienen un inicio de sesión activo con una de las aplicaciones.

### <a name="webview"></a>WebView

Para usar WebView de la aplicación, ponga la siguiente línea en el archivo JSON de configuración de la aplicación que se pasa a MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Al utilizar `WebView` de la aplicación, el usuario inicia sesión directamente en la aplicación. Los tokens se mantienen en el espacio aislado de la aplicación y no están disponibles fuera del archivo jar de cookies de la aplicación. En consecuencia, el usuario no puede tener una experiencia de inicio de sesión único entre aplicaciones a menos que las aplicaciones se integren con Authenticator o el Portal de empresa.

Sin embargo, `WebView` proporciona la funcionalidad para personalizar la apariencia de la interfaz de usuario de inicio de sesión. Para más información sobre cómo realizar esta personalización, consulte [Android WebViews](https://developer.android.com/reference/android/webkit/WebView) (WebViews en Android).

### <a name="default-browser-plus-custom-tabs"></a>Explorador predeterminado con Custom Tabs

De forma predeterminada, MSAL usa el explorador y una estrategia de [Custom Tabs](https://developer.chrome.com/multidevice/android/customtabs). Puede indicar explícitamente esta estrategia para evitar cambios en versiones futuras en `DEFAULT` mediante la siguiente configuración de JSON en el archivo de configuración personalizado:

```json
"authorization_user_agent" : "BROWSER"
```

Utilice este enfoque para proporcionar una experiencia de inicio de sesión único mediante el explorador del dispositivo. MSAL usa un archivo jar de cookies compartido, que permite que otras aplicaciones nativas o aplicaciones web ejecuten el inicio de sesión único en el dispositivo mediante la cookie de sesión persistente establecida por MSAL.

### <a name="browser-selection-heuristic"></a>Heurística de selección de explorador

Como es imposible que MSAL especifique el paquete exacto de explorador que se va a usar en cada amplia gama de teléfonos Android, MSAL implementa una heurística de selección de explorador que intenta proporcionar el mejor inicio de sesión único entre dispositivos.

MSAL recupera en primer lugar el explorador predeterminado del administrador de paquetes y comprueba si está en una lista probada de exploradores seguros. Si no es así, MSAL vuelve a usar la vista web en lugar de iniciar otro explorador no predeterminado desde la lista segura. Se elegirá el explorador predeterminado, independientemente de si admite pestañas personalizadas. Si el explorador admite Custom Tabs, MSAL iniciará Custom Tab. Custom Tabs tiene una apariencia similar a la de una aplicación `WebView` y permite una personalización de la interfaz de usuario básica. Consulte [Custom Tabs in Android](https://developer.chrome.com/multidevice/android/customtabs) (Custom Tabs en Android) para más información.

Si no hay paquetes de explorador en el dispositivo, MSAL usa `WebView` de la aplicación. Si no se cambia la configuración predeterminada del dispositivo, se debe iniciar el mismo explorador para cada inicio de sesión con el fin de garantizar una experiencia de SSO.

#### <a name="tested-browsers"></a>Exploradores probados

Se han probado los siguientes exploradores para ver si se redirigen correctamente al `"redirect_uri"` especificado en el archivo de configuración:

| Dispositivo | Explorador integrado | Chrome | Opera  | Microsoft Edge | Explorador UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | superada | superada |no aplicable |no aplicable |no aplicable |no aplicable |
| Samsung S7 (API 25) | superada<sup>1</sup> | superada | superada | superada | no superada |superada |
| Huawei (API 26) |superada<sup>2</sup> | superada | no superada | superada | superada |superada |
| Vivo (API 26) |superada|superada|superada|superada|superada|no superada|
| Pixel 2 (API 26) |superada | superada | superada | superada | no superada |superada |
| Oppo | superada | no aplicable<sup>3</sup>|no aplicable  |no aplicable |no aplicable | no aplicable|
| OnePlus (API 25) |superada | superada | superada | superada | no superada |superada |
| Nexus (API 28) |superada | superada | superada | superada | no superada |superada |
|MI | superada | superada | superada | superada | no superada |superada |

<sup>1</sup>El explorador integrado de Samsung es Samsung Internet.<br/>
<sup>2</sup>El explorador integrado de Huawei es Huawei Browser.<br/>
<sup>3</sup>El explorador predeterminado no se puede cambiar en la configuración del dispositivo Oppo.

## <a name="next-steps"></a>Pasos siguientes

[El modo de dispositivo compartido para dispositivos Android](msal-android-shared-devices.md) le permite configurar un dispositivo Android para que varios empleados puedan compartirlo fácilmente.