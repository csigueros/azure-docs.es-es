---
title: 'Incorporación de Google como proveedor de identidades para B2B: Azure AD'
description: Federación con Google para permitir a los usuarios invitados iniciar sesión en sus aplicaciones de Azure AD con sus cuentas de Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/01/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510e2207a2c25c5da5f4de08f3bf16fbf6cf4c7d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354346"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Incorporación de Google como proveedor de identidades para los usuarios invitados de B2B

Si configura la federación con Google, puede permitir que los usuarios invitados puedan iniciar sesión en sus aplicaciones y recursos compartidos con sus propias cuentas de Google, sin tener que crear cuentas Microsoft. Después de agregar Google como una de las opciones de inicio de sesión de la aplicación, en la página **Iniciar sesión**, el usuario solo tendrá que escribir la dirección de correo electrónico de Gmail que utiliza para iniciar sesión en Google.

![Opciones de inicio de sesión para los usuarios de Google](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> La federación de Google está diseñada específicamente para los usuarios de Gmail. Para la federación con dominios de Google Workspace, use la [federación del proveedor de identidades de SAML/WS-Fed](direct-federation.md).

> [!IMPORTANT]
>
> - **A partir del 12 de julio de 2021**, si los clientes de Azure AD B2B configuran nuevas integraciones de Google para su uso con el registro de autoservicio o para invitar a usuarios externos para sus aplicaciones personalizadas o de línea de negocio, la autenticación podría bloquearse para los usuarios de Gmail (con la pantalla de error que se muestra a continuación en la página [Qué esperar](#what-to-expect)). Este problema solo se produce si crea la integración de Google para flujos de usuario de registro de autoservicio o invitaciones después del 12 de julio de 2021, y las autenticaciones de Gmail en las aplicaciones personalizadas o de línea de negocio no se han movido a las vistas web del sistema. Dado que las vistas web del sistema están habilitadas de forma predeterminada, la mayoría de las aplicaciones no se verán afectadas. Para evitar el problema, le recomendamos encarecidamente que traslade las autenticaciones de Gmail a exploradores del sistema antes de crear nuevas integraciones de Google para el registro de autoservicio. Consulte [Acción necesaria para las vistas web insertadas](#action-needed-for-embedded-frameworks).
> - **A partir del 30 de septiembre de 30, 2021,** , Google [retira la compatibilidad con el inicio de sesión en vista web](https://developers.googleblog.com/2021/06/upcoming-security-changes-to-googles-oauth-2.0-authorization-endpoint.html). Si sus aplicaciones autentican a los usuarios con una vista web insertada y va a usar la federación de Google con [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) o Azure AD B2B para las invitaciones de usuarios externos o el [registro de autoservicio](identity-providers.md), los usuarios de Google Gmail no podrán autenticarse. [Más información](#deprecation-of-web-view-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>¿Cuál es la experiencia del usuario de Google?

Cuando un usuario de Google canjee su invitación, su experiencia variará en función de si ya ha iniciado sesión o no en Google:

- Se pedirá a los usuarios invitados que no hayan iniciado sesión en Google que lo hagan.
- Se pedirá a los usuarios invitados que ya hayan iniciado sesión en Google que elijan la cuenta que desean usar. Deben elegir la cuenta donde hayan recibido la invitación.

Los usuarios invitados que ven un error "encabezado demasiado largo" pueden eliminar sus cookies o abrir una ventana privada o de incógnito e intentar iniciar sesión de nuevo.

![Captura de pantalla en la que se muestra la página de inicio de sesión de Google.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Puntos de conexión de inicio de sesión

Ahora, los usuarios invitados de Google pueden iniciar sesión en sus aplicaciones multiinquilino o en las aplicaciones propias de Microsoft utilizando un [punto de conexión común](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (en otras palabras, una dirección URL general de la aplicación que no incluya el contexto del inquilino). Durante el proceso de inicio de sesión, el usuario invitado elige **Opciones de inicio de sesión** y después selecciona **Sign in to an organization** (Iniciar sesión en una organización). A continuación, el usuario escribe el nombre de la organización y continúa iniciando sesión con las credenciales de Google.

Los usuarios invitados de Google también pueden usar puntos de conexión de la aplicación que incluyan la información del inquilino; por ejemplo:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

También puede proporcionar a los usuarios invitados de Google un vínculo directo a una aplicación o recurso que incluya la información del inquilino; por ejemplo, `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`.

## <a name="deprecation-of-web-view-sign-in-support"></a>Desuso de la compatibilidad con el inicio de sesión en vista web

A partir del 30 de septiembre de 2021, Google [retira la compatibilidad con el inicio de sesión en la vista web insertada](https://developers.googleblog.com/2021/06/upcoming-security-changes-to-googles-oauth-2.0-authorization-endpoint.html). Si sus aplicaciones autentican a los usuarios con una vista web insertada y va a usar la federación de Google con [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) o Azure AD B2B para las [invitaciones de usuarios externos](google-federation.md) o el [registro de autoservicio](identity-providers.md), los usuarios de Google Gmail no podrán autenticarse.

Estos son escenarios conocidos que afectarán a los usuarios de Gmail:
- Aplicaciones de Microsoft (por ejemplo, Teams y Power Apps) en Windows 
- Aplicaciones de Windows que usan el control [WebView](/windows/communitytoolkit/controls/wpf-winforms/webview), [WebView2](/microsoft-edge/webview2/) o el control WebBrowser anterior, para la autenticación. Estas aplicaciones deben migrarse para usar el flujo Administrador de cuentas web (WAM).
- Aplicaciones Android que usan el elemento de interfaz de usuario WebView 
- Aplicaciones iOS con UIWebView/WKWebview 
- [Aplicaciones que usan ADAL](../develop/howto-get-list-of-all-active-directory-auth-library-apps.md)

Este cambio no afecta a:
- Aplicaciones web
- Servicios de Microsoft 365 a los que se accede a través de un sitio web (por ejemplo, SharePoint Online, aplicaciones web de Office y aplicación web de Teams)
- Aplicaciones móviles que usan vistas web del sistema para la autenticación ([SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) en iOS, [Pestañas personalizadas](https://developer.chrome.com/docs/android/custom-tabs/overview/) en Android).  
- Identidades de Google Workspace, por ejemplo cuando usa la [federación basada en SAML](direct-federation.md) con Google Workspace.
- Aplicaciones de Windows que usan el Administrador de cuentas web (WAM) o el Agente de autenticación web (WAB).  

### <a name="action-needed-for-embedded-web-views"></a>Acción necesaria para las vistas web insertadas

Modifique sus aplicaciones para que usen el explorador del sistema para el inicio de sesión. Para más información, consulte [Interfaz de usuario web del sistema frente a insertada](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) en la documentación de MSAL.NET. Todos los SDK de MSAL usan la vista web del sistema de forma predeterminada.

### <a name="what-to-expect"></a>Qué esperar

A partir del 30 de septiembre, Microsoft implantará globalmente un flujo de inicio de sesión del dispositivo que actúa como solución alternativa para las aplicaciones que aún usan vistas web insertadas para asegurarse de que la autenticación no se bloquea.

### <a name="how-to-sign-in-with-the-device-sign-in-flow"></a>Inicio de sesión con el flujo de inicio de sesión del dispositivo

El flujo de inicio de sesión del dispositivo solicita a los usuarios que inicien sesión con una cuenta de Gmail en una vista web insertada que escriban un código en un explorador independiente para terminar el proceso. Si los usuarios inician sesión con su cuenta de Gmail por primera vez sin sesiones activas en el explorador, verán la siguiente secuencia de pantallas. Si una cuenta de Gmail existente ya ha iniciado sesión, es posible que se eliminen algunos de estos pasos.

1. En la pantalla **Iniciar sesión**, el usuario escribe su dirección de Gmail y selecciona **Siguiente**.

   ![Captura de pantalla en la que se muestra la pantalla de inicio de sesión.](media/google-federation/1-sign-in.png)

1. Aparece la siguiente pantalla, en la que se le pide al usuario que abra una nueva ventana, vaya a https://microsoft.com/devicelogin y escriba el código alfanumérico de 9 dígitos que se muestra.

   ![Captura de pantalla que muestra el código de 9 dígitos](media/google-federation/2-sign-in-code.png)

1. Se abre la página de inicio de sesión del dispositivo, donde el usuario puede escribir el código. 

   ![Captura de pantalla que muestra la página de inicio de sesión de Google](media/google-federation/3-enter-code.png)

1. Si los códigos coinciden, por motivos de seguridad, se pide al usuario que vuelva a escribir su correo electrónico para confirmar la aplicación y la ubicación de inicio de sesión.

   ![Captura de pantalla que muestra la pantalla para volver a escribir el correo electrónico](media/google-federation/4-sign-in-reenter-email.png)

1. El usuario inicia sesión en Google con su correo electrónico y contraseña.

   ![Captura de pantalla que muestra la página de inicio de sesión de Google](media/google-federation/5-sign-in-with-google.png)

1. Una vez más, se les pide que confirmen la aplicación en la que inician sesión.

   ![Captura de pantalla que muestra la pantalla de confirmación de la aplicación](media/google-federation/6-confirm-sign-in.png)

1. El usuario selecciona **Continuar**. Un mensaje confirma que han iniciado sesión. El usuario cierra la pestaña o ventana y se le devuelve a la primera pantalla, donde ahora ha iniciado sesión en la aplicación.

   ![Captura de pantalla que muestra la confirmación de inicio de sesión](media/google-federation/7-app-sign-in-confirmed.png)

Como alternativa, puede hacer que los usuarios de Gmail nuevos y existentes inicien sesión con el código de acceso de un solo uso por correo electrónico. Para que los usuarios de Gmail usen el código de acceso de un solo uso por correo electrónico:

1. [Habilite el código de acceso de un solo uso por correo electrónico](one-time-passcode.md#enable-email-one-time-passcode).
2. [Elimine la federación de Google](google-federation.md#how-do-i-remove-google-federation).
3. [Restablezca el estado de canje](reset-redemption-status.md) de los usuarios de Gmail para que puedan usar el código de acceso de un solo uso por correo electrónico en el futuro.

Si desea solicitar una extensión, los clientes con identificadores de cliente de OAuth afectados deben haber recibido un correo electrónico de los desarrolladores de Google con la siguiente información relacionada con una extensión de aplicación de directivas de un solo uso, que debe completarse antes del 31 de enero de 2022:

- "Si es necesario, puede solicitar una **extensión de aplicación de directivas de un solo uso para vistas web insertadas** para cada identificador de cliente de OAuth que aparece hasta el 31 de enero de 2022. Para mayor claridad, la directiva para vistas web insertadas se aplicará el 1 de febrero de 2022 sin excepciones ni extensiones."

Las aplicaciones que se migran a una vista web permitida para la autenticación no se verán afectadas, y los usuarios podrán autenticarse a través de Google como de costumbre.

Si las aplicaciones no se migran a una vista web permitida para la autenticación, los usuarios de Gmail afectados verán la pantalla siguiente.

![Error de inicio de sesión de Google si las aplicaciones no se migran a los exploradores del sistema](media/google-federation/google-sign-in-error-ewv.png)

### <a name="distinguishing-between-cefelectron-and-embedded-web-views"></a>Distinción entre CEF/Electron y vistas web insertadas

Además del [desuso de la vista web insertada y la compatibilidad con el inicio de sesión en el marco](#deprecation-of-web-view-sign-in-support), Google también va a [dejar de usar la autenticación de Gmail basada en Chromium Embedded Framework (CEF)](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). En el caso de las aplicaciones basadas en CEF, como las aplicaciones de Electron, Google deshabilitará la autenticación el 30 de junio de 2021. Las aplicaciones afectadas han recibido un aviso de Google directamente y no se tratan en esta documentación.  Este documento pertenece a las vistas web insertadas descritas anteriormente, que Google restringirá en una fecha diferente a partir del 30 de septiembre de 2021.

### <a name="action-needed-for-embedded-frameworks"></a>Acción necesaria para marcos insertados

Siga la [guía de Google](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) para determinar si sus aplicaciones se verán afectadas por este cambio.

## <a name="step-1-configure-a-google-developer-project"></a>Paso 1: configuración de un proyecto de desarrollador de Google

En primer lugar, cree un proyecto en la consola de desarrolladores de Google para obtener un identificador y un secreto de cliente que pueda agregar después a Azure Active Directory (Azure AD). 
1. Vaya a las API de Google de https://console.developers.google.com e inicie sesión con su cuenta de Google. Se recomienda utilizar una cuenta de Google compartida con el equipo.
2. Si se le solicita, acepte los términos del servicio.
3. Cree un nuevo proyecto: en la esquina superior izquierda de la página, seleccione la lista de proyectos y, en la página **Seleccionar un proyecto**, haga clic en **Nuevo proyecto**.
4. En la página **Nuevo proyecto**, escriba un nombre para el proyecto (por ejemplo, **Azure AD B2B**) y seleccione **Crear**: 
   
   ![Captura en la que se muestra una página Nuevo proyecto.](media/google-federation/google-new-project.png)

4. En la página **API y servicios**, seleccione **Ver** en el nuevo proyecto.

5. Seleccione **Go to APIs overview** (Ir a la información general de las API) en la tarjeta de API. Seleccione **OAuth consent screen** (Pantalla de consentimiento de OAuth).

6. Seleccione **Externo** y después **Crear**. 

7. En la **Pantalla de consentimiento de OAuth**, especifique un **nombre de aplicación**:

   ![Captura de pantalla en la que se muestra la pantalla de consentimiento de OAuth de Google.](media/google-federation/google-oauth-consent-screen.png)

8. Desplácese hasta la sección **Dominios autorizados** y escriba **microsoftonline.com**:

   ![Captura de pantalla en la que se muestra la sección de dominios autorizados.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Seleccione **Guardar**.

10. Seleccione **Credenciales**. En el menú **Crear credenciales**, seleccione **Id. del cliente de OAuth**.

    ![Captura de pantalla en la que se muestra el menú Crear credenciales de las API de Google.](media/google-federation/google-api-credentials.png)

11. En **Application type** (Tipo de aplicación), seleccione **Web application** (Aplicación web). Asigne un nombre adecuado a la aplicación, como **Azure AD B2B**. En **URI de redirección autorizados**, escriba los siguientes URI:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(donde `<tenant ID>` es el id. de inquilino)
    - `https://login.microsoftonline.com/te/<tenant name>.onmicrosoft.com/oauth2/authresp` <br>(donde `<tenant name>` es el nombre del inquilino)
   
    > [!NOTE]
    > Para buscar el identificador de inquilino, vaya a [Azure Portal](https://portal.azure.com). En **Azure Active Directory**, seleccione **Propiedades** y copie el **Id. del inquilino**.

    ![Captura de pantalla en la que se muestra la sección de identificadores URI de redirección autorizados.](media/google-federation/google-create-oauth-client-id.png)

12. Seleccione **Crear**. Copie el identificador de cliente y del secreto de cliente. Los usará cuando agregue el proveedor de identidades en Azure Portal.

    ![Captura de pantalla en la que se muestra el Id. de cliente de OAuth y el secreto de cliente.](media/google-federation/google-auth-client-id-secret.png)

13. Puede dejar el proyecto con el estado de publicación **Pruebas** y agregar usuarios de prueba a la pantalla de consentimiento de OAuth. O bien, puede seleccionar el botón **Publicar aplicación** en la pantalla de consentimiento de OAuth para que la aplicación esté disponible para cualquier usuario con una cuenta de Google.

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Paso 2: configuración de la federación de Google en Azure AD 

Defina ahora el identificador y el secreto de cliente de Google. Para ello, puede usar Azure Portal o PowerShell. No olvide probar la configuración de la federación de Google; para ello, invítese a usted mismo. Use una dirección de Gmail e intente canjear la invitación con su cuenta de Google invitada. 

**Para configurar la federación de Google en Azure Portal** 
1. Vaya a [Azure Portal](https://portal.azure.com). En el panel izquierdo, seleccione **Azure Active Directory**. 
2. Seleccione **External Identities**.
3. Seleccione **Todos los proveedores de identidades** y haga clic en el botón de **Google**.
4. Escriba el identificador y el secreto de cliente que obtuvo anteriormente. Seleccione **Guardar**: 

   ![Captura de pantalla en la que se muestra la página para agregar un proveedor de identidades de Google.](media/google-federation/google-identity-provider.png)

**Configuración de la federación de Google con PowerShell**
1. Instale la versión más reciente de Azure AD PowerShell para el módulo Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Ejecute este comando: `Connect-AzureAD`
3. En el símbolo del sistema, inicie sesión con la cuenta de administrador global administrada.  
4. Ejecute el siguiente comando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Use el identificador y secreto de cliente de la aplicación que creó en el "Paso 1: configuración de un proyecto de desarrollador de Google". Para más información, vea [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true). 

## <a name="how-do-i-remove-google-federation"></a>¿Cómo se quita la federación de Google?

La configuración de la federación de Google se puede eliminar. Si lo hace, los usuarios invitados de Google que ya hayan canjeado su invitación no podrán iniciar sesión. Sin embargo, puede concederles acceso de nuevo a sus recursos al [restablecer el estado de canje](reset-redemption-status.md).
 
**Para eliminar la federación de Google desde el portal de Azure AD**
1. Vaya a [Azure Portal](https://portal.azure.com). En el panel izquierdo, seleccione **Azure Active Directory**. 
2. Seleccione **External Identities**.
3. Seleccione **Todos los proveedores de identidades**.
4. En la línea de **Google**, seleccione el botón de puntos suspensivos ( **...** ) y, a continuación, seleccione **Eliminar**. 
   
   ![Captura de pantalla en la que se muestra el botón de eliminación del proveedor de identidades sociales.](media/google-federation/google-social-identity-providers.png)

1. Seleccione **Sí** para confirmar la eliminación. 

**Para eliminar la federación de Google mediante PowerShell** 
1. Instale la versión más reciente de Azure AD PowerShell para el módulo Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Ejecute `Connect-AzureAD`.  
4. En el símbolo del sistema, inicie sesión con la cuenta de administrador global administrada.  
5. Escriba el comando siguiente:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para más información, consulte [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true).