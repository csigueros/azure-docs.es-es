---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con OpenAthens | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y OpenAthens.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: jeedes
ms.openlocfilehash: 52ed1d50aa1567b36c81706adcd3ea474e853a1e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124738183"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con OpenAthens

En este tutorial aprenderá a integrar OpenAthens con Azure Active Directory (Azure AD). Al integrar OpenAthens con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a OpenAthens.
* Permitir que los usuarios inicien sesión automáticamente en OpenAthens con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en OpenAthens.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* OpenAthens admite SSO iniciado por **IDP**
* OpenAthens admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-openathens-from-the-gallery"></a>Incorporación de OpenAthens desde la galería

Para configurar la integración de OpenAthens en Azure AD, será preciso que agregue OpenAthens desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **OpenAthens** en el cuadro de búsqueda.
1. Seleccione **OpenAthens** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-openathens"></a>Configuración y prueba del inicio de sesión único de Azure AD para OpenAthens

Configure y pruebe el inicio de sesión único de Azure AD con OpenAthens mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de OpenAthens.

Para configurar y probar el inicio de sesión único de Azure AD con OpenAthens, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en OpenAthens](#configure-openathens-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba en OpenAthens](#create-openathens-test-user)** , para tener un homólogo de B.Simon en OpenAthens que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **OpenAthens**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, cargue el **archivo de metadatos del proveedor de servicios** (los pasos para ello se mencionan más adelante en este tutorial).

    a. Haga clic en **Cargar el archivo de metadatos**.

    ![openathens cargar metadatos](common/upload-metadata.png)

    b. Haga clic en el **logotipo de la carpeta** para seleccionar el archivo de metadatos y luego en **Cargar**.

    ![Openathens explora metadatos de carga](common/browse-upload-metadata.png)

    c. Una vez que se haya cargado correctamente el archivo de metadatos, el valor de **Identificador** se rellena automáticamente en el cuadro de texto de la sección **Configuración básica de SAML**:

    ![Información de dominio y direcciones URL de inicio de sesión único de OpenAthens](common/idp-identifier.png)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar OpenAthens**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, habilitará a B.Simon para que use el inicio de sesión único de Azure concediéndole acceso a OpenAthens.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **OpenAthens**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-openathens-sso"></a>Configuración del inicio de sesión único de OpenAthens

1. En otra ventana del explorador web, inicie sesión en el sitio de la empresa de OpenAthens como administrador.

1. Seleccione **Connections** (Conexiones) de la lista de la pestaña **Management** (Administración).

    ![Captura de pantalla que muestra la página del sitio de la compañía "OpenAthens" con "Connections" (Conexiones) seleccionadas en la pestaña "Management" (Administración).](./media/openathens-tutorial/connections.png)

1. Seleccione **SAML 1.1/2.0** y, a continuación, seleccione el botón **Configure** (Configurar).

    ![Captura de pantalla que muestra el cuadro de diálogo "Select local authentication system type" (Seleccionar tipo de sistema de autenticación local) con "S A M L 1.1/2.0" y el botón "Configure" (Configurar) seleccionados.](./media/openathens-tutorial/saml.png)

1. Para agregar la configuración, seleccione el botón **Browse** (Examinar) para cargar el archivo .xml de metadatos que descargó desde Azure Portal y, a continuación, seleccione **Add** (Agregar).

    ![Captura de pantalla que muestra el cuadro de diálogo "Add S A M L authentication system" (Agregar sistema de autenticación de S A M L) con la acción "Browse" (Examinar) y el botón "Add" (Agregar) seleccionado.](./media/openathens-tutorial/configure.png)

1. Realice los pasos siguientes en la pestaña **Details** (Detalles).

    ![Configurar inicio de sesión único](./media/openathens-tutorial/add.png)

    a. En **Display name mapping** (Asignación de nombre para mostrar), seleccione **Use attribute** (Usar atributo).

    b. En el cuadro de texto **Display name attribute** (Atributo de nombre para mostrar), escriba el valor `http://schemas.microsoft.com/identity/claims/displayname`.

    c. En **Unique user mapping** (Asignación de usuario único), seleccione **Use attribute** (Usar atributo).

    d. En el cuadro de texto **Unique user attribute** (Atributo de usuario único), introduzca el valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. En **Status** (Estado), seleccione las tres casillas de verificación.

    f. En **Create local accounts** (Crear cuentas locales), seleccione **automatically** (automáticamente).

    g. Seleccione **Save changes** (Guardar los cambios).

    h. Desde la pestaña **Usuario de confianza**, copie la **Dirección URL de metadatos** y ábrala en el explorador para descargar el archivo **XML de metadatos de SP**. Cargue este archivo de metadatos de SP en la sección **Configuración básica de SAML** de Azure AD.

    ![Captura de pantalla que muestra la pestaña "Usuario de confianza" seleccionada y la opción "U R L de metadatos" resaltada.](./media/openathens-tutorial/metadata.png)

### <a name="create-openathens-test-user"></a>Creación de un usuario de prueba de OpenAthens

En esta sección, se crea un usuario llamado a Britta Simon en OpenAthens. OpenAthens admite el **aprovisionamiento de usuarios Just-In-Time**, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario deja de existir en OpenAthens, se crea uno nuevo después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de OpenAthens para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de OpenAthens en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de OpenAthens para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado OpenAthens, puede aplicar el control de sesión, que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).