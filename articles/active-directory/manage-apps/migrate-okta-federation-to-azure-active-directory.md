---
title: Tutorial para migrar la federación de Okta a la autenticación administrada de Azure AD
titleSuffix: Active Directory
description: Aprenda a migrar las aplicaciones federadas de Okta a la autenticación administrada de Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: b2486f4be20d2347f0cadff04ef8d0aa776ccdc5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791695"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>Tutorial: Migración de la federación de Okta a la autenticación administrada de Azure AD

En este tutorial, aprenderá a federar los inquilinos de Office 365 existentes con Okta para conseguir funcionalidades de inicio de sesión único.

La migración de la federación a Azure Active Directory se puede realizar por fases para garantizar la experiencia de autenticación deseada para los usuarios. También aprenderá a probar el acceso de federación inverso de nuevo a las aplicaciones de inicio de sesión único de Okta restantes.

## <a name="prerequisites"></a>Prerrequisitos

- Inquilino de Office 365 federado en Okta para el inicio de sesión único
- Configure los agentes de aprovisionamiento del servidor o la nube de Azure AD Connect para el aprovisionamiento de usuarios en Azure AD.

## <a name="step-1---configure-azure-ad-connect-for-authentication"></a>Paso 1: Configuración de Azure AD Connect para la autenticación

Es posible que los clientes que han federado sus dominios de Office 365 con Okta no tengan actualmente un método de autenticación válido configurado en Azure AD. Antes de migrar a la autenticación administrada, Azure AD Connect debe validarse y configurarse con una de las siguientes opciones para permitir el inicio de sesión del usuario.

Use los métodos siguientes para determinar qué método es más adecuado para su entorno:

- La **sincronización de hash de contraseñas** - [sincronización de hash de contraseñas](../hybrid/whatis-phs.md) es una extensión de la característica de sincronización de directorios implementada por los agentes de aprovisionamiento en el servidor o la nube de Azure AD Connect. Puede usar esta característica para iniciar sesión en servicios de Azure AD, como Microsoft 365. Inicie sesión en el servicio con la misma contraseña que usa para iniciar sesión en la instancia local de Active Directory.

- **Autenticación transferida**: la [autenticación transferida](../hybrid/how-to-connect-pta.md) de Azure AD permite a los usuarios iniciar sesión en aplicaciones locales y basadas en la nube con las mismas contraseñas. Cuando los usuarios inician sesión con Azure AD, esta característica valida sus contraseñas directamente con la instancia de Active Directory local mediante el agente de autenticación transferida.

- **Inicio de sesión único de conexión directa** - [El inicio de sesión único de conexión directa de Azure AD](../hybrid/how-to-connect-sso.md) permite iniciar sesión automáticamente a los usuarios en equipos de escritorio corporativos conectados a la red de la empresa. El inicio de sesión único de conexión directa proporciona a los usuarios un acceso sencillo a las aplicaciones basadas en la nube sin necesidad de usar otros componentes locales.

El inicio de sesión único de conexión directa también se puede implementar en la sincronización de hash de contraseña o en la autenticación transferida para crear una sencilla experiencia de autenticación para los usuarios de Azure AD.

Asegúrese de que se implementen todos los requisitos previos necesarios del inicio de sesión único de conexión directa para los usuarios finales mediante las indicaciones de la [guía de implementación](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites).

En nuestro ejemplo, se va a configurar la sincronización de hash de contraseña y el inicio de sesión único de conexión directa.

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>Configuración de la sincronización de hash de contraseña y el inicio de sesión único de conexión directa de Azure AD Connect

Siga estos pasos para configurar la sincronización de hash de contraseña de Azure AD Connect:

