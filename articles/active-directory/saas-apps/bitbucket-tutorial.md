---
title: 'Tutorial: Integración de Azure Active Directory con SAML SSO for Bitbucket by resolution GmbH | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAML SSO for Bitbucket by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 25c96d6e69a6b76e41cc84a88b5b470bbeeb4bc0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814049"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Tutorial: Integración de Azure Active Directory con SAML SSO for Bitbucket by resolution GmbH

En este tutorial, aprenderá a integrar SAML SSO for Bitbucket by resolution GmbH con Azure Active Directory (Azure AD). Al integrar SAML SSO for Bitbucket by resolution GmbH con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a SAML SSO for Bitbucket by resolution GmbH.
* Permitir que los usuarios inicien sesión automáticamente en SAML SSO for Bitbucket by resolution GmbH con sus cuentas de Azure AD.
* Administrar sus cuentas en una ubicación central: Azure Portal.


## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAML SSO for Bitbucket by resolution GmbH, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/)
* Una suscripción habilitada para el inicio de sesión único en SAML SSO for Bitbucket by resolution GmbH

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SAML SSO for Bitbucket by resolution GmbH admite el inicio de sesión único iniciado por **SP e IDP**
* SAML SSO for Bitbucket by resolution GmbH admite el aprovisionamiento de usuarios **Just In Time**


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Adición de SAML SSO for Bitbucket by resolution GmbH desde la galería

Para configurar la integración de SAML SSO for Bitbucket by resolution GmbH en Azure AD, debe agregar esta aplicación desde la galería a la lista de aplicaciones administradas de SaaS.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel izquierdo, seleccione **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SAML SSO for Bitbucket by resolution GmbH** en el cuadro de búsqueda.
1. Seleccione **SAML SSO for Bitbucket by resolution GmbH** en los resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Configure y pruebe el inicio de sesión único de Azure AD con SAML SSO for Bitbucket by resolution GmbH

Configure y pruebe el inicio de sesión único de Azure AD con SAML SSO for Bitbucket by resolution GmbH mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de SAML SSO for Bitbucket by resolution GmbH.

Para configurar el inicio de sesión único de Azure AD con SAML SSO for Bitbucket by resolution GmbH, siga estos pasos:


1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de SAML SSO for Bitbucket by resolution GmbH](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** , para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SAML SSO for Bitbucket by resolution GmbH](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** : para tener un homólogo de Britta Simon en SAML SSO for Bitbucket by resolution GmbH que esté vinculado a la representación de este usuario en Azure AD.
6. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

En esta sección va a habilitar el inicio de sesión único de Azure AD en Azure Portal.
 
1. En Azure Portal, en la página de integración de la aplicación **SAML SSO for Bitbucket by resolution GmbH**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, seleccione el icono con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración de SAML básica**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por **IDP**:


    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<server-base-url>/plugins/servlet/samlsso`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:
    
    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory** > **Usuarios** > **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Seleccione la casilla **Mostrar contraseña** y, a continuación, anote la contraseña.
   1. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a B.Simon para que use el inicio de sesión único de Azure, para lo cual le concederá acceso a SAML SSO for Bitbucket by resolution GmbH.

1. En Azure Portal, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SAML SSO for Bitbucket by resolution GmbH**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista de usuarios. A continuación, elija **Seleccionar** en la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>Configuración del inicio de sesión único de SAML SSO for Bitbucket by resolution GmbH

1. Inicie sesión en el sitio de empresa de SAML SSO for Bitbucket by resolution GmbH como administrador.

2. En el lado derecho de la barra de herramientas principal, haga clic en **Settings** (Configuración).

3. Vaya a la sección ACCOUNTS (CUENTAS) y haga clic en **SAML SingleSignOn** (Inicio de sesión único de SAML) en la barra de menús.

    ![El inicio de sesión único de SAML](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. En la página **SAML SIngleSignOn Plugin Configuration** (Configuración de complementos de inicio de sesión único de SAML), haga clic en **Add idp** (Agregar IdP). 

    ![Add idp (Agregar IdP)](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. En la página **Choose your SAML Identity Provider** (Elegir el proveedor de identidades SAML), siga estos pasos:

    ![El proveedor de identidades](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Para **Idp Type** (Tipo de IdP), elija **AZURE AD**.

    b. En el cuadro de texto **Name** (Nombre), escriba un nombre.

    c. En el cuadro de texto **Description** (Descripción), escriba la descripción.

    d. Haga clic en **Next**.

6. En la página **Identity provider configuration** (Configuración del proveedor de identidades), haga clic en el botón **Next** (Siguiente).

    ![La configuración de identidad](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  En la página **Import SAML Idp Metadata** (Importar metadatos de IdP de SAML), haga clic en **Load File** (Cargar archivo) para cargar el archivo **METADATA XML** que ha descargado de Azure Portal.

    ![Los metadatos de IdP](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Haga clic en **Next**.

9. Haga clic en **Save settings** (Guardar configuración).

    ![La operación de guardar](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Creación de un usuario de prueba de SAML SSO for Bitbucket by resolution GmbH

El objetivo de esta sección es crear un usuario llamado Britta Simon en SAML SSO for Bitbucket by resolution GmbH. SAML SSO for Bitbucket by resolution GmbH admite aprovisionamiento Just-In-Time y creación manual de usuarios, según sus necesidades. Para más información póngase en contacto con el [equipo de atención al cliente de SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de SAML SSO for Bitbucket by resolution GmbH, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya directamente a SAML SSO for Bitbucket by resolution GmbH y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de SAML SSO for Bitbucket by resolution para la que configuró el inicio de sesión único.

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de SAML SSO for Bitbucket by resolution GmbH en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de EasySSO for BitBucket para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que se ha configurado SAML SSO for Bitbucket by resolution GmbH, puede aplicar los controles de sesión que protegen su organización en tiempo real frente a la filtración e infiltración de información confidencial. Los controles de sesión proceden del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).