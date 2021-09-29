---
title: 'Tutorial: Integración del inicio de sesión único de Azure Active Directory con Workware | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workware.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 5ed4be15346e1f3248773472b68e8972457c34fb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785465"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Tutorial: Integración del inicio de sesión único de Azure Active Directory con Workware

En este tutorial aprenderá a integrar Workware con Azure Active Directory (Azure AD). Al integrar Workware con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Workware.
* Permitir que los usuarios inicien sesión automáticamente en Workware con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Workware.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Workware admite el inicio de sesión único iniciado por **IDP**.

## <a name="adding-workware-from-the-gallery"></a>Incorporación de Workware desde la galería

Para configurar la integración de Workware en Azure AD, deberá agregar Workware desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Workware** en el cuadro de búsqueda.
1. Seleccione **Workware** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Configuración y prueba del inicio de sesión único de Azure AD para Workware

Configure y pruebe el inicio de sesión único de Azure AD con Workware mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Workware.

Para configurar y probar el inicio de sesión único de Azure AD con Workware, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Workware](#configure-workware-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba en Workware](#create-workware-test-user)** : para tener un homólogo de B.Simon en Workware vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Workware**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configurar el inicio de sesión único con SAML**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `<WORKWARE_URL>/WW/AuthServices`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Workware](mailto:support@activeops.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Workware** (Configurar Workware), copie las direcciones URL que necesite.

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

En esta sección va a conceder a B.Simon acceso a Workware mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Workware**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-workware-sso"></a>Configuración del inicio de sesión único en Workware

Para usar la característica de inicio de sesión único en Workware, es necesario completar la siguiente configuración:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Habilitación de los permisos de inicio de sesión único para los administradores del sistema de Workware

* Para permitir que los administradores del sistema de Workware configuren la autenticación de inicio de sesión único, se debe habilitar el permiso de autenticación de inicio de sesión único (en la pantalla de **administración > categoría de permisos de configuración del sistema > permisos para el rol**) para los administradores del sistema de Workware.

    ![Permiso de autenticación de inicio de sesión único](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Configuración de la autenticación de inicio de sesión único en Workware

1. Vaya a la página **Configuración del sistema** y haga clic en **SSO Authentication** (Autenticación de SSO).

1. En la sección **SSO Authentication** (Autenticación de SSO), haga clic en el botón **Add SSO Authentication** (Agregar autenticación de SSO) y realice los pasos siguientes: 

    ![Autenticación de SSO](./media/workware-tutorial/authentication.png)

    1. En **External Identity Provider** (Proveedor de identidades externo), proporcione el nombre del proveedor de identidades.
    1. Seleccione **SAML2.0** como **Tipo de autenticación**.
    1. En el cuadro de texto **Identity Provider SignIn URL** (Dirección URL de inicio de sesión del proveedor de identidades), escriba el valor de **Dirección URL de inicio de sesión**  que ha copiado de Azure Portal.
    1. En el cuadro de texto **Identity Provider Issuer URL** (Dirección URL del emisor de proveedor de identidades), escriba el valor del **identificador Azure AD** que ha copiado de Azure Portal.
    1. En el cuadro de texto **Identity Provider Logout URL** (Dirección URL de cierre de sesión único del proveedor de identidades), escriba el valor de **Dirección URL de cierre de sesión**  que ha copiado de Azure Portal.
    1. Haga clic en **Enable** (Habilitar).
    1. Cargue el **certificado** descargado de Azure Portal en **Identity Provider Certificate** (Certificado del proveedor de identidades).
    1. Haga clic en **Guardar**


### <a name="create-workware-test-user"></a>Creación de un usuario de prueba en Workware

1. Inicie sesión en el sitio web de Workware como administrador.

1. Seleccione **Admin > Create / View > User Accounts > Add New** (Administrador > Crear / Ver > Cuentas de usuario > Agregar nueva).

1. En la página siguiente, realice estos pasos.

    ![Test user](./media/workware-tutorial/create-user.png)

    a. Escriba un nombre válido en el campo **Name** (Nombre).

    b. Seleccione **SSO** (Inicio de sesión único) como **Authentication Type** (Tipo de autenticación).

    c. Escriba los campos obligatorios y haga clic en **Save** (Guardar).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Workware para la que ha configurado el inicio de sesión único.

* Puede usar el Panel de acceso de Microsoft. Al hacer clic en el icono de Workware en el panel de acceso, debería iniciar sesión automáticamente en la versión de Workware para la que ha configurado el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Workware, puede aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).