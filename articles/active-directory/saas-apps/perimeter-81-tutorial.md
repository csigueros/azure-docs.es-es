---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Perimeter 81 | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Perimeter 81.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: fd23ee8ff4d3ff6e32647b18c8deac57f1bb5005
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826049"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeter-81"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Perimeter 81

En este tutorial, aprenderá a integrar Perimeter 81 con Azure Active Directory (Azure AD). Al integrar Perimeter 81 con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Perimeter 81.
* Permitir que los usuarios inicien sesión automáticamente en Perimeter 81 con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Perimeter 81.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Perimeter 81 admite el inicio de sesión único iniciado por **SP e IDP**.
* Perimeter 81 admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="adding-perimeter-81-from-the-gallery"></a>Incorporación de Perimeter 81 desde la galería

Para configurar la integración de Perimeter 81 en Azure AD, debe agregar la aplicación desde la galería a su lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Perimeter 81** en el cuadro de búsqueda.
1. Seleccione **Perimeter 81** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-perimeter-81"></a>Configuración y prueba del inicio de sesión único de Azure AD para Perimeter 81

Configure y pruebe el inicio de sesión único de Azure AD con Perimeter 81 mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Perimeter 81.

Para configurar y probar el inicio de sesión único de Azure AD con Perimeter 81, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Perimeter 81](#configure-perimeter-81-sso)** , para definir los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Perimeter 81](#create-perimeter-81-test-user)** , para tener un homólogo de B.Simon vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Perimeter 81**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba un valor con el siguiente patrón: `urn:auth0:perimeter81:<SUBDOMAIN>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://auth.perimeter81.com/login/callback?connection=<SUBDOMAIN>`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.perimeter81.com`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico al cliente de clientes de Perimeter 81](mailto:support@perimeter81.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Perimeter 81**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Perimeter 81 mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Perimeter 81**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-perimeter-81-sso"></a>Configuración del inicio de sesión único de Perimeter 81

1. Para automatizar la configuración en Perimeter 81, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar Perimeter 81** para ir a la aplicación del mismo nombre. En ella, escriba las credenciales de administrador para iniciar sesión en Perimeter 81. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 7.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Perimeter 81 manualmente, en otra ventana del explorador web, inicie sesión en su sitio empresarial de Perimeter 81 como administrador.

4. Vaya a **Settings** (Configuración) y haga clic en **Identity Providers** (Proveedores de identidades).

    ![Configuración de Perimeter 81](./media/perimeter-81-tutorial/settings.png)

5. Haga clic en el botón **Add Provider** (Agregar proveedor).

    ![Adición de proveedor en Perimeter 81](./media/perimeter-81-tutorial/add-provider.png)

6. Seleccione **SAML 2.0 Identity Providers** (Proveedores de identidades SAML 2.0) y haga clic en el botón **Continue** (Continuar).

    ![Adición de proveedor de identidades en Perimeter 81](./media/perimeter-81-tutorial/add-identity-provider.png)

7. En la sección **SAML 2.0 Identity Providers** (Proveedores de identidades SAML 2.0), realice los pasos siguientes:

    ![Configuración de SAML en Perimeter 81](./media/perimeter-81-tutorial/setting-up-saml.png)

    a. En el cuadro de texto **Sign-in URL** (Dirección URL de inicio de sesión), pegue el valor de **Dirección URL de inicio de sesión** que copió de Azure Portal.

    b. En el cuadro de texto **Domain Aliases** (Alias de dominio), escriba el valor del alias de dominio.

    c. Abra el **certificado (Base64)** descargado de Azure Portal en el Bloc de notas y pegue el contenido en el cuadro de texto **X.509 Signing Certificate** (Certificado de firma de X.509).

    > [!NOTE]
    > También puede hacer clic en **Upload PEM/CERT File** (Cargar archivo PEM/CERT) para cargar el **certificado (Base64)** que descargó de Azure Portal.
    
    d. Haga clic en **Done**(Listo).

### <a name="create-perimeter-81-test-user"></a>Creación de un usuario de prueba de Perimeter 81

En esta sección, se creará un usuario llamado Britta Simon en Perimeter 81. Perimeter 81 admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe aún Perimeter 81, se creará uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Perimeter 81, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Acceda directamente a la URL de inicio de sesión de Perimeter 81 y ponga en marcha el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Perimeter 81 para la que ha configurado el inicio de sesión único.

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Perimeter 81 en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Perimeter 81 para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Perimeter 81, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).