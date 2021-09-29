---
title: 'Tutorial: Integración de Azure Active Directory con Cisco Umbrella Admin SSO | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Cisco Umbrella Admin SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: f4cef83309d1579a9360738193c0ea4771883ae1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124813903"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Tutorial: Integración de Azure Active Directory con Cisco Umbrella Admin SSO

En este tutorial aprenderá a integrar Cisco Umbrella Admin SSO con Azure Active Directory (Azure AD). Al integrar Cisco Umbrella Admin SSO con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Cisco Umbrella Admin SSO.
* Permitir que los usuarios inicien sesión automáticamente en Cisco Umbrella Admin SSO con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Cisco Umbrella Admin SSO.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Cisco Umbrella Admin SSO admite el inicio de sesión único habilitado por **SP e IDP**.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Incorporación de Cisco Umbrella Admin SSO desde la galería

Para configurar la integración de Cisco Umbrella Admin SSO en Azure AD, debe agregar la aplicación desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Cisco Umbrella Admin SSO** en el cuadro de búsqueda.
1. Seleccione **Cisco Umbrella Admin SSO** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD para Cisco Umbrella Admin SSO

Configure y pruebe el inicio de sesión único de Azure AD con Cisco Umbrella Admin SSO mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Cisco Umbrella Admin SSO.

Para configurar y probar el inicio de sesión único de Azure AD con Cisco Umbrella SSO, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Cisco Umbrella Admin SSO](#configure-cisco-umbrella-admin-sso-sso)** , para establecer los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Cisco Umbrella Admin SSO](#create-cisco-umbrella-admin-sso-test-user)** , para tener un homólogo de B.Simon en Cisco Umbrella Admin SSO vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Cisco Umbrella Admin SSO**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, el usuario no tiene que realizar ningún paso porque la aplicación ya se ha integrado previamente con Azure.

    a. Si desea configurar la aplicación en modo iniciado por **SP**, realice los siguientes pasos:

    b. Haga clic en **Establecer direcciones URL adicionales**.

    c. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://login.umbrella.com/sso`

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar **XML de metadatos** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Configurar Cisco Umbrella Admin SSO**, copie las URL necesarias.

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

En esta sección, va a permitir que B.Simon acceda a Cisco Umbrella Admin SSO mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Cisco Umbrella Admin SSO**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Configuración del inicio de sesión único de Cisco Umbrela Admin SSO

1. En otra ventana del explorador, inicie sesión en el sitio de Cisco Umbrella Admin SSO como administrador.

2. En el lado izquierdo del menú, haga clic en **Administrador**, vaya a **Autenticación** y, a continuación, haga clic en **SAML**.

    ![El administrador](./media/cisco-umbrella-tutorial/admin.png)

3. Elija **Otro** y haga clic en **SIGUIENTE**.

    ![El otro](./media/cisco-umbrella-tutorial/other.png)

4. En la página **Metadatos de Cisco Umbrella Admin SSO**, haga clic en **SIGUIENTE**.

    ![Los metadatos](./media/cisco-umbrella-tutorial/metadata.png)

5. En la pestaña **Cargar metadatos**, si había preconfigurado SAML, seleccione la opción **Haga clic aquí para cambiarlos** y siga los pasos siguientes.

    ![El siguiente](./media/cisco-umbrella-tutorial/next.png)

6. En la **Opción A: Cargar archivo XML**, cargue el archivo **XML de metadatos de federación** que descargó desde Azure Portal y, una vez que los siguientes valores se autorrellenen automáticamente tras la carga de los metadatos, haga clic en **SIGUIENTE**.

    ![El método ChooseFile](./media/cisco-umbrella-tutorial/choose-file.png)

7. En la sección **Validación de la configuración de SAML**, haga clic en **PROBAR LA CONFIGURACIÓN DE SAML**.

    ![La prueba](./media/cisco-umbrella-tutorial/test.png)

8. Haga clic en **GUARDAR**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Creación de un usuario de prueba de Cisco Umbrella Admin SSO

Para permitir que los usuarios de Azure AD inicien sesión en Cisco Umbrella Admin SSO, tiene que aprovisionarlos en Cisco Umbrella Admin SSO.  
En el caso de Cisco Umbrella Admin SSO, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. En otra ventana del explorador, inicie sesión en el sitio de Cisco Umbrella Admin SSO como administrador.

2. En el lado izquierdo del menú, haga clic en **Administrador** y vaya a **Cuentas**.

    ![La cuenta](./media/cisco-umbrella-tutorial/account.png)

3. En la página **Cuentas**, haga clic en **Agregar** en el lado superior derecho de la página y siga los siguientes pasos.

    ![El usuario](./media/cisco-umbrella-tutorial/create-user.png)

    a. En el campo **Nombre**, escriba el nombre, como **Britta**.

    b. En el campo **Apellido**, escriba el apellido, como **simon**.

    c. En **Elegir rol de administrador delegado**, seleccione su rol.

    d. En el campo **Email address** (Dirección de correo electrónico), escriba la dirección de correo electrónico de un usuario, por ejemplo, **brittasimon\@contoso.com**.

    e. En el campo **Contraseña**, escriba su contraseña.

    f. En el campo **Confirmar contraseña**, vuelva a escribir su contraseña.

    g. Haga clic en **CREATE** (Crear).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Cisco Umbrella Admin SSO, desde donde puede comenzar el flujo de inicio de sesión.  

* Acceda directamente a la URL de inicio de sesión de Cisco Umbrella Admin SSO y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Cisco Umbrella Admin SSO para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Cisco Umbrella Admin SSO en Aplicaciones, si está configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión y, si está configurado en modo IDP, debería iniciar sesión automáticamente en la instancia de Cisco Umbrella Admin SSO para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Cisco Umbrella Admin SSO, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).