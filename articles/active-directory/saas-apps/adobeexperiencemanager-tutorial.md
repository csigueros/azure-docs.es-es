---
title: 'Tutorial: Integración de Azure Active Directory con Adobe Experience Manager | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 0592404cbc7a2960466fb84d7739c0787dbaa043
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803273"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Integración de Azure Active Directory con Adobe Experience Manager

En este tutorial, aprenderá a integrar Adobe Experience Manager con Azure Active Directory (Azure AD). Al integrar Adobe Experience Manager con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Adobe Experience Manager.
* Habilitar que los usuarios inicien sesión automáticamente en Adobe Experience Manager con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para inicio de sesión único (SSO) en Adobe Experience Manager

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Adobe Experience Manager admite el inicio de sesión único iniciado por **SP y IDP**

* Adobe Experience Manager admite el aprovisionamiento de usuarios **Just In Time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Incorporación de Adobe Experience Manager desde la galería

Para configurar la integración de Adobe Experience Manager en Azure AD, será preciso que lo agregue desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Adobe Experience Manager** en el cuadro de búsqueda.
1. Seleccione **Adobe Experience Manager** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Configuración y prueba del inicio de sesión único de Azure AD para Adobe Experience Manager

Configure y pruebe el inicio de sesión único de Azure AD con Adobe Experience Manager mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Adobe Experience Manager.

Para configurar el inicio de sesión único de Azure AD con Adobe Experience Manager, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de Adobe Experience Manager](#configure-adobe-experience-manager-sso)** : para configurar los valores de inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba en Adobe Experience Manager](#create-adobe-experience-manager-test-user)** : para tener un homólogo de Britta Simon en Adobe Experience Manager que esté vinculado a su representación en Azure AD.
6. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Adobe Experience Manager**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba un valor único que se define también en el servidor AEM.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. Actualice la dirección URL de respuesta con la dirección URL de respuesta real. Para obtener este valor, póngase en contacto con el [equipo de soporte técnico de Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL de servidor de Adobe Experience Manager.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Configurar Adobe Experience Manager**, copie las direcciones URL que necesite.

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

En esta sección, habilitará a B.Simon para que use el inicio de sesión único de Azure, para lo cual le concederá acceso a Adobe Experience Manager.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Adobe Experience Manager**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-adobe-experience-manager-sso"></a>Configuración del inicio de sesión único de Adobe Experience Manager

1. En otra ventana del explorador, abra el portal de administración de **Adobe Experience Manager**.

2. Seleccione **Settings** >  (Configuración) **Security** >  (Seguridad) **Users** (Usuarios).

    ![Captura de pantalla que muestra el icono Usuarios en Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Seleccione **Administrator** (Administrador) o cualquier otro usuario pertinente.

    ![Captura de pantalla que resalta el usuario Administrator (Administrador).](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Seleccione **Account Settings** > **Manage TrustStore** (Configuración de la cuenta > Administrar TrustStore).

    ![Captura de pantalla que muestra Manage TrustStore (Administrar TrustStore) en Account settings (Configuración de cuenta).](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. En **Add Certificate from CER file** (Agregar certificado desde archivo CER), haga clic en **Select Certificate File** (Seleccionar archivo de certificado). Busque el archivo de certificado que ha descargado de Azure Portal y selecciónelo.

    ![Captura de pantalla que resalta el botón Select Certificate File (Seleccionar archivo de certificado).](./media/adobe-experience-manager-tutorial/user-2.png)

6. El certificado se agrega a TrustStore. Anote el alias del certificado.

    ![Captura de pantalla que muestra que el certificado se ha agregado a TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. En la página **Users** (Usuarios), seleccione **authentication-service**.

    ![Captura que resalta el servicio de autenticación en la pantalla.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Seleccione **Account Settings** >  (Configuración de la cuenta) **Create/Manage KeyStore** (Crear o administrar KeyStore). Proporcione una contraseña para crear KeyStore.

    ![Captura de pantalla que resalta Manage KeyStore (Administrar KeyStore).](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Vuelva a la pantalla de administración. Después, seleccione **Settings** > **Operations** > **Web Console** (Configuración > Operaciones > Consola web).

    ![Captura de pantalla que resalta Web Console (Consola web) en Operations (Operaciones) en la sección Settings (Configuración).](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Se abre la página de configuración.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Busque **Adobe Granite SAML 2.0 Authentication Handler** (Controlador de autenticación de Adobe Granite SAML 2.0). Después, seleccione el botón **Agregar**.

    ![Captura de pantalla que resalta Adobe Granite SAML 2.0 Authentication Handler (Controlador de autenticación de Adobe Granite SAML 2.0).](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. En esta página, realice las acciones siguientes.

    ![Botón Configurar inicio de sesión único](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. En el cuadro **Path** (Ruta de acceso), escriba **/** .

    b. En el cuadro **IDP URL** (URL de IDP), escriba el valor de **Login URL** (URL de inicio de sesión) que copió de Azure Portal.

    c. En el cuadro **IDP Certificate Alias** (Alias de certificado de IDP), introduzca el valor del **alias de certificado** que agregó en TrustStore.

    d. En el cuadro de texto **Security Provided Entity ID** (Identificador de entidad de seguridad proporcionado), escriba el valor único de **Azure AD Identifier** (Identificador de Azure AD) que se ha configurado en Azure Portal.

    e. En el cuadro **Assertion Consumer Service URL** (URL del Servicio de consumidor de aserciones), escriba el valor de **Reply URL** (URL de respuesta) que se ha configurado en Azure Portal.

    f. En el cuadro **Password of Key Store** (Contraseña del almacén de claves), escriba la **contraseña**, que ha establecido en el almacén de claves.

    g. En el cuadro **User Attribute ID** (Identificador de atributo de usuario), escriba **Name ID** (Identificador de nombre) u otro identificador de usuario que sean pertinentes en su caso.

    h. Seleccione **Autocreate CRX Users** (Crear automáticamente usuarios de CRX).

    i. En el cuadro **Logout URL** (URL de cierre de sesión), pegue la dirección de la **URL de cierre de sesión** que obtuvo de Azure Portal.

    j. Seleccione **Guardar**.

### <a name="create-adobe-experience-manager-test-user"></a>Creación de un usuario de prueba de Adobe Experience Manager

En esta sección, se crea un usuario denominado Britta Simon en Adobe Experience Manager. Si ha seleccionado la opción **Autocreate CRX Users** (Crear automáticamente usuarios de CRX), los usuarios se crearán automáticamente tras la autenticación correcta.

Si desea crear los usuarios manualmente, trabaje con el [equipo de soporte técnico de Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para agregar los usuarios en la plataforma de Adobe Experience Manager.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Adobe Experience Manager, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Adobe Experience Manager y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Adobe Experience Manager para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Adobe Experience Manager en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Adobe Experience Manager para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Adobe Experience Manager, puede aplicar el control de sesión, que protege a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).