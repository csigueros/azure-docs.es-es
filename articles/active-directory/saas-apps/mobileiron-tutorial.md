---
title: 'Tutorial: Integración de Azure Active Directory con MobileIron | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: b44e6eae96ac2a0d9a3ce4bb72043b5e24dfe9db
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780364"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Tutorial: integración de Azure Active Directory con MobileIron

 En este tutorial, aprenderá a integrar MobileIron con Azure Active Directory (Azure AD). Al integrar MobileIron con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a MobileIron.
* Permitir que los usuarios inicien sesión automáticamente en MobileIron con sus cuentas de Azure AD.
* Administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en MobileIron.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* MobileIron admite SSO iniciado por **SP e IDP**.

## <a name="add-mobileiron-from-the-gallery"></a>Incorporación de MobileIron desde la galería

Para configurar la integración de MobileIron en Azure AD, es preciso agregar MobileIron desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel izquierdo, seleccione **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **MobileIron** en el cuadro de búsqueda.
1. Seleccione **MobileIron** en los resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Configuración y prueba del inicio de sesión único de Azure AD para MobileIron

Configure y pruebe el inicio de sesión único de Azure AD con MobileIron mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de MobileIron.

Para configurar y probar el inicio de sesión único de Azure AD con MobileIron, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
     1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de MobileIron](#configure-mobileiron-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de MobileIron](#create-mobileiron-test-user)** : para tener un homólogo de Britta Simon en MobileIron que esté vinculado a la representación del usuario en Azure AD.
6. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

En esta sección va a habilitar el inicio de sesión único de Azure AD en Azure Portal.
 
1. En Azure Portal, en la página de integración de la aplicación **Mobile Locker**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, seleccione el icono con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración de SAML básica**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por **IDP**:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://www.MobileIron.com/<key>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

     En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Obtendrá los valores de clave y host desde el portal de administración de MobileIron, como se explica más adelante en el tutorial.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD 

En esta sección va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory** > **Usuarios** > **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Seleccione la casilla **Mostrar contraseña** y, a continuación, anote la contraseña.
   1. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a MobileIron mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **MobileIron**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista de usuarios. A continuación, elija **Seleccionar** en la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

## <a name="configure-mobileiron-sso"></a>Configuración del inicio de sesión único de MobileIron

1. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la empresa de MobileIron.

2. Vaya a **Admin** > **Identity** (Administración e identidad) y seleccione la opción **AAD** en el campo **Info on Cloud IDP Setup** (Información de configuración del IDP en la nube).

    ![Captura de pantalla que muestra la pestaña Admin del sitio MobileIron con la opción Identity (Identidad) seleccionada.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Copie los valores de **Key** (Clave) y **Host**, y péguelos para completar las direcciones URL en la sección **Configuración de SAML básica** en Azure Portal.

    ![Captura de pantalla que muestra la opción Setting Up SAML (Configuración de SAML) con una clave y un valor de host.](./media/MobileIron-tutorial/key.png)

4. En el campo **Export metadata file from AAD and import to MobileIron Cloud** (Exportar archivo de metadatos de AAD e importar en MobileIron Cloud) haga clic en **Choose File** (Elegir archivo) para cargar los metadatos descargados desde Azure Portal. Haga clic en **Done** (Listo) una vez cargado.

    ![Botón de metadatos de administración de inicio de sesión único](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>Creación de un usuario de prueba de MobileIron

Para permitir que los usuarios de Azure AD inicien sesión en MobileIron, es necesario que se aprovisionen en MobileIron.  
En el caso de MobileIron, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la compañía de MobileIron como administrador.

1. Vaya a **Users** (Usuarios) y haga clic en **Add** (Agregar)  > **Single User** (Usuario único).

    ![Botón Configurar usuario de inicio de sesión único](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. En la página de diálogo **“Single User”** (Usuario único), realice los siguientes pasos:

    ![Botón Configurar agregar usuario de inicio de sesión único](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. En el cuadro de texto **E-mail Address** (Dirección de correo electrónico), escriba el correo electrónico del usuario, en este caso, brittasimon@contoso.com.

    b. En el cuadro de texto **First Name** (Nombre), escriba el nombre de usuario, en este caso Britta.

    c. En el cuadro de texto **Last Name** (Apellidos), escriba el nombre de usuario, en este caso Simon.

    d. Haga clic en **Done**(Listo).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de MobileIron, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya directamente a la URL de inicio de sesión de MobileIron y ponga en marcha el flujo de inicio de sesión desde allí.

### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de MobileIron para la que configuró el inicio de sesión único.

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de MobileIron en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de MobileIron para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que se ha configurado MobileIron, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. Los controles de sesión proceden del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).