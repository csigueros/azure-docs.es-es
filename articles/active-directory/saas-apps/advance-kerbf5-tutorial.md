---
title: 'Tutorial: Integración del inicio de sesión único de Azure Active Directory con F5 | Microsoft Docs'
description: En este tutorial obtendrá información sobre los pasos necesarios para realizar la integración de F5 con Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: 908a7e86c14f87a8cf59d62a3290c1b9ce6eb149
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817844"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con F5

En este tutorial, obtendrá información sobre cómo integrar F5 con Azure Active Directory (Azure AD). Al integrar F5 con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a F5.
* Permitir que los usuarios inicien sesión automáticamente en F5 con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en F5.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

F5 admite el inicio de sesión único iniciado por **SP e IDP**.

El inicio de sesión único de F5 se puede configurar de tres maneras diferentes:

- [Configuración del inicio de sesión único de F5 para la aplicación Kerberos avanzada](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Configuración del inicio de sesión único de F5 para la aplicación basada en encabezados](headerf5-tutorial.md)

- [Configuración del inicio de sesión único de F5 para la aplicación Kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Adición de F5 desde la galería

Para configurar la integración de F5 en Azure AD, será preciso agregar F5 desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **F5** en el cuadro de búsqueda.
1. Seleccione **F5** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configuración y prueba del inicio de sesión único de Azure AD para F5

Configure y pruebe el inicio de sesión único de Azure AD con F5 mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de F5.

Para configurar y probar el inicio de sesión único de Azure AD con F5, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de F5](#configure-f5-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de F5](#create-f5-test-user)** , para tener un homólogo de B.Simon en F5 que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de la aplicación **F5**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<YourCustomFQDN>.f5.com/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<YourCustomFQDN>.f5.com/`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up F5** (Configurar F5), copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a F5 mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **F5**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

   ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo de Agregar usuario](common/add-assign-user.png)

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-f5-sso"></a>Configuración del inicio de sesión único de F5

- [Configuración del inicio de sesión único de F5 para la aplicación basada en encabezados](headerf5-tutorial.md)

