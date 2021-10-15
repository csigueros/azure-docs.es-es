---
title: Tutorial sobre la migración de la federación de Okta a la autenticación administrada de Azure AD
titleSuffix: Active Directory
description: Aprenda a migrar las aplicaciones federadas de Okta a la autenticación administrada de Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 28759e1f53199aca866a6b073c9e05ffd31f3d1e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355914"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>Tutorial: Migración de la federación de Okta a la autenticación administrada de Azure Active Directory

En este tutorial, aprenderá a federar los inquilinos de Office 365 existentes con Okta para conseguir funcionalidades de inicio de sesión único.

Puede migrar la federación a Azure Active Directory por fases para garantizar una buena experiencia de autenticación para los usuarios. En una migración por fases, también puede probar el acceso de federación inverso de nuevo a las aplicaciones de inicio de sesión único de Okta restantes.

## <a name="prerequisites"></a>Requisitos previos

- Un inquilino de Office 365 federado en Okta para el inicio de sesión único
- Agentes de aprovisionamiento del servidor o la nube de Azure AD Connect configurados para el aprovisionamiento de usuarios en Azure AD.

## <a name="1-configure-azure-ad-connect-for-authentication"></a>1. Configuración de Azure AD Connect para la autenticación

Es posible que los clientes que han federado sus dominios de Office 365 con Okta no tengan actualmente un método de autenticación válido configurado en Azure AD. Antes de migrar a la autenticación administrada, valide Azure AD Connect y configúrela para permitir el inicio de sesión del usuario.

Configure el método de inicio de sesión más adecuado para su entorno:

- **Sincronización de hash de contraseñas**: la [sincronización de hash de contraseñas](../hybrid/whatis-phs.md) es una extensión de la característica de sincronización de directorios que implementan los agentes de aprovisionamiento en el servidor o la nube de Azure AD Connect. Puede usar esta característica para iniciar sesión en servicios de Azure AD, como Microsoft 365. Inicie sesión en el servicio con la misma contraseña que usa para iniciar sesión en la instancia local de Active Directory.

- **Autenticación transferida**: la [autenticación transferida](../hybrid/how-to-connect-pta.md) de Azure AD permite a los usuarios iniciar sesión en aplicaciones locales y basadas en la nube con las mismas contraseñas. Cuando los usuarios inician sesión con Azure AD, el agente de autenticación transferida valida las contraseñas directamente con la instancia de Active Directory local.

- **Inicio de sesión único de conexión directa**: el [inicio de sesión único de conexión directa de Azure AD](../hybrid/how-to-connect-sso.md) permite iniciar sesión automáticamente a los usuarios en equipos de escritorio corporativos conectados a la red de la empresa. El inicio de sesión único de conexión directa proporciona a los usuarios un acceso sencillo a las aplicaciones basadas en la nube sin necesidad usar de otros componentes locales.

El inicio de sesión único de conexión directa se puede implementar en la sincronización de hash de contraseña o en la autenticación transferida para crear una sencilla experiencia de autenticación para los usuarios de Azure AD.

Asegúrese de que se implementen todos los requisitos previos necesarios del inicio de sesión único de conexión directa para los usuarios finales mediante las indicaciones de la [guía de implementación](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites).

En nuestro ejemplo, configuraremos la sincronización de hash de contraseña y el inicio de sesión único de conexión directa.

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>Configuración de la sincronización de hash de contraseña y el inicio de sesión único de conexión directa de Azure AD Connect

Siga estos pasos para configurar la sincronización de hash de contraseña de Azure AD Connect:

