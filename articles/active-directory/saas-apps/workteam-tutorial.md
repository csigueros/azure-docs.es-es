---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Workteam | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workteam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: a0072466fbb351a5fa98689b4a6d54989fce5e6f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124745087"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Workteam

En este tutorial, aprenderá a integrar Workteam con Azure Active Directory (Azure AD). Al integrar Workteam con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Workteam.
* Permitir que los usuarios inicien sesión automáticamente en Workteam con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Workteam.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Workteam admite el inicio de sesión único iniciado por **SP e IDP**.
* Workteam admite el [aprovisionamiento automatizado de usuarios](workteam-provisioning-tutorial.md).

## <a name="add-workteam-from-the-gallery"></a>Adición de Workteam desde la galería

Para configurar la integración de Workteam en Azure AD, es preciso agregar Workteam desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Workteam** en el cuadro de búsqueda.
1. Seleccione **Workteam** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-workteam"></a>Configuración y prueba del inicio de sesión único de Azure AD para Workteam

Configure y pruebe el inicio de sesión único de Azure AD con Workteam mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Workteam.

Para configurar y probar el inicio de sesión único de Azure AD con Workteam, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Workteam](#configure-workteam-sso)**, para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Workteam](#create-workteam-test-user)**, para tener un homólogo de B.Simon en Workteam que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Workteam**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada en el modo iniciado por **IDP** y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe hacer clic en el botón **Guardar**.

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.workte.am`

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Workteam**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Workteam mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Workteam**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-workteam-sso"></a>Configuración del inicio de sesión único en Workteam

1. Para automatizar la configuración en Workteam, debe instalar la **extensión del explorador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar Workteam** para ir a la aplicación Workteam. Desde ahí, proporcione las credenciales de administrador para iniciar sesión en esta aplicación. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 6.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Workteam manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Workteam como administrador y haga lo siguiente:

4. En la esquina superior derecha, haga clic en el **logotipo del perfil** y, a continuación, haga clic en **Configuración de la organización**. 

    ![Captura de pantalla que muestra la configuración de Workteam.](./media/workteam-tutorial/settings.png)

5. En la sección **AUTENTICACIÓN**, haga clic en el **icono de configuración**.

     ![Captura de pantalla que muestra Workteam en Azure.](./media/workteam-tutorial/azure.png)

6. En la página **SAML Settings** (Configuración de SAML), realice los pasos siguientes:

     ![Captura de pantalla que muestra la configuración de SAML de Workteam.](./media/workteam-tutorial/certificate.png)

    a. En **SAML IdP** (Proveedor de identidades de SAML), seleccione **AD Azure**.

    b. En el cuadro de texto **SAML Single Sign-On Service URL** (Dirección URL de inicio de sesión único de SAML), pegue el valor de la **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c. En el cuadro de texto **SAML Entity ID** (Identificador de identidad de SAML), pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    d. En el Bloc de notas, abra el **certificado codificado en Base 64** que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro **Certificado de firma SAML (Base64)**.

    e. Haga clic en **OK**.

### <a name="create-workteam-test-user"></a>Creación de un usuario de prueba de Workteam

Para permitir que los usuarios de Azure AD inicien sesión en Workteam, tienen que aprovisionarse en Workteam. En Workteam, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en Workteam como administrador de seguridad.

2. En la parte superior central de la página **Configuración de la organización** página, haga clic en **USUARIOS** y, a continuación, haga clic en **NUEVO USUARIO**.

    ![Captura de pantalla que muestra el usuario de Workteam.](./media/workteam-tutorial/user.png)

3. En la página **Nuevo empleado**, realice los pasos siguientes:

    ![Captura de pantalla que muestra el nuevo usuario de Workteam.](./media/workteam-tutorial/new-user.png)

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de usuario de la siguiente manera: **B.Simon**.

    b. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario, por ejemplo, `B.Simon\@contoso.com`.

    c. Haga clic en **OK**.

> [!NOTE]
> Workteam también admite el aprovisionamiento automático de usuarios. [Aquí](./workteam-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Workteam, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Workteam e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Workteam para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Workteam en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, debería iniciar sesión automáticamente en la instancia de Workteam para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Workteam, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).