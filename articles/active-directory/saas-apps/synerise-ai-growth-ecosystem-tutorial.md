---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Synerise AI Growth Operating System | Microsoft Docs'
description: Aprenda cómo configurar el inicio de sesión único entre Azure Active Directory y Synerise AI Growth Operating System.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: jeedes
ms.openlocfilehash: f21e12f8bf342508be36e1dfe80773695aa54b37
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751691"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synerise-ai-growth-operating-system"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Synerise AI Growth Operating System

En este tutorial aprenderá a integrar Synerise con Azure Active Directory (Azure AD). Al integrar Synerise con Azure AD, puede realizar las siguientes acciones:

* Controlar en Azure AD quién tiene acceso a Synerise.
* Permitir que los usuarios inicien sesión automáticamente en Synerise con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Synerise.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Synerise admite el inicio de sesión único iniciado por **SP e IDP**.
* Synerise admite el aprovisionamiento de usuarios **Just-In-Time**

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.


## <a name="adding-synerise-ai-growth-operating-system-from-the-gallery"></a>Adición de Synerise AI Growth Operating System desde la galería

Para configurar la integración de Synerise en Azure AD, es preciso agregar Synerise desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Synerise AI Growth Operating System** en el cuadro de búsqueda.
1. Seleccione **Synerise AI Growth Operating System** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-synerise-ai-growth-operating-system"></a>Configuración y prueba del inicio de sesión único de Azure AD para Synerise AI Growth Operating System

Configure y pruebe el inicio de sesión único de Azure AD con Synerise mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Synerise.

Para configurar y probar el inicio de sesión único de Azure AD con Synerise, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Synerise AI Growth Operating System](#configure-synerise-ai-growth-operating-system-sso)** : para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Synerise AI Growth Operating System](#create-synerise-ai-growth-operating-system-test-user)** : para tener un homólogo de B. Simon en Synerise vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Synerise AI Growth Operating System**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Synerise](mailto:support@synerise.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Synerise**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección va a permitir que B.Simon acceda a Synerise mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Synerise AI Growth Operating System**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-synerise-ai-growth-operating-system-sso"></a>Configuración del inicio de sesión único de Synerise AI Growth Operating System

1. Inicie sesión en Synerise como administrador.

1. Vaya a **Configuración > Control de acceso**.

    ![Configuración de Synerise](./media/synerise-ai-growth-ecosystem-tutorial/settings.png)

1. En la página **Control de acceso**, haga clic en el botón **Mostrar** de la pestaña **Inicio de sesión único**.

    ![Control de acceso de Synerise](./media/synerise-ai-growth-ecosystem-tutorial/single-sign-on.png)

1. Realice en la página siguiente los pasos que se indican a continuación.

    ![Configuración de Synerise](./media/synerise-ai-growth-ecosystem-tutorial/configuration.png)

    a. En el cuadro de texto **Identity Provider Entity ID** (Id. de entidad del proveedor de identidades), pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    b. En el cuadro de texto **SSO endpoint (http)** [Punto de conexión de inicio de sesión único (http)], pegue el valor de la **URL de inicio de sesión** que ha copiado de Azure Portal.

    c. En el cuadro de texto **Identity Provider application ID** (Id. de aplicación del proveedor de identidades), pegue el valor de **Id. de la aplicación**.

    d. Copie el valor de **Service Provider redirect URI** (URI de redireccionamiento de proveedor de servicios) y péguelo en el cuadro de texto **URL de respuesta**, de la sección Configuración básica de SAML de Azure Portal.

    e. Seleccione **HTTP REDIRECT** (Redirección HTTP) en **Request binding** (Enlace de solicitud).

    f. Cambie a **Request signature** (Firma de solicitud).

    g. Cargue el archivo del **certificado (Base64)**  descargado en **Identity Provider Signature Certificate** (Certificado de firma del proveedor de identidades).

    i. Haga clic en **Aplicar**.

### <a name="create-synerise-ai-growth-operating-system-test-user"></a>Creación de un usuario de prueba de Synerise AI Growth Operating System

En esta sección, creará un usuario llamado Britta Simon en Synerise. Synerise admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si no existe un usuario en Synerise, se creará tras la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Synerise, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya directamente a la URL de inicio de sesión de Synerise y comience el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Synerise para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Synerise de Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Proware para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Synerise, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).