1. En el servidor de Azure AD Connect, abra la aplicación **Azure AD Connect** y seleccione **Configurar**.

   ![Captura de pantalla que muestra el icono de Azure AD y el botón Configurar.](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

2. Seleccione **Cambiar inicio de sesión de usuario** > **Siguiente**.

   ![Captura de pantalla que muestra la página para cambiar el inicio de sesión del usuario.](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

3. Especifique las credenciales de administrador global.

   ![Captura de pantalla que muestra dónde puede especificar las credenciales de administrador global.](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

4. Actualmente, el servidor está configurado para la federación con Okta. Cambie la selección a **Sincronización de hash de contraseña**. A continuación, seleccione **Habilitar el inicio de sesión único**.

5. Seleccione **Siguiente**.

Siga estos pasos para habilitar el inicio de sesión único de conexión directa:

1. Especifique las credenciales de administrador de dominio para el sistema local. Luego, seleccione **Siguiente**.

   ![Captura de pantalla que muestra la configuración del inicio de sesión del usuario.](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

2. En la página final, seleccione **Configurar** para actualizar el servidor de Azure AD Connect.

   ![Captura de pantalla que muestra la página de configuración.](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

3. Ignore la advertencia de Unión a Azure AD híbrido por ahora. Volverá a configurar las opciones del dispositivo después de deshabilitar la federación desde Okta.

   ![Captura de pantalla que muestra el vínculo para configurar las opciones del dispositivo.](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="2-configure-staged-rollout-features"></a>2. Configuración de las características de lanzamiento preconfigurado

En Azure AD, puede usar un [lanzamiento preconfigurado de la autenticación en la nube](../hybrid/how-to-connect-staged-rollout.md) para probar la anulación de la federación de los usuarios antes de probar la anulación de la federación de un dominio completo. Antes de realizar la implementación, revise los [requisitos previos](../hybrid/how-to-connect-staged-rollout.md#prerequisites).

Después de habilitar la sincronización de hash de contraseña y el inicio de sesión único de conexión directa en el servidor de Azure AD Connect, siga estos pasos para configurar un lanzamiento preconfigurado.

1. En [Azure Portal](https://portal.azure.com/#home), seleccione **Ver** o **Administrar Azure Active Directory**.

   ![Captura de pantalla en la que se muestra Azure Portal.](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

2. En el menú **Azure Active Directory**, seleccione **Azure AD Connect**. A continuación, confirme que la **sincronización de hash de contraseña** está habilitada en el inquilino.

3. Seleccione **Habilitar lanzamiento preconfigurado para el inicio de sesión de usuarios administrados**.

   ![Captura de pantalla que muestra la opción para habilitar un lanzamiento preconfigurado.](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

4. Es posible que el valor de **sincronización de hash de contraseña** haya cambiado a **Activado** después de configurar el servidor. Si no es así, habilite esta opción ahora. 

   Tenga en cuenta que el **inicio de sesión único de conexión directa** está establecido en **Desactivado**. Si intenta activarlo, se producirá un error que significa que ya está habilitado para los usuarios del inquilino.

5. Seleccione **Administrar grupos**.

   ![Captura de pantalla que muestra el botón para administrar grupos.](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

Siga las instrucciones para agregar un grupo al lanzamiento de la sincronización de hash de contraseña. En el ejemplo siguiente, el grupo de seguridad empieza con 10 miembros.

![Captura de pantalla que muestra un ejemplo de un grupo de seguridad.](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

Después de agregar el grupo, espere unos 30 minutos mientras la característica surte efecto en el inquilino. Cuando lo haya hecho, los usuarios ya no se redirigirán a Okta cuando intenten acceder a los servicios de Office 365.

La característica de lanzamiento preconfigurado tiene algunos escenarios no admitidos:  

- No se admite la autenticación heredada, como POP3 y SMTP.

- Si ha configurado Unión a Azure AD híbrido para su uso con Okta, todos los flujos de Unión a Azure AD híbrido seguirán en Okta hasta que se haya anulado la federación del dominio. Una directiva de inicio de sesión debe permanecer en Okta para permitir la autenticación heredada de Unión a Azure AD híbrido para clientes de Windows.

## <a name="3-create-an-okta-app-in-azure-ad"></a>3. Creación de una aplicación de Okta en Azure AD

Es posible que los usuarios que han cambiado a la autenticación administrada necesiten todavía acceder a las aplicaciones de Okta. Para permitir que los usuarios accedan fácilmente a esas aplicaciones, puede registrar una aplicación de Azure AD que se vincule a la página principal de Okta.

Para configurar el registro de aplicaciones empresariales para Okta: 
1. En [Azure Portal](https://portal.azure.com/#home), en **Administrar Azure Active Directory**, seleccione **Ver**.

2. En el menú izquierdo, en **Administrar**, seleccione **Aplicaciones empresariales**.

   ![Captura de pantalla que muestra la opción "Aplicaciones empresariales".](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

3. En el menú **Todas las aplicaciones**, seleccione **Nueva aplicación**.

   ![Captura de pantalla en la que se muestra la opción "Nueva aplicación".](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

4. Seleccione **Cree su propia aplicación**. En el menú que se abre, asigne un nombre a la aplicación de Okta y seleccione **Registrar una aplicación con la que está trabajando para integrarla con Azure AD**. Seleccione **Crear**.

   :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/register-application.png" alt-text="Captura de pantalla que muestra cómo registrar una aplicación." lightbox="media/migrate-okta-federation-to-azure-active-directory/register-application.png":::

5. Seleccione **Cuentas en cualquier directorio de la organización (cualquier directorio de Azure AD: multiinquilino)**  > **Registrar**.

   ![Captura de pantalla que muestra cómo registrar una aplicación y cambiar la cuenta de la aplicación.](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

6. En el menú de Azure AD, seleccione **Registros de aplicaciones**. A continuación, abra el registro recién creado.

   ![Captura de pantalla que muestra el registro de la nueva aplicación.](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

7. Guarde el identificador de inquilino y el identificador de aplicación.

   >[!Note]
   >Los necesitará más adelante para configurar el proveedor de identidades en Okta.

   ![Captura de pantalla que muestra el identificador de inquilino y el de aplicación.](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

8. En el menú izquierdo, seleccione **Certificados y secretos**. A continuación, seleccione **Nuevo secreto de cliente**. Asigne al secreto un nombre genérico y establezca su fecha de expiración.

9. Guarde el valor y el identificador del secreto.

   >[!NOTE]
   >El valor y el identificador no se mostrarán más adelante. Si no guarda esta información ahora, tendrá que volver a generar un secreto.

   ![Captura de pantalla que muestra dónde guardar el valor del secreto y el identificador.](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

10. Seleccione **Permisos de API** en el menú izquierdo. Conceda a la aplicación acceso a la pila de OpenID Connect (OIDC).

11. Seleccione **Agregar permiso** > **Microsoft Graph** > **Permisos delegados**.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png" alt-text="Captura de pantalla que muestra los permisos delegados." lightbox="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png":::

12. En la sección Permisos de OpenId, agregue **Correo electrónico**, **OpenID** y **Perfil**. Seleccione **Agregar permisos**.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png" alt-text="Captura de pantalla que muestra cómo agregar permisos" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png":::.

13. Seleccione **Conceder consentimiento de administrador para \<tenant domain name>** y espere hasta que aparezca el estado **Concedido**.

    ![Captura de pantalla que muestra el consentimiento concedido.](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

14. En el menú de la izquierda, seleccione **Personalización de marca**. En **Dirección URL de la página principal**, agregue la página principal de la aplicación del usuario.

    ![Captura de pantalla que muestra cómo agregar la personalización de marca.](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

15. En el portal de administración de Okta, seleccione **Security (Seguridad)**  > **Identity Providers** (Proveedores de identidades) para agregar un nuevo proveedor de identidad. Seleccione **Add Microsoft** (Agregar Microsoft).

    ![Captura de pantalla que muestra cómo agregar el proveedor de identidades.](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

16. En la página **Proveedor de identidades**, copie el identificador de la aplicación en el campo **Identificador de cliente**. Copie el secreto de cliente en el campo **Secreto de cliente**.

17. Seleccione **Mostrar la configuración avanzada**. De forma predeterminada, esta configuración vinculará el nombre principal de usuario (UPN) de Okta con el UPN de Azure AD para el acceso de federación inverso.

    >[!IMPORTANT]
    >Si los UPN de Okta y Azure AD no coinciden, seleccione un atributo que sea común entre los usuarios.

18. Finalice las selecciones para el aprovisionamiento automático. De forma predeterminada, si un usuario no coincide en Okta, el sistema intentará aprovisionarlo en Azure AD. Si ha migrado el aprovisionamiento fuera de Okta, seleccione la opción **Redirect to Okta Sign-in page** (Redirigir a la página de inicio de sesión de Okta).

    ![Captura de pantalla que muestra la opción para redirigir a la página de inicio de sesión de Okta.](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    Ahora que ha creado el proveedor de identidades (IDP), debe enviar usuarios al IDP correcto.

19. En el menú **Identity Providers** (Proveedores de identidades), seleccione **Routing Rules** > **Add Routing Rule** (Reglas de enrutamiento > Agregar regla de enrutamiento). Use uno de los atributos disponibles en el perfil de Okta.

20. Para dirigir los inicios de sesión desde todos los dispositivos y direcciones IP a Azure AD, configure la directiva como se muestra en la imagen siguiente.

    En este ejemplo, el atributo **Division** no se está usando en ningún perfil de Okta, por lo que es una buena opción para el enrutamiento de IDP.

    ![Captura de pantalla que muestra el atributo Division para el enrutamiento de IDP.](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

21. Ahora que ha agregado la regla de enrutamiento, guarde el URI de redireccionamiento para poder agregarlo al registro de la aplicación.

    ![Captura de pantalla que muestra la ubicación del URI de redireccionamiento.](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

22. En el registro de la aplicación, en el menú izquierdo, seleccione **Autenticación**. A continuación, seleccione **Agregar una plataforma** > **Web**

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-platform.png" alt-text="Captura de pantalla que muestra cómo agregar una plataforma web" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-platform.png":::.

23. Agregue el URI de redireccionamiento que guardó en el IDP de Okta. Seleccione **Tokens de acceso** y **Tokens de id.**

    ![Captura de pantalla que muestra los tokens de acceso y de identificador de Okta.](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

24. Seleccione **Directorio** > **Personas** en la consola de administración. Seleccione el primer usuario de prueba para editar el perfil.

25. En el perfil, agregue **ToAzureAD** como en la imagen siguiente. Después, seleccione **Guardar**.

    ![Captura de pantalla que muestra cómo editar un perfil.](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

26. Intente iniciar sesión en el [portal de Microsoft 365](https://portal.office.com) como usuario modificado. Observará que la acción se recorre en bucle si el usuario no forma parte del programa piloto de autenticación administrada. Para salir del bucle, agregue al usuario a la experiencia de autenticación administrada.

## <a name="4-test-okta-app-access-on-pilot-members"></a>4. Prueba del acceso a la aplicación de Okta en miembros piloto

Después de configurar la aplicación de Okta en Azure AD y el proveedor de identidades en el portal de Okta, debe asignar la aplicación a los usuarios.

1. En Azure Portal, seleccione **Azure Active Directory** > **Aplicaciones empresariales**.

2. Seleccione el registro de aplicación que creó anteriormente y vaya a **Usuarios y grupos**. Agregue el grupo que se correlaciona con el piloto de autenticación administrada.

   >[!NOTE]
   >Solo puede agregar usuarios y grupos desde la página **Aplicaciones empresariales**. No se pueden agregar usuarios desde el menú **Registros de aplicaciones**.

   ![Captura de pantalla que muestra cómo agregar un grupo.](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

3. Después de unos 15 minutos, inicie sesión como uno de los usuarios piloto de la autenticación administrada y vaya a [Aplicaciones](https://myapplications.microsoft.com).

   ![Captura de pantalla que muestra la galería Aplicaciones.](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

4. Seleccione el icono de **acceso a la aplicación de Okta** para devolver al usuario a la página principal de Okta.

## <a name="5-test-managed-authentication-on-pilot-members"></a>5. Prueba de la autenticación administrada en miembros piloto

Después de configurar la aplicación de federación inversa de Okta, haga que los usuarios realicen pruebas completas en la experiencia de autenticación administrada. Se recomienda configurar la personalización de marca de la empresa para ayudar a los usuarios a reconocer el inquilino en el que inician sesión. Para más información, consulte [Incorporación de la personalización de marca en la página de inicio de sesión de Azure Active Directory de la organización](../fundamentals/customize-branding.md).

>[!IMPORTANT]
>Identifique las directivas de acceso condicional adicionales que pueda necesitar antes de anular completamente la federación de los dominios de Okta. Consulte [Directivas de inicio de sesión de Okta para la migración del acceso condicional de Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md) para proteger el entorno antes de la anulación definitiva.

## <a name="6-defederate-office-365-domains"></a>6. Anulación de la federación de los dominios de Office 365

Una vez que la organización se sienta cómoda con la experiencia de autenticación administrada, puede anular la federación del dominio de Okta. Para empezar, use los siguientes comandos para conectarse a MSOnline PowerShell. Si aún no tiene el módulo MSOnline PowerShell, para descargarlo, escriba `install-module MSOnline`.

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

Después de establecer el dominio en Autenticación administrada, habrá anulado correctamente la federación del inquilino de Office 365 de Okta, al tiempo que ha mantenido el acceso de los usuarios a la página principal de Okta. 

## <a name="next-steps"></a>Pasos siguientes

- [Migración del aprovisionamiento de sincronización de Okta a la sincronización basada en Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Migración de directivas de inicio de sesión de Okta al acceso condicional de Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)

- [Migración de aplicaciones desde Okta a Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