- [Configuración del inicio de sesión único de F5 para la aplicación Kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Configuración del inicio de sesión único de F5 para la aplicación Kerberos avanzada

1. Abra una nueva ventana del explorador web e inicie sesión en el sitio de empresa de F5 (Kerberos avanzado) como administrador y haga lo siguiente:

1. Debe importar el certificado de metadatos en F5 (Kerberos avanzado), que se usará más adelante en el proceso de instalación. Vaya a **System > Certificate Management > Traffic Certificate Management > > SSL Certificate List** (Sistema > Administración de certificados > Lista de certificados SSL). Haga clic en **Import** (Importar) en la esquina derecha.

    ![Captura de pantalla que resalta el botón Import (Importar) para importar el certificado de metadatos.](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Para configurar el IDP de SAML, vaya a **Access > Federation > SAML Service Provider > Create > From Metadata** (Acceso > Federación > Proveedor de servicios SAML > Crear > A partir de metadatos).

    ![Captura de pantalla que muestra cómo crear el IDP de SAML a partir de metadatos.](./media/advance-kerbf5-tutorial/configure02.png)

    ![Captura de pantalla que muestra la pantalla Create New SAML IdP Connector (Crear conector de IdP de SAML).](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Configuración de F5 (Kerberos avanzado)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Captura de pantalla que muestra la pantalla Single Sign On Service Settings (Valor de configuración del servicio de inicio de sesión único). ](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Especificación del certificado cargado desde la tarea 3

    ![Captura de pantalla que muestra la pantalla Edit SAML IdP Connector (Editar el conector de IdP de SAML).](./media/advance-kerbf5-tutorial/configure06.png)

    ![Captura de pantalla que muestra la pantalla Single Logout Service Settings (Valor de configuración del servicio de cierre de sesión único).](./media/advance-kerbf5-tutorial/configure07.png)

 1. Para configurar el SP de SAML, vaya a **Access > Federation > SAML Service Federation > Local SP Services > Create** (Acceso > Federación > Federación del servicio SAML > Servicios SP locales > Crear).

    ![Captura de pantalla que muestra la pantalla en la que puede crear un servicio de SP local.](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Haga clic en **OK**.

1. Seleccione la configuración de SP y haga clic en **Bind/UnBind IdP Connectors** (Enlazar/Desenlazar conectores de IdP).

     ![Captura de pantalla que muestra SAML Service Provider (Proveedor de servicios de SAML).](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Haga clic en **Agregar nueva fila** y seleccione el **conector IdP externo** creado en el paso anterior.

    ![Captura de pantalla que resalta el botón Agregar nueva fila.](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Para configurar el inicio de sesión único de Kerberos, seleccione **Access > Single Sign-on > Kerberos** (Acceso > Inicio de sesión único > Kerberos).

    >[!Note]
    >Necesitará que la cuenta de delegación de Kerberos se cree y se especifique. Consulte la sección Referencia de KCD (consulte el apéndice para ver las referencias a variables)

    • Origen de nombre de usuario `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Origen de nombre de territorio `session.logon.last.domain`

    ![Captura de pantalla que resalta Access > Single Sign On (Acceso > Inicio de sesión único).](./media/advance-kerbf5-tutorial/configure11.png)

1. Para configurar el perfil de acceso, seleccione **Access > Profile/Policies > Access Profile (per session policies)** [Acceso > Perfil/Directivas> Perfil de acceso (por directivas de sesión)].

    ![Captura de pantalla que resalta la pestaña Properties (Propiedades) en la opción de menú Profiles/Policies (Perfiles/Directivas).](./media/advance-kerbf5-tutorial/configure12.png)

    ![Captura de pantalla que muestra la pestaña SSO/Auth Domains (SSO/Dominios de autenticación).](./media/advance-kerbf5-tutorial/configure13.png)

    ![Captura de pantalla que muestra la pestaña Access Policy (Directiva de acceso).](./media/advance-kerbf5-tutorial/configure14.png)

    ![Captura de pantalla que muestra la pestaña Properties (Propiedades) en la directiva de acceso.](./media/advance-kerbf5-tutorial/configure15.png)

    ![Captura de pantalla que muestra las propiedades para la asignación de variables.](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![Captura de pantalla que muestra las propiedades de la consulta de AD.](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![Captura de pantalla que muestra la pestaña Branch Rules (Reglas de rama) y la regla Check Account (Comprobar cuenta).](./media/advance-kerbf5-tutorial/configure18.png)

    ![Captura de pantalla que muestra los cuadros de texto de variable personalizada y expresión personalizada.](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![Captura de pantalla que muestra los valores de los campos SSO Token Name (Nombre de token de SSO) y SSO Token Password (Contraseña de token de SSO).](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Para agregar un nuevo nodo, vaya a **Local Traffic > Nodes > Node List > +** (Tráfico local > Nodos > lista de nodos> +).

    ![Captura de pantalla que resalta Local Traffic > Nodes (Tráfico local > Nodos).](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Para crear un nuevo grupo, vaya a **Local Traffic > Pools > Pool List > Create** (Tráfico local > Grupos > Lista de grupos > Crear).

     ![Captura de pantalla que resalta Local Traffic > Groups (Tráfico local > Grupos).](./media/advance-kerbf5-tutorial/configure22.png)

 1. Para crear un nuevo servidor virtual, vaya a **Local Traffic > Virtual Servers > Virtual Server List > +** (Tráfico local > servidores virtuales > Lista de servidores virtuales > +).

    ![Captura de pantalla que resalta Local Traffic > Virtual Servers (Tráfico local > Servidores virtuales).](./media/advance-kerbf5-tutorial/configure23.png)

1. Especifique el perfil de acceso creado en el paso anterior.

    ![Captura de pantalla que muestra dónde especificar el perfil de acceso que ha creado.](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Configuración de la delegación Kerberos 

>[!Note]
>Para más detalles, consulte [aquí](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf).

* **Paso 1: Crear una cuenta de delegación**

    * Ejemplo
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Paso 2: Establecer SPN (en la cuenta de delegación de APM)**

    *  Ejemplo
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Paso 3: Delegación de SPN (para la cuenta App Service)**

    * Configure la delegación adecuada para la cuenta de delegación de F5.
    * En el ejemplo siguiente, se está configurando la cuenta de delegación de APM para KCD para la aplicación FRP-App1.superdemo.live.

        ![Captura de pantalla que muestra la pestaña Delegation (Delegación) de las propiedades de la cuenta de delegación de APM.](./media/advance-kerbf5-tutorial/configure25.png)

1. Proporcione los detalles tal como se mencionó en el documento de referencia anterior en [este vínculo](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Apéndice: las asignaciones de variables BIG-IP de F5 en SAML que se muestran a continuación:

    ![Captura de pantalla que muestra la pestaña Overview > Active Sessions (Información general > Sesiones activas).](./media/advance-kerbf5-tutorial/configure26.png)

    ![Captura de pantalla que muestra las variables y las claves de sesión.](./media/advance-kerbf5-tutorial/configure27.png) 

1. A continuación se muestra la lista completa de los atributos de SAML predeterminados. GivenName se representa mediante la siguiente cadena.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| Sesión | Atributo |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Creación de un usuario de prueba de F5

En esta sección, creará un usuario llamado B.Simon en F5. Trabaje con el [equipo de soporte técnico del cliente de F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para agregar los usuarios a la plataforma de F5. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de F5 en el panel de acceso, debería iniciar sesión automáticamente en la versión de F5 para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales acerca de cómo integrar aplicaciones SaaS con Azure Active Directory](./tutorial-list.md)

- [¿Qué es el acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [¿Qué es el acceso condicional en Azure Active Directory?](../conditional-access/overview.md)

- [Prueba de F5 con Azure AD](https://aad.portal.azure.com/)

- [Configuración del inicio de sesión único de F5 para la aplicación basada en encabezados](headerf5-tutorial.md)

- [Configuración del inicio de sesión único de F5 para la aplicación Kerberos](kerbf5-tutorial.md)

- [Integración de F5 BIG-IP APM y Azure AD para el acceso híbrido seguro](../manage-apps/f5-aad-integration.md)

- [Tutorial para implementar la máquina virtual de F5 BIG-IP Virtual Edition en IaaS de Azure para el acceso híbrido seguro](../manage-apps/f5-bigip-deployment-guide.md)

- [Tutorial para la integración del inicio de sesión único de Azure Active Directory con F5 BIG-IP para una VPN sin contraseña](../manage-apps/f5-aad-password-less-vpn.md)