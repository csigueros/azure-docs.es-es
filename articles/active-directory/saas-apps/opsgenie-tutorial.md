---
title: 'Tutorial: Integración de Azure Active Directory con OpsGenie | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y OpsGenie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: e35ed7aaabbb81c274c4d92ad7e83dcd9f3bac77
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124738159"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con OpsGenie

En este tutorial aprenderá a integrar OpsGenie con Azure Active Directory (Azure AD). Al integrar OpsGenie con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a OpsGenie.
* Permitir que los usuarios inicien sesión automáticamente en OpsGenie con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único (SSO) en OpsGenie.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* OpsGenie admite el inicio de sesión único iniciado por **IDP**.

## <a name="adding-opsgenie-from-the-gallery"></a>Adición de OpsGenie desde la galería

Para configurar la integración de OpsGenie en Azure AD, deberá agregar OpsGenie desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **OpsGenie** en el cuadro de búsqueda.
1. Seleccione **OpsGenie** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>Configuración y prueba del inicio de sesión único de Azure AD para OpsGenie

Configure y pruebe el inicio de sesión único de Azure AD con OpsGenie mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de OpsGenie.

Para configurar y probar el inicio de sesión único de Azure AD con OpsGenie, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de OpsGenie](#configure-opsgenie-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de OpsGenie](#create-opsgenie-test-user)** , para tener un homólogo de B.Simon en OpsGenie vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **OpsGenie**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de respuesta reales, que se explican más adelante en el tutorial.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

1. En la sección **Configurar OpsGenie**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a OpsGenie mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **OpsGenie**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-opsgenie-sso"></a>Configuración del inicio de sesión único de OpsGenie

1. Para automatizar la configuración en OpsGenie, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la aplicación**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar OpsGenie** para ir a la aplicación. Desde ahí, especifique las credenciales de administrador para iniciar sesión en OpsGenie. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 7.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar OpsGenie manualmente, abra otra ventana del explorador web e inicie sesión como administrador en el sitio web de OpsGenie.

2. Haga clic en **Configuración** y después en la pestaña **Inicio de sesión único**.
   
    ![Inicio de sesión único de OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Para habilitar SSO, seleccione **Habilitado**.
   
    ![Captura de pantalla que muestra la casilla "Habilitado" seleccionada.](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. En la sección **Proveedor**, haga clic en la pestaña **Azure Active Directory**.
   
    ![Captura de pantalla que muestra la sección "Proveedor" con la pestaña "Azure Active Directory" seleccionada.](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. En la página de diálogo de Azure Active Directory, realice los siguientes pasos:
   
    ![Captura de pantalla que muestra la sección "Inicio de sesión único" con el botón de alternancia "Habilitar el inicio de sesión único", "Punto de conexión de S A M L 2.0" y "U R L de metadatos".](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Copie el valor de **App ID URI** (URI de id. de aplicación) y péguelo en el cuadro de texto **Identificador (id. de entidad)** de la sección **Configuración básica de SAML** en Azure Portal.

    a. Copie el valor de **Reply URL** (URL de respuesta) y péguelo en el cuadro de texto **URL de respuesta** de la sección **Configuración básica de SAML** en Azure Portal.

    a. En el cuadro de texto **SAML 2.0 Endpoint** (Punto de conexión SAML 2.0), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.
    
    b. En el cuadro de texto **URL de metadatos**, pegue el valor de **Dirección URL de metadatos de federación de la aplicación** que copió en Azure Portal.
    
    c. Para habilitar el inicio de sesión único, active la alternancia **Enable single sign-on** (Habilitar el inicio de sesión único).

    d. Haga clic en **Apply SSO settings** (Aplicar la configuración de SSO).

### <a name="create-opsgenie-test-user"></a>Creación de un usuario de prueba de OpsGenie

El objetivo de esta sección es crear un usuario de prueba llamado B.Simon en OpsGenie. 

1. En una ventana del explorador web, inicie sesión en el inquilino de OpsGenie como administrador.

2. Vaya a la lista Usuarios haciendo clic en **Usuarios** en el panel izquierdo.
   
    ![Configuración de OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Haga clic en **Agregar usuario**.

4. En el cuadro de diálogo **Agregar usuario** , realice los pasos siguientes:
   
    ![Captura de pantalla que muestra el cuadro de diálogo "Add User" (Agregar usuario) con los cuadros de texto "Email" (Correo electrónico) y "Full name" (Nombre completo) resaltados y el botón "Save" (Guardar) seleccionado.](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico de B.Simon en Azure Active Directory.
   
    b. En el cuadro de texto **Full name** (Nombre completo), escriba **B.Simon**.
   
    c. Haga clic en **Save**(Guardar). 

> [!NOTE]
> B.Simon recibirá un correo electrónico con instrucciones para configurar su perfil.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de OpsGenie para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de OpsGenie en Mis aplicaciones, se debería iniciar sesión automáticamente en la instancia de OpsGenie para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

* Una vez configurado OpsGenie, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).