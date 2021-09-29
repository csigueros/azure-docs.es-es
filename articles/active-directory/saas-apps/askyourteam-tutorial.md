---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con AskYourTeam | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 67907689f68d034ef3d098d6843c79a619a0ed58
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748100"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con AskYourTeam

En este tutorial aprenderá a integrar AskYourTeam con Azure Active Directory (Azure AD). Al integrar AskYourTeam con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a AskYourTeam.
* Permitir que los usuarios inicien sesión automáticamente en AskYourTeam con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en AskYourTeam.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* AskYourTeam admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="adding-askyourteam-from-the-gallery"></a>Incorporación de AskYourTeam desde la galería

Para configurar la integración de AskYourTeam en Azure AD, será preciso que agregue AskYourTeam desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **AskYourTeam** en el cuadro de búsqueda.
1. Seleccione **AskYourTeam** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>Configuración y prueba del inicio de sesión único de Azure AD para AskYourTeam

Configure y pruebe el inicio de sesión único de Azure AD con AskYourTeam utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de AskYourTeam.

Para configurar y probar el inicio de sesión único de Azure AD con AskYourTeam, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en AskYourTeam](#configure-askyourteam-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de AskYourTeam](#create-askyourteam-test-user)** , para tener un homólogo de B.Simon en AskYourTeam que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **AskYourTeam**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Estos valores no son reales. La actualización de estos valores por los valores reales de dirección URL de respuesta y dirección URL de inicio de sesión se explica más adelante en el tutorial.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar AskYourTeam**, copie las direcciones URL adecuadas en función de sus necesidades.

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

En esta sección va a permitir que B.Simon acceda a AskYourTeam mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **AskYourTeam**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-askyourteam-sso"></a>Configuración del inicio de sesión único en AskYourTeam

1. Para automatizar la configuración en AskYourTeam, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar AskYourTeam** para ir a la aplicación. Desde ahí, especifique las credenciales de administrador para iniciar sesión en AskYourTeam. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 7.

    ![Configuración](common/setup-sso.png)

3. Si desea configurar AskYourTeam manualmente, abra otra ventana del explorador web e inicie sesión como administrador en el sitio web de AskYourTeam.

1. Haga clic en **Mi organización**.

    ![Captura de pantalla que muestra el vínculo Mi organización.](./media/askyourteam-tutorial/user1.png)

1. Haga clic en **Integrations** (Integraciones).

    ![Captura de pantalla que muestra el vínculo Integrations (Integraciones).](./media/askyourteam-tutorial/configure1.png)

1. Haga clic en **Edit Settings** (Editar configuración).

    ![Captura de pantalla que muestra el mensaje Single Sign-On (Inicio de sesión único) con un botón Edit Settings (Editar configuración).](./media/askyourteam-tutorial/configure2.png)

1. En la página **Edit Single Sign-On Integration** (Editar la integración de inicio de sesión único), siga estos pasos: 

    ![Captura de pantalla que muestra Edit Single Sign-On Integration (Editar la integración del inicio de sesión único) para especificar los valores para este paso.](./media/askyourteam-tutorial/configure3.png)

    a. En el cuadro de texto **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    b. En el cuadro de texto **SAML Entity ID** (Id. de entidad SAML), pegue el valor de **Identificador de Azure AD** que ha copiado de Azure Portal.

    c. En el cuadro de texto **Sign-Out URL** (URL de cierre de sesión), pegue el valor de **Dirección URL de cierre de sesión** que ha copiado de Azure Portal.

    d. Abra en el Bloc de notas el archivo de **certificado (base64)** que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **SAML Signing Certificate - Base64** (Certificado de firma de SAML - Base64).

    > [!NOTE]
    > También puede cargar el archivo **XML de metadatos de federación** haciendo clic en la opción **Choose File** (Elegir archivo).

    e. Copie el valor de **Reply URL (Assertion Consumer Service URL)** (Dirección URL de respuesta [URL del Servicio de consumidor de aserciones]) y péguelo en el cuadro **URL de respuesta**, en la sección **Configuración básica de SAML** de Azure Portal.

    f. Copie el valor de **Sign on URL** (URL de inicio de sesión) y péguelo en el cuadro de texto **URL de inicio de sesión** de la sección **Configuración básica de SAML** de Azure Portal.

    g. Haga clic en **Save**(Guardar).

### <a name="create-askyourteam-test-user"></a>Creación de un usuario de prueba de AskYourTeam

1. En otra ventana del explorador web, inicie sesión en el sitio web de AskYourTeam como administrador.

1. Haga clic en **Mi organización**.

    ![Captura de pantalla que muestra el vínculo Mi organización, desde donde comenzar esta tarea.](./media/askyourteam-tutorial/user1.png)

1. Haz clic en **Users** (Usuarios) y seleccione **New User** (Nuevo usuario).

    ![Captura de pantalla que muestra el vínculo Users (Usuarios) con New User (Nuevo usuario).](./media/askyourteam-tutorial/user2.png)

1. En la sección **New user** (Nuevo usuario), lleve a cabo estos pasos:

    ![Captura de pantalla que muestra una sección New User (Nuevo usuario) en la que se especifica la información de usuario.](./media/askyourteam-tutorial/user3.png)

    1. En el cuadro de texto **First name** (Nombre), escriba el nombre de usuario.

    1. En el cuadro de texto **Last name** (Apellidos), escriba los apellidos del usuario.

    1. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico del usuario, como B.Simon@contoso.com.

    1. Seleccione el valor de **Rol** para el usuario según el requisito de su organización.

    1. Haga clic en **Save**(Guardar).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de AskYourTeam, desde donde puede comenzar el flujo de inicio de sesión.

* Acceda directamente a la URL de inicio de sesión de AskYourTeam y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de AskYourTeam para la que ha configurado el inicio de sesión único.

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de AskYourTeam en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de AskYourTeam para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado AskYourTeam, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).