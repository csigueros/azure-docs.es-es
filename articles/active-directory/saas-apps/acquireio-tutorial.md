---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con AcquireIO | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AcquireIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/27/2021
ms.author: jeedes
ms.openlocfilehash: 3a4d924b92fd2e80df816f13f5b7af7c346d9ecf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124770612"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con AcquireIO

En este tutorial, obtendrá información sobre cómo integrar AcquireIO con Azure Active Directory (Azure AD). Al integrar AcquireIO con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a AcquireIO.
* Permitir que los usuarios puedan iniciar sesión automáticamente en AcquireIO con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en AcquireIO.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* AcquireIO admite el inicio de sesión único iniciado por **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-acquireio-from-the-gallery"></a>Adición de AcquireIO desde la galería

Para configurar la integración de AcquireIO en Azure AD, deberá agregar AcquireIO desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **AcquireIO** en el cuadro de búsqueda.
1. Seleccione **AcquireIO** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-acquireio"></a>Configuración y prueba del inicio de sesión único de Azure AD para AcquireIO

Configure y pruebe el inicio de sesión único de Azure AD con AcquireIO mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de AcquireIO.

Para configurar y probar el inicio de sesión único de Azure AD con AcquireIO, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en AcquireIO](#configure-acquireio-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en AcquireIO](#create-acquireio-test-user)** : para tener un homólogo de B.Simon en AcquireIO vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **AcquireIO**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Este valor no es real. Obtendrá la dirección URL de respuesta real, que se explica más adelante en la sección **Configuración de AcquireIO** del tutorial. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up AcquireIO** (Configurar AcquireIO), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B. Simon acceda a AcquireIO utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **AcquireIO**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-acquireio-sso"></a>Configuración del inicio de sesión único en AcquireIO

1. Para automatizar la configuración en AcquireIO, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

1. Después de agregar la extensión al explorador, haga clic en **Set up AcquireIO** (Configurar AcquireIO), lo cual le dirigirá a la aplicación del mismo nombre. En ella, escriba las credenciales de administrador para iniciar sesión en AcquireIO. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 6.

    ![Configuración](common/setup-sso.png)

1. Si desea configurar AcquireIO manualmente, en otra ventana del explorador web, inicie sesión en AcquireIO como administrador.

1. En el lado izquierdo del menú, haga clic en **App Store**.

    ![Captura de pantalla que resalta App Store.](./media/acquireio-tutorial/menu.png)

1. Desplácese hacia abajo hasta **Active Directory** y haga clic en **Instalar**.

    ![Captura de pantalla que resalta la sección Active Directory y el botón Install (Instalar).](./media/acquireio-tutorial/install-button.png)

1. En el elemento emergente de Active Directory, realice los siguientes pasos:

    ![Captura de pantalla que muestra la pantalla Active Directory.](./media/acquireio-tutorial/configuration.png)

    a. Haga clic en **Copiar** para copiar la dirección URL de respuesta de la instancia y péguela en el cuadro de texto **URL de respuesta** de la sección **Configuración básica de SAML** de Azure Portal.

    b. En el cuadro de texto **Login URL** (Dirección URL de inicio de sesión), pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c. Abra el certificado codificado en Base64 con el Bloc de notas, copie su contenido y péguelo en el cuadro de texto **Certificado X.509**.

    d. Haga clic en **Conectar ahora**.

### <a name="create-acquireio-test-user"></a>Creación de un usuario de prueba de AcquireIO

Para permitir que los usuarios de Azure AD inicien sesión en AcquireIO, deben aprovisionarse en AcquireIO. En AcquireIO, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. En otra ventana del explorador web, inicie sesión en AcquireIO como administrador.

1. En el lado izquierdo del menú, haga clic en **Perfiles** y vaya a **Agregar perfil**.

    ![Captura de pantalla que resalta Profiles (Perfiles) en el menú del lado izquierdo de la pantalla y la opción Add Profile (Agregar perfil).](./media/acquireio-tutorial/profile.png)

1. En el elemento emergente **Agregar cliente**, lleve a cabo los pasos siguientes:

    ![Configuración de AcquireIO.](./media/acquireio-tutorial/add-profile.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre de usuario, por ejemplo, **B.simon**.

    b. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario; por ejemplo, **B.simon@contoso.com** .

    c. Haga clic en **Enviar**.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; iniciará sesión automáticamente en la instancia de AcquireIO para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic el icono de AcquireIO en el panel de acceso, iniciará sesión automáticamente en la instancia de AcquireIO para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado AcquireIO, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).