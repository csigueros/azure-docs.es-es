---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Claromentis | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Claromentis.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/11/2021
ms.author: jeedes
ms.openlocfilehash: 87afbae80f08716a1263aadeb52a8dedc71d4a90
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753950"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Claromentis

En este tutorial, aprenderá a integrar Claromentis con Azure Active Directory (Azure AD). Cuando integre Claromentis con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Claromentis.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Claromentis con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Claromentis.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Claromentis admite el inicio de sesión único iniciado por **SP e IDP**.
* Claromentis admite el aprovisionamiento de usuarios **Just-In-Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-claromentis-from-the-gallery"></a>Adición de Claromentis desde la galería

Para configurar la integración de Claromentis en Azure AD, deberá agregar Claromentis desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Claromentis** en el cuadro de búsqueda.
1. Seleccione **Claromentis** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-claromentis"></a>Configuración y prueba del inicio de sesión único de Azure AD para Claromentis

Configure y pruebe el inicio de sesión único de Azure AD con Claromentis mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Claromentis.

Para configurar y probar el SSO de Azure AD con Claromentis, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Claromentis](#configure-claromentis-sso)** , para configurar los valores del inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Claromentis](#create-claromentis-test-user)** , para tener un homólogo de B.Simon en Claromentis que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Claromentis**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba el valor del identificador según los requisitos de su organización.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<CUSTOMER_SITE_URL>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

    | URL de inicio de sesión |
    | ---- |
    | `https://<CUSTOMER_SITE_URL>/login` |
    | `https://<CUSTOMER_SITE_URL>/login?no_auto=0` |
    |
    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL de inicio de sesión y la dirección URL de respuesta reales, como se explica más adelante en el tutorial.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar Claromentis**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Claromentis mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Claromentis**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-claromentis-sso"></a>Configuración del inicio de sesión único de Claromentis

1. En otra ventana del explorador, inicie sesión en el sitio web de Claromentis como administrador.

1. Haga clic en el **icono de aplicación** y seleccione **Administrador**.

    ![Captura de pantalla que muestra el sitio web de Claromentis con la opción Admin (Administrador) seleccionada.](./media/claromentis-tutorial/admin.png)

1. Seleccione la pestaña **Custom Login Handler** (Controlador del inicio de sesión personalizado).

    ![Captura de pantalla que muestra la página Administration (Administración) con la opción Custom Login Handler (Controlador de inicio de sesión personalizado) seleccionada.](./media/claromentis-tutorial/custom-login.png)

1. Seleccione **SAML Config** (Configuración de SAML).

    ![Captura de pantalla que muestra la página de configuración de SAML.](./media/claromentis-tutorial/configure.png)

1. En la pestaña **SAML Config** (Configuración de SAML), desplácese hacia abajo a la sección **Config** (Configuración) y realice los pasos siguientes:

    ![Captura de pantalla que muestra la sección Config (Configuración) de la página, donde puede especificar la información que se describe en este paso.](./media/claromentis-tutorial/information.png)

    a. En el cuadro de texto **Nombre del contacto técnico**, escriba el nombre de la persona de contacto del soporte técnico.

    b. En el cuadro de texto **Correo del contacto técnico**, escriba la dirección de correo electrónico de la persona de contacto del soporte técnico.

    c. Proporcione la contraseña en el cuadro de texto **Auth Admin Password** (Autenticar contraseña del administrador).

1. Desplácese hacia abajo hasta la opción **Auth Sources** (Orígenes de autenticación) y realice los pasos siguientes:

    ![Captura de pantalla que muestra la sección Auth Sources (Orígenes de autenticación), donde puede especificar la información que se describe en este paso.](./media/claromentis-tutorial/sources.png)

    a. En el cuadro de texto **IDP**, pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    b. En el cuadro de texto **Entity ID** (Id. de entidad), escriba el identificador de entidad.

    c. Cargue el archivo **XML de metadatos de federación** que descargó de Azure Portal.

    d. Haga clic en **Save**(Guardar).

1. Ahora observará que todas las direcciones URL se han rellenado en la sección **Proveedor de identidades** de **SAML Config** (Configuración de SAML).

    ![Captura de pantalla que muestra la página Proveedor de identidades rellenada con las direcciones U R L.](./media/claromentis-tutorial/configuration.png)

    a. Copie el valor de **Identificador (id. de entidad)** y péguelo en el cuadro de texto **Identificador** de la sección **Configuración básica de SAML** de Azure Portal.

    b. Copie el valor de **URL de respuesta** y péguelo en el cuadro de texto **URL de respuesta** de la sección **Configuración básica de SAML** de Azure Portal.

    c. Copie el valor de **URL de inicio de sesión** y péguelo en el cuadro de texto **URL de inicio de sesión** de la sección **Configuración básica de SAML** de Azure Portal.

### <a name="create-claromentis-test-user"></a>Creación de un usuario de prueba de Claromentis

En esta sección, se crea un usuario llamado B.Simon en Claromentis. Claromentis admite el aprovisionamiento de usuarios Just-In-Time, el cual está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario deja de existir en Claromentis, se crea otro después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Claromentis, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la URL de inicio de sesión de Claromentis e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Claromentis para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Claromentis en Mis aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de Claromentis para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Claromentis, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).