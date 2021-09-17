---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con KnowledgeOwl | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y KnowledgeOwl.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2021
ms.author: jeedes
ms.openlocfilehash: 61b8a15e4e765113455be0e8b37ba22b98e6e4ad
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271118"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con KnowledgeOwl

En este tutorial aprenderá a integrar KnowledgeOwl con Azure Active Directory (Azure AD). Al integrar KnowledgeOwl con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a KnowledgeOwl.
* Permitir que los usuarios inicien sesión automáticamente en KnowledgeOwl con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción de KnowledgeOwl con el inicio de sesión único habilitado.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* KnowledgeOwl admite el inicio de sesión único iniciado por **SP e IDP**.
* KnowledgeOwl admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-knowledgeowl-from-the-gallery"></a>Adición de KnowledgeOwl desde la galería

Para configurar la integración de KnowledgeOwl en Azure AD, es preciso agregar KnowledgeOwl desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **KnowledgeOwl** en el cuadro de búsqueda.
1. Seleccione **KnowledgeOwl** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-knowledgeowl"></a>Configuración y prueba del inicio de sesión único de Azure AD para KnowledgeOwl

Configure y pruebe el inicio de sesión único de Azure AD con KnowledgeOwl con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de KnowledgeOwl.

Para configurar y probar el inicio de sesión único de Azure AD con KnowledgeOwl, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en KnowledgeOwl](#configure-knowledgeowl-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Crear un usuario de prueba en KnowledgeOwl](#create-knowledgeowl-test-user)** : para tener un homólogo de B.Simon en KnowledgeOwl vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **KnowledgeOwl**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL con uno de los siguientes patrones:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. En el cuadro de texto **Dirección URL de respuesta**, escriba la dirección URL con uno de los siguientes patrones:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con uno de los siguientes patrones:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Estos valores no son reales. Debe actualizar este valor con la dirección URL de respuesta, el identificador y la dirección URL de inicio de sesión reales, tal como se explica más adelante en el tutorial.

1. La aplicación KnowledgeOwl espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación KnowledgeOwl espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre | Atributo de origen | Espacio de nombres |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (sin procesar)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificateraw.png)

1. En la sección **Set up KnowledgeOwl** (Configurar KnowledgeOwl), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a KnowledgeOwl mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **KnowledgeOwl**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-knowledgeowl-sso"></a>Configuración del inicio de sesión único en KnowledgeOwl

1. En otra ventana del explorador web, inicie sesión en el sitio de la empresa de KnowledgeOwl como administrador.

1. Haga clic en **Settings** (Configuración) y, a continuación, seleccione **SSO**.

    ![Captura de pantalla que muestra la opción SSO seleccionada en el menú de configuración.](./media/knowledgeowl-tutorial/settings-sso-dropdown.png)

1. En la pestaña **SAML Settings** (Configuración de SAML), realice los pasos siguientes:

    ![Captura de pantalla que muestra la opción SAML SSO Integration (Integración de SSO de SAML) donde puede realizar los cambios que se describen aquí.](./media/knowledgeowl-tutorial/sso-settings-required-fields.png)

    a. Seleccione **Enable SAML SSO** (Habilitar SSO de SAML).

    b. Copie el valor **SP Entity ID** (Id. de identidad de SP) en **Identificador (Id. de entidad)** en la sección **Configuración básica de SAML** de Azure Portal.

    c. Copie el valor de **SP Login URL** (Dirección URL de inicio de sesión SP) y péguelo en el cuadro de texto **Sign-on URL and Reply URL** (Direcciones URL de inicio de sesión y respuesta) de la sección **Configuración básica de SAML** de Azure Portal.

    d. En el cuadro de texto **IdP entityID** (Id. de entidad de IdP), pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    e. En el cuadro de texto **IdP Login URL** (Dirección URL de inicio de sesión de IdP), pegue el valor de la **URL de inicio de sesión** que ha copiado de Azure Portal.

    f. En el cuadro de texto **IdP Logout URL** (Dirección URL de cierre de sesión del IdP), pegue el valor de **URL de cierre de sesión** que ha copiado de Azure Portal.

    g. Cargue el certificado que descargó de Azure Portal haciendo clic en el vínculo **Upload** (Cargar) situado debajo de **IdP Certificate** (Certificado del IdP).

    h. Haga clic en **Guardar** en la parte inferior de la página.

    ![Captura de pantalla que muestra el botón Save (Guardar).](./media/knowledgeowl-tutorial/sso-settings-saml-save.png)

    i. Abra la pestaña **SAML Attribute Map** (Asignación de atributos de SAML) para asignar atributos y realice los pasos siguientes:

    ![Captura de pantalla que muestra la opción Map SAML Attributes (Asignar atributos de SAML) donde puede realizar los cambios que se indican aquí.](./media/knowledgeowl-tutorial/sso-settings-direct-attribute-fields.png)

    * Escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` en el cuadro de texto **Id. de SSO**.
    * Escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` en el cuadro de texto **Nombre de usuario / correo electrónico**.
    * Escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` en el cuadro de texto **Nombre**.
    * Escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` en el cuadro de texto **Apellidos**.

    j. Haga clic en **Guardar** en la parte inferior de la página.

    ![Captura de pantalla que muestra el botón Guardar.](./media/knowledgeowl-tutorial/sso-settings-direct-attribute-save.png)

### <a name="create-knowledgeowl-test-user"></a>Creación de un usuario de prueba de KnowledgeOwl

En esta sección se crea un usuario llamado B.Simon en KnowledgeOwl. KnowledgeOwl admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario deja de existir en KnowledgeOwl, se crea otro después de la autenticación.

> [!Note]
> Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de KnowledgeOwl, donde podrá iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de KnowledgeOwl e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de la aplicación KnowledgeOwl para la que configuró el inicio de sesión único. 

También puede usar el portal Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de KnowledgeOwl en el portal Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, debería iniciar sesión automáticamente en la instancia de la aplicación KnowledgeOwl para la que ha configurado el inicio de sesión único. Para más información acerca del portal Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado KnowledgeOwl, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