1. En el servidor de Azure AD Connect, inicie la aplicación **Azure AD Connect** desde el menú Inicio o el icono de escritorio, y seleccione **Configurar**.

   ![En la imagen se muestra el icono de Azure AD y el botón Configurar](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

2. Seleccione **Cambiar inicio de sesión de usuario** > **Siguiente**.

   ![En la imagen se muestra la pantalla Cambiar inicio de sesión de usuario](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

3. Especifique las credenciales de administrador global en la siguiente página.

   ![Imagen que muestra la especificación de las credenciales de administrador global](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

4. Actualmente, el servidor está configurado para la federación con Okta. Actualice la selección a Sincronización de hash de contraseña. Marque la casilla **Habilitar el inicio de sesión único**.

5. Después de actualizar la selección, seleccione **Siguiente**.

Siga estos pasos para habilitar el inicio de sesión único de conexión directa:

1. Escriba una credencial de administrador de dominio en el entorno local y seleccione **Siguiente**.

   ![Imagen que muestra la especificación de las credenciales de administrador de dominio](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

2. En la página final, seleccione **Configurar** para actualizar el servidor de Azure AD Connect.

   ![En la imagen se muestra la actualización del servidor de Azure AD Connect](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

3. Ignore la advertencia de Unión a Azure AD híbrido por ahora, pero tiene que volver a configurar las **opciones del dispositivo** después de deshabilitar la federación de Okta.

   ![La imagen muestra la reconfiguración de las opciones del dispositivo](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="step-2---configure-staged-rollout-features"></a>Paso 2: Configuración de las características de lanzamiento preconfigurado

El [lanzamiento preconfigurado de la autenticación en la nube](../hybrid/how-to-connect-staged-rollout.md) es una característica de Azure AD que se puede usar para probar la anulación de la federación de usuarios antes de hacer lo propio con un dominio completo. Antes de la implementación, consulte los [requisitos previos](../hybrid/how-to-connect-staged-rollout.md#prerequisites).

Después de habilitar la sincronización de hash de contraseña y el inicio de sesión único de conexión directa en el servidor de Azure AD Connect, siga estos pasos para configurar el lanzamiento preconfigurado.

1. Vaya a [Azure Portal](https://portal.azure.com/#home) y seleccione **Ver** o **Administrar Azure Active Directory**.

   ![Imagen en la que aparece Azure Portal](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

2. En el menú de Azure Active Directory, seleccione **Azure AD Connect** y confirme que la sincronización de hash de contraseña aparece como habilitada en el inquilino.

3. Después de confirmar, seleccione **Habilitar el lanzamiento preconfigurado para el inicio de sesión de usuarios administrados**.

   ![Imagen que muestra la habilitación del lanzamiento preconfigurado](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

4. Es posible que el valor de la sincronización de hash de contraseña haya pasado a **On** después de configurar el servidor; si no es así, habilítela ahora, y observará que el inicio de sesión único de conexión directa pasa a **Off**. Si intenta habilitar la sincronización en el menú, se producirá un error que significa que ya está habilitada para los usuarios del inquilino.

5. Después de habilitar la sincronización de hash de contraseña, seleccione **Administrar grupos**.

   ![Imagen que muestra la habilitación de la sincronización de hash de contraseña](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

Siga las instrucciones para agregar un grupo al lanzamiento de la sincronización de hash de contraseña. En el ejemplo siguiente, se usa un grupo de seguridad con 10 miembros para empezar.

![Imagen que muestra un ejemplo de grupo de seguridad](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

Después de agregar el grupo, espere unos 30 minutos a que la característica surta efecto en el inquilino. Cuando lo haya hecho, los usuarios ya no se redirigirán a Okta cuando intenten acceder a los servicios de Office 365.

La característica de lanzamiento preconfigurado tiene algunos escenarios no admitidos; son los siguientes:  

- No se admite la autenticación heredada, como POP3 y SMTP.

- Si ha configurado Unión a Azure AD híbrido para su uso con Okta, todos los flujos de Unión a Azure AD híbrido seguirán en Okta hasta que se haya anulado la federación del dominio. Debe permanecer una directiva de inicio de sesión en Okta que permita la autenticación heredada de Unión a Azure AD híbrido para clientes de Windows.

## <a name="step-3---create-okta-app-in-azure-ad"></a>Paso 3: Creación de la aplicación Okta en Azure AD

Es posible que los usuarios que han pasado a la autenticación administrada sigan teniendo aplicaciones en Okta a las que necesitan acceder para permitir a los usuarios un acceso sencillo a esas aplicaciones. Obtenga información sobre cómo configurar un registro de aplicaciones de Azure AD que se vincule a la página principal de Okta para los usuarios.

1. Para configurar el registro de la aplicación empresarial para Okta, vaya a [Azure Portal](https://portal.azure.com/#home). Seleccione **Ver** en **Administrar Azure Active Directory**.

2. A continuación, seleccione **Aplicaciones empresariales** en el menú de la sección Administrar.

   ![Imagen que muestra aplicaciones empresariales](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

3. En el menú **Todas las aplicaciones**, seleccione **Nueva aplicación**.

   ![Imagen que muestra nuevas aplicaciones](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

4. Seleccione **Cree su propia aplicación** en el menú lateral que aparece, asigne un nombre a la aplicación de Okta y seleccione el tipo radial para **Registrar una aplicación con la que está trabajando para integrarla con Azure AD** y, a continuación, seleccione **Crear**.

   ![Imagen que muestra el registro de una aplicación](media/migrate-okta-federation-to-azure-active-directory/register-application.png)

5. Después de registrar la aplicación, cambie la aplicación a una cuenta de cualquier directorio organizativo, como cualquier instancia de Azure AD multiinquilino y, a continuación, seleccione **Registrar**.

   ![Imagen que muestra el registro de una aplicación y el cambio de la cuenta de la aplicación](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

6. Después de agregar el registro, vuelva al menú Azure AD, seleccione **Registros de aplicaciones** y abra el registro recién creado.

   ![Imagen que muestra el registro de aplicaciones](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

7. Después de abrir la aplicación, registre el identificador de inquilino y el identificador de aplicación.

   >[!Note]
   >Los necesitará más adelante para configurar el proveedor de identidades en Okta.

   ![La imagen muestra el registro del identificador de inquilino y el identificador de aplicación](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

8. Seleccione **Certificados y secretos** en el menú de la izquierda. Seleccione **Nuevo secreto de cliente**, asígnele un nombre genérico y establezca una fecha de expiración.

9. Registre el valor y el identificador del secreto antes de salir de esta página.

   >[!NOTE]
   >Si pierde el secreto, tendrá que volver a generar uno, ya que no podrá registrar esta información posteriormente.

   ![La imagen muestra secretos de registro](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

10. Seleccione **Permisos de API** en el menú de la izquierda y conceda a la aplicación acceso a la pila de OpenID Connect (OIDC).

11. Seleccione **Agregar permiso** > **Microsoft Graph** > **Permisos delegados**.

    ![La imagen muestra los permisos delegados](media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png)

12. En la sección Permisos de OpenId, agregue **Correo electrónico**, **OpenID** y **Perfil**. A continuación, seleccione **Agregar permisos**.

    ![En la imagen se muestra la opción Agregar permisos](media/migrate-okta-federation-to-azure-active-directory/add-permissions.png)

13. Seleccione la opción **Grant admin consent for Tenant Domain Name** (Conceder consentimiento del administrador para el nombre de dominio de inquilino) y espere a que aparezca el estado Concedido.

    ![La imagen muestra el consentimiento de la concesión](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

14. Una vez que haya dado su consentimiento a los permisos, agregue la dirección URL de la página principal en **Personalización de marca** para la página principal de la aplicación del usuario.

    ![Imagen que muestra la adición de la personalización de marca](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

15. Después de configurar la aplicación, vaya al portal de administración de Okta y configure Microsoft como proveedor de identidades. Seleccione **Seguridad** > **Proveedores de identidad**, agregue un nuevo proveedor de identidades y agregue la opción predeterminada **Microsoft**.

    ![Imagen en la que se muestra la configuración de IDP](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

16. En la página Proveedor de identidades, copie el identificador de la aplicación en el campo Id. de cliente y el secreto de cliente en el campo correspondiente.

17. Seleccione **Mostrar la configuración avanzada**. De forma predeterminada, esto vinculará el nombre principal de usuario (UPN) en Okta y Azure AD para el acceso de federación inverso.

    >[!IMPORTANT]
    >Si los UPN no coinciden en Okta y Azure AD, seleccione un atributo común entre los usuarios para obtener la coincidencia.

18. Finalice la selección para el aprovisionamiento automático. De forma predeterminada, si un usuario no coincide con Okta, se intentará aprovisionarlos en Azure AD. Si ha migrado el aprovisionamiento fuera de Okta, seleccione la opción **Redirect to Okta Sign-in page** (Redirigir a la página de inicio de sesión de Okta).

    ![En la imagen se muestra la opción para redirigir a la página de inicio de sesión de Okta](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    Después de crear el IDP, se necesita una configuración adicional para enviar usuarios al IDP correcto.

19. Seleccione **Reglas de enrutamiento** en el menú Proveedores de identidades y, a continuación, seleccione **Agregar regla de enrutamiento** mediante uno de los atributos disponibles en el perfil de Okta.

20. Configure la directiva como se indica para dirigir los inicios de sesión desde todos los dispositivos y direcciones IP a Azure AD.

    En el ejemplo, el atributo **Division** no se usa en todos nuestros perfiles de Okta, lo que hace que sea un candidato fácil a usarlo para el enrutamiento de IDP.

    ![En la imagen se muestra el atributo Division para el enrutamiento de IDP](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

21. Después de agregar la regla de enrutamiento, registre el URI de redirección y agréguelo al **registro de aplicación**.

    ![Imagen que muestra el registro de aplicación](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

22. Vuelva al registro de la aplicación y seleccione la pestaña de autenticación, después, seleccione **Agregar una plataforma** y **Web**.

    ![Imagen que muestra la opción Agregar una plataforma](media/migrate-okta-federation-to-azure-active-directory/add-platform.png)

23. Agregue el URI de redirección desde el IDP en Okta y, a continuación, seleccione **Access and ID tokens** (Tokens de acceso e identificador).

    ![Imagen que muestra los tokens de acceso e identificador de Okta](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

24. Seleccione **Directorio** > **Personas** en la consola de administración. Seleccione el primer usuario de prueba y su perfil.

25. Al editar el perfil, agregue **ToAzureAD** para que coincida con el ejemplo y seleccione **Guardar**.

    ![En la imagen se muestra la edición de perfiles](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

26. Después de guardar los atributos de usuario, intente iniciar sesión como usuario modificado en el [portal de Microsoft 365](https://portal.office.com). Observará que se recorre en bucle si el usuario no forma parte del programa piloto de autenticación administrada. Para que los usuarios salgan del bucle, agréguelos a la experiencia de autenticación administrada.

## <a name="step-4---test-okta-app-access-on-pilot-members"></a>Paso 4: Prueba del acceso a la aplicación Okta en miembros piloto

Después de configurar la aplicación Okta en Azure AD y el proveedor de identidades del portal de Okta, debe asignar la aplicación a los usuarios.

1. Vaya a Azure Portal y seleccione **Azure Active Directory** > **Aplicaciones empresariales**.

2. Seleccione el registro de aplicación creado anteriormente y vaya a **Usuarios y grupos**. Agregue el grupo que se correlaciona con el piloto de autenticación administrada.

>[!NOTE]
>Los usuarios y grupos solo se pueden agregar desde la selección Aplicaciones empresariales, no se pueden agregar usuarios en el menú Registros de aplicaciones.

   ![Imagen que muestra cómo agregar un grupo](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

3. Después de unos 15 minutos, inicie sesión como uno de los usuarios piloto de la autenticación administrada y acceda a [Myapplications](https://myapplications.microsoft.com).

   ![Imagen que muestra el acceso a myapplications](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

4. Una vez autenticado, habrá un icono de **acceso a la aplicación Okta** que vinculará al usuario de nuevo a la página principal de Okta.

## <a name="step-5---test-managed-authentication-on-pilot-members"></a>Paso 5: Prueba de la autenticación administrada en miembros piloto

Después de configurar la aplicación de federación inversa Okta, haga que los usuarios realicen pruebas completas en la experiencia de autenticación administrada. Se recomienda configurar la personalización de marca de la empresa para ayudar a los usuarios a distinguir el inquilino adecuado en el que inician sesión. Obtenga [instrucciones](../fundamentals/customize-branding.md) para configurar la personalización de marca de la empresa.

>[!IMPORTANT]
>Determine las directivas de acceso condicional adicionales que puedan ser necesarias antes de anular la federación de los dominios en su conjunto desde Okta. Consulte **Directivas de inicio de sesión de Okta para la migración del acceso condicional de Azure AD** para conocer los pasos para proteger el entorno antes de la anulación definitiva.

## <a name="step-6---remove-federation-for-office-365-domains"></a>Paso 6: Eliminación de la federación para dominios de Office 365

Una vez que la organización se sienta cómoda con la experiencia de autenticación administrada, es el momento de anular la federación del dominio de Okta. Para ello, conéctese a MSOnline PowerShell con los siguientes comandos: si aún no tiene el módulo MSOnline PowerShell, puede descargarlo primero mediante un módulo de instalación MSOnline.

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

Después de establecer el dominio en Autenticación administrada, ha anulado correctamente la federación del inquilino de Office 365 de Okta, al tiempo que ha mantenido el acceso de los usuarios a la página principal de Okta. 

## <a name="next-steps"></a>Pasos siguientes

- [Migración del aprovisionamiento de sincronización de Okta a la sincronización basada en Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Migración de directivas de inicio de sesión de Okta al acceso condicional de Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)

- [Migración de aplicaciones desde Okta a Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)