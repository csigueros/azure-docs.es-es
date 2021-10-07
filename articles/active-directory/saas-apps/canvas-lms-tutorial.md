---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Canvas'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2021
ms.author: jeedes
ms.openlocfilehash: b91f6c186be774110b52fab75b742dcb73925f03
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622249"
---
# <a name="tutorial-azure-ad-sso-integration-with-canvas"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Canvas

En este tutorial, aprenderá a integrar Canvas con Azure Active Directory (Azure AD). Al integrar Canvas con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Canvas.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Canvas con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:
 
* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Canvas.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Canvas admite inicio de sesión único iniciado por **SP**.

## <a name="add-canvas-from-the-gallery"></a>Adición de Canvas desde la galería

Para configurar la integración de Canvas en Azure AD, deberá agregar Canvas desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Canvas** en el cuadro de búsqueda.
1. Seleccione **Canvas** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Configuración y prueba del inicio de sesión único de Azure AD para Canvas

Configure y pruebe el inicio de sesión único de Azure AD con Canvas mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Canvas.

Para configurar y probar el inicio de sesión único de Azure AD con Canvas, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Canvas](#configure-canvas-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Canvas](#create-canvas-test-user)** , para tener un homólogo de B.Simon en Canvas que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación de **Canvas**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.instructure.com`

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte de cliente de Canvas](https://community.canvaslms.com/community/help) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la sección **Certificado de firma de SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Certificado de firma de SAML**.

    ![Edición del certificado de firma de SAML](common/edit-certificate.png)

6. En la sección **Certificado de firma de SAML**, copie el valor de **Huella digital** y guárdelo en el equipo.

    ![Copia del valor de la huella digital](common/copy-thumbprint.png)

7. En la sección **Set up Canvas** (Configurar Canvas), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Canvas mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Canvas**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-canvas-sso"></a>Configuración del inicio de sesión único en Canvas

1. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Canvas.

2. Vaya a **Cursos \> Cuentas administradas \> Microsoft**.

    ![Lienzo](./media/canvas-lms-tutorial/course.png "Lienzo")

3. En el panel de navegación izquierdo, seleccione **Authentication** (Autenticación) y, después, haga clic en **Add New SAML Config** (Agregar nueva configuración de SAML).

    ![Autenticación](./media/canvas-lms-tutorial/tools.png "Authentication")

4. En la página Curent Integration (integración actual) realice los siguientes pasos:

    ![Current Integration (Integración actual)](./media/canvas-lms-tutorial/save.png "Integración actual")

    a. En el cuadro de texto **IdP Entity ID** (Identificador de la entidad de IdP), pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    b. En el cuadro de texto **Log On URL** (URL de inicio de sesión), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c. En el cuadro de texto **Log Out URL** (URL de cierre de sesión), pegue el valor de **Dirección URL de cierre de sesión** que copió de Azure Portal.

    d. En el cuadro de texto **Change Password Link** (Cambiar vínculo de contraseña), pegue el valor de **Cambiar dirección URL de contraseña** que copió de Azure Portal.

    e. En el cuadro de texto **Certificate Fingerprint** (Huella digital de certificado), pegue el valor de **Huella digital** del certificado que haya copiado de Azure Portal.

    f. En la lista **Atributo de inicio de sesión**, seleccione **NameID**.

    g. En la lista **Formato de identificador**, seleccione **emailAddress**.

    h. Haga clic en **Guardar configuración de autenticación**.

### <a name="create-canvas-test-user"></a>Creación de un usuario de prueba de Canvas

Para permitir que los usuarios de Azure AD inicien sesión en Canvas, tienen que aprovisionarse en Canvas. En el caso de Canvas, el aprovisionamiento de usuarios es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Canvas** .

2. Vaya a **Cursos \> Cuentas administradas \> Microsoft**.

   ![Lienzo](./media/canvas-lms-tutorial/course.png "Lienzo")

3. Haga clic en **Usuarios**.

   ![Captura de pantalla que muestra el menú Lienzo con la opción Usuarios seleccionada.](./media/canvas-lms-tutorial/user.png "Usuarios")

4. Haga clic en **Add New User**(Agregar nuevo usuario).

   ![Captura de pantalla que muestra el botón Add a new User (Agregar nuevo usuario).](./media/canvas-lms-tutorial/add-user.png "Usuarios")

5. En la página de diálogo Agregar nuevo usuario, realice los pasos siguientes:

   ![Agregar usuario](./media/canvas-lms-tutorial/name.png "Agregar usuario")

   a. En el cuadro de texto **Nombre completo**, escriba el nombre de usuario, por ejemplo, **BrittaSimon**.

   b. En el cuadro de texto **Email** (Correo electrónico), escriba el correo electrónico del usuario; por ejemplo, **brittasimon\@contoso.com**.

   c. En el cuadro de texto **Login** (Inicio de sesión), escriba la dirección de correo electrónico de Azure AD del usuario **brittasimon\@contoso.com**.

   d. Seleccione **Email the user about this account creation**(Enviar correo electrónico al usuario sobre la creación de esta cuenta).

   e. Haga clic en **Agregar usuario**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Canvas ofrecida por Canvas para aprovisionar cuentas de usuario de Azure AD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Canvas, desde donde podrá comenzar el flujo de inicio de sesión. 

* Acceda directamente a la URL de inicio de sesión de Canvas y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Canvas en Aplicaciones, debería iniciar sesión automáticamente en la instancia de Canvas para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Canvas, puede aplicar el control de sesión, que protege frente a la filtración e infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).