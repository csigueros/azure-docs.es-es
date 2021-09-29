---
title: 'Tutorial: Integración de Azure Active Directory con Adaptive Insights | Microsoft Docs'
description: Obtenga más información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Adaptive Insights.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 5444d1eb503fd238f5acd5c1e19da68d58782cad
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124770536"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutorial: Integración de Adaptive Insights con Azure Active Directory

En este tutorial obtendrá más información sobre cómo integrar Adaptive Insights con Azure Active Directory (Azure AD). Al integrar Adaptive Insights con Azure AD, puede:

* Controlar quién tiene acceso a Adaptive Insights en Azure AD.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Adaptive Insights con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción de Adaptive Insights con el inicio de sesión único (SSO) habilitado.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Adaptive Insights admite el inicio de sesión único iniciado por **IDP**.

## <a name="add-adaptive-insights-from-the-gallery"></a>Incorporación de Adaptive Insights desde la galería

Para configurar la integración de Adaptive Insights con Azure AD, deberá agregar Adaptive Insights desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Adaptive Insights** en el cuadro de búsqueda.
1. Seleccione **Adaptive Insights** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Configuración y prueba del inicio de sesión único de Azure AD para Adaptive Insights

Configure y pruebe el inicio de sesión único de Azure AD con Adaptive Insights mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Adaptive Insights.

Para configurar y probar el inicio de sesión único de Azure AD con Adaptive Insights, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Adaptive Insights](#configure-adaptive-insights-sso)** , para configurar los valores del inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Adaptive Insights](#create-adaptive-insights-test-user)** : para tener un homólogo de B.Simon en Adaptive Insights vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Adaptive Insights**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Puede obtener el identificador (id. de entidad) y los valores de URL en la página **Configuración de SSO de SAML** de Adaptive Insights.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Configurar Adaptive Insights**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, concederá acceso a Adaptive Insights a B.Simon para permitirle usar el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Adaptive Insights**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="configure-adaptive-insights-sso"></a>Configuración del inicio de sesión único de Adaptive Insights

1. En otra ventana del explorador web, inicie sesión en el sitio de la empresa de Adaptive Insights como administrador.

2. Vaya a **Administración**.

    ![Captura de pantalla donde se resalta Administración en el panel de navegación.](./media/adaptivesuite-tutorial/administration.png "Administración")

3. En la sección **Users and Roles** (Usuarios y roles), haga clic en **SAML SSO Settings** (Configuración de SSO de SAML).

    ![Administración de la configuración de inicio de sesión único de SAML](./media/adaptivesuite-tutorial/settings.png "Manage SAML SSO Settings")

4. En la página **SAML SSO Settings** (Configuración de SSO de SAML), realice los pasos siguientes:

    ![SAML SSO Settings](./media/adaptivesuite-tutorial/saml.png "SAML SSO Settings") (Configuración de SSO de SAML)

    a. En el cuadro de texto **Identity provider name** (Nombre del proveedor de identidades), escriba el nombre de la configuración.

    b. Pegue el valor de **Azure Ad Identifier** (Identificador de Azure AD) que ha copiado de Azure Portal en el cuadro de texto **Identity Provider Entity ID** (Id. de entidad del proveedor de identidades).

    c. Pegue el valor de **Dirección URL de inicio de sesión** que copió de Azure Portal en el cuadro de texto **Identity provider SSO URL** (URL de SSO de proveedor de identidades).

    d. Pegue el valor de **URL de cierre de sesión** que copió de Azure Portal en el cuadro de texto **Custom logout URL** (Dirección URL de cierre de sesión personalizada).

    e. Para cargar el certificado descargado, haga clic en **Choose file**(Elegir archivo).

    f. Seleccione las opciones siguientes en cada caso:

     * En **SAML user id** (identificador de usuario de SAML), seleccione **User’s Adaptive Insights user name** (Nombre de usuario de Adaptive Insights del usuario).

     * En **SAML user id location** (Ubicación del id. de usuario de SAML), seleccione **User id in NameID of Subject** (identificador de usuario en NameID del tema).

     * En **SAML NameID format** (Formato de NameID de SAML), seleccione **Email address** (Dirección de correo electrónico).

     * En **Enable SAML** (Habilitar SAML), seleccione **Allow SAML SSO and direct Adaptive Insights login** (Permitir inicio de sesión único de SAML e inicio de sesión directo de Adaptive Insights).

    g. Copie la **dirección URL de inicio de sesión único de Adaptive Insights** y péguela en los cuadros de texto **Identificador (id. de entidad)** y **URL de respuesta** en la sección **Configuración básica de SAML** de Azure Portal.

    h. Haga clic en **Save**(Guardar).

### <a name="create-adaptive-insights-test-user"></a>Creación de un usuario de prueba de Adaptive Insights

Para permitir que los usuarios de Azure AD inicien sesión en Adaptive Insights, deben aprovisionarse en Adaptive Insights. En el caso de Adaptive Insights, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en el sitio de la compañía de **Adaptive Insights** como administrador.

2. Vaya a **Administración**.

   ![Administrador](./media/adaptivesuite-tutorial/administration.png "Administración")

3. En la sección **Users and Roles** (Usuarios y roles), haga clic en **Users** (Usuarios).

   ![Agregar usuario](./media/adaptivesuite-tutorial/users.png "Agregar usuario")

4. En la sección **Nuevo usuario** , lleve a cabo estos pasos:

   Haga clic en ![Enviar](./media/adaptivesuite-tutorial/new.png "Enviar").

   a. Escriba el **nombre**, el **nombre de usuario**, la **dirección de correo electrónico** y la **contraseña** o de un usuario válido de Azure Active Directory que quiera aprovisionar en los cuadros de texto relacionados.

   b. Seleccione un **Role**(rol).

   c. Haga clic en **Enviar**.

> [!NOTE]
> Para aprovisionar cuentas de usuario de Azure AD, puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Adaptive Insights ofrecida por Adaptive Insights.

### <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Adaptive Insights para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Adaptive Insights en Aplicaciones, debería iniciar sesión automáticamente en la versión de Adaptive Insights para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Adaptive Insights, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).