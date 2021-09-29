---
title: 'Tutorial: Integración de Azure Active Directory con Palo Alto Networks - Aperture | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Palo Alto Networks - Aperture.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 48b90f46157d4510714d99f642917fdbeb0e9b8f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746576"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Tutorial: Integración de Azure Active Directory con Palo Alto Networks - Aperture

En este tutorial, aprenderá a integrar Palo Alto Networks - Aperture con Azure Active Directory (Azure AD). Al integrar Palo Alto Networks - Aperture con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Palo Alto Networks - Aperture.
* Permitir que los usuarios inicien sesión automáticamente en Palo Alto Networks - Aperture con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Palo Alto Networks - Aperture.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Palo Alto Networks - Aperture admite el inicio de sesión único comenzado por **SP** e **IDP**.

## <a name="add-palo-alto-networks---aperture-from-the-gallery"></a>Adición de Palo Alto Networks - Aperture desde la galería

Para configurar la integración de Palo Alto Networks - Aperture en Azure AD, es preciso agregar Palo Alto Networks - Aperture desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Palo Alto Networks - Aperture** en el cuadro de búsqueda.
1. Seleccione **Palo Alto Networks - Aperture** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD

En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con Palo Alto Networks - Aperture con un usuario de prueba llamado **B.Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Palo Alto Networks - Aperture.

Para configurar y probar el inicio de sesión único de Azure AD con Palo Alto Networks - Aperture, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de Palo Alto Networks - Aperture](#configure-palo-alto-networks---aperture-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Palo Alto Networks - Aperture](#create-palo-alto-networks---aperture-test-user)**: para tener un homólogo de Britta Simon en Palo Alto Networks - Aperture que esté vinculado a la representación del usuario en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Palo Alto Networks - Aperture**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con [el equipo de soporte técnico del cliente de Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Configurar Palo Alto Networks - Aperture**, copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon acceda a Palo Alto Networks - Aperture mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Palo Alto Networks - Aperture**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-palo-alto-networks---aperture-sso"></a>Configuración del inicio de sesión único de Palo Alto Networks - Aperture

1. En otra ventana del explorador web, inicie sesión en el sitio web de Palo Alto Networks - Aperture como administrador.

2. En la barra de menús superior, haga clic en **SETTINGS** (CONFIGURACIÓN).

    ![La pestaña Configuración](./media/paloaltonetworks-aperture-tutorial/settings.png)

3. Vaya a la sección **APPLICATION** (APLICACIÓN) y haga clic en el formulario **Authentication** (Autenticación) en el lado izquierdo del menú.

    ![La pestaña Autenticación](./media/paloaltonetworks-aperture-tutorial/authentication.png)
    
4. En la página **Authentication** (Autenticación), realice los siguientes pasos:
    
    ![La pestaña Autenticación](./media/paloaltonetworks-aperture-tutorial/tab.png)

    a. Active **Enable Single Sign-On(Supported SSP Providers are Okta, One login)** (Habilitar inicio de sesión único [Los proveedores SSP admitidos son Okta, One login]) en el campo **Single Sign-On** (Inicio de sesión único).

    b. En el cuadro de texto **Identity Provider ID** (Identificador del proveedor de identidades), pegue el valor de **Azure AD Identifier** (Identificador de Azure AD) que copió de Azure Portal.

    c. Haga clic en **Choose File** (Elegir archivo) para cargar el certificado descargado de Azure AD en **Identity Provider Certificate** (Certificado del proveedor de identidades).

    d. En el cuadro de texto **Identity Provider SSO URL** (Dirección URL de inicio de sesión único del proveedor de identidades), pegue el valor de **Dirección URL de inicio de sesión**  que ha copiado de Azure Portal.

    e. Revise la información del proveedor de identidades en la sección **Aperture Info** (Información de Aperture) y descargue el certificado del campo **Aperture Key** (Clave de Aperture).

    f. Haga clic en **Save**(Guardar).


### <a name="create-palo-alto-networks---aperture-test-user"></a>Creación de un usuario de prueba de Palo Alto Networks - Aperture

En esta sección, creará un usuario llamado Britta Simon en Palo Alto Networks - Aperture. Trabaje con el [equipo de soporte técnico del cliente de Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para agregar los usuarios a la plataforma Palo Alto Networks - Aperture. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Palo Alto Networks - Aperture, desde donde puede comenzar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Palo Alto Networks - Aperture y comience el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Palo Alto Networks - Aperture para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Palo Alto Networks - Aperture en Aplicaciones, si está configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión y, si está configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Palo Alto Networks - Aperture para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Palo Alto Networks - Aperture, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración y la infiltración de la información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).