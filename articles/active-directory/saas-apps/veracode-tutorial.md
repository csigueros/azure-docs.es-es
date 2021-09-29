---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Veracode | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 38f822ad8c88f38a1e6b5b5dce4d3d51a629eee7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124825351"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Veracode

En este tutorial aprenderá a integrar Veracode con Azure Active Directory (Azure AD). Al integrar Veracode con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Veracode.
* Permitir que los usuarios inicien sesión automáticamente en Veracode con sus cuentas de Azure AD.
* Administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Veracode.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. Veracode admite el SSO iniciado por el proveedor de identidades y el aprovisionamiento de usuarios Just-in-Time.

## <a name="add-veracode-from-the-gallery"></a>Agregación de Veracode desde la galería

Para configurar la integración de Veracode en Azure AD, deberá agregar Veracode desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba "Veracode" en el cuadro de búsqueda.
1. Seleccione **Veracode** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Configuración y prueba del inicio de sesión único de Azure AD para Veracode

Configure y pruebe el inicio de sesión único de Azure AD con Veracode mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Veracode.

Para configurar y probar el inicio de sesión único de Azure AD con Veracode, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Veracode](#configure-veracode-sso)**, para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba de Veracode](#create-veracode-test-user)**, para tener un homólogo de B.Simon en Veracode que esté vinculado a la representación del usuario en Azure AD.
1. **[Comprobación del inicio de sesión único](#test-sso)** , para verificar que la configuración funciona correctamente.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Veracode**, busque la sección **Administrar**. Seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, seleccione el icono con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada y las direcciones URL necesarias ya se han rellenado previamente con Azure. Seleccione **Guardar**.

1. En la página **Configuración del inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** . Seleccione **Descargar** para descargar el certificado y guárdelo en el equipo.

    ![Captura de pantalla de la sección Certificado de firma de SAML, con el vínculo Descargar resaltado](common/certificatebase64.png)

1. Veracode espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![Captura de pantalla de la sección Atributos y reclamaciones del usuario](common/default-attributes.png)

1. Veracode también espera que se devuelvan algunos atributos más en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre | Atributo de origen|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |

1. En la sección **Configurar Veracode**, copie las direcciones URL adecuadas según sus necesidades.

    ![Captura de pantalla de la sección de configuración de Veracode, con direcciones URL de configuración resaltadas](common/copy-configuration-urls.png)

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

En esta sección, va a permitir que B.Simon acceda a Veracode mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Veracode**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-veracode-sso"></a>Configuración del inicio de sesión único de Veracode

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Veracode como administrador.

1. En el menú de la parte superior, seleccione **Settings** > **Admin** (Configuración > Administración).
   
    ![Captura de pantalla de administración de Veracode, con el icono de configuración y la opción de administración resaltados](./media/veracode-tutorial/admin.png "Administración")

1. Seleccione la pestaña **SAML**.

1. En la sección **Configuración de SAML de organización** siga estos pasos:

    ![Captura de pantalla de la sección Organization SAML Settings (Configuración de SAML de organización)](./media/veracode-tutorial/saml.png "Administración")

    a.  En **Issuer** (Emisor), pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    b. En **Assertion Signing Certificate** (Certificado de firma de aserción), seleccione **Choose File** (Elegir archivo) para cargar el certificado descargado de Azure Portal.

    c. En **Self Registration** (Registro automático), seleccione **Enable Self Registration** (Habilitar registro automático).

1. En la sección **Self Registration Settings** (Configuración de registro automático), lleve a cabo estos pasos y, después, seleccione **Guardar**:

    ![Captura de pantalla de la sección Self Registration Settings (Configuración de registro automático), con varias opciones resaltadas](./media/veracode-tutorial/save.png "Administración")

    a. En **New User Activation** (Activación de nuevo usuario), seleccione **No Activation Required** (No se requiere ninguna activación).

    b. En **User Data Updates** (Actualizaciones de datos de usuario), seleccione **Preference Veracode User Data** (Datos de usuario de Veracode de preferencia).

    c. Para **Detalles de atributo de SAML**, seleccione lo siguiente:
      * **Roles de usuario**
      * **Administrador de directivas**
      * **Revisor**
      * **Principal de seguridad**
      * **Ejecutivo**
      * **Remitente**
      * **Creador**
      * **Todos los tipos de detección**
      * **Pertenencias a equipos**
      * **Equipo predeterminado**

### <a name="create-veracode-test-user"></a>Creación de un usuario de prueba de Veracode

En esta sección, se crea un usuario llamado B.Simon en Veracode. Veracode admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Veracode, se crea uno después de la autenticación.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Veracode ofrecida por Veracode para aprovisionar cuentas de usuario de Azure AD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Veracode para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Veracode en Aplicaciones, debería iniciar sesión automáticamente en la versión de Veracode para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que configure Veracode, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).