---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Zero Trust Remote Access Platform | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zero Trust Remote Access Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 5794c8353c466f405bc3829b34f89db6f5c394c6
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111949560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zero-trust-remote-access-platform"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Zero Trust Remote Access Platform

En este tutorial, aprenderá a integrar Zero Trust Remote Access Platform con Azure Active Directory (Azure AD). Al integrar Zero Trust Remote Access Platform con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Zero Trust Remote Access Platform.
* Permitir que los usuarios inicien sesión automáticamente en Zero Trust Remote Access Platform con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Zero Trust Remote Access Platform.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Zero Trust Remote Access Platform admite el inicio de sesión único iniciado por **SP e IDP**.
* Zero Trust Remote Access Platform admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-zero-trust-remote-access-platform-from-the-gallery"></a>Adición de Zero Trust Remote Access Platform desde la galería

Para configurar la integración de Zero Trust Remote Access Platform en Azure AD, tendrá que agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Zero Trust Remote Access Platform** en el cuadro de búsqueda.
1. Seleccione **Zero Trust Remote Access Platform** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zero-trust-remote-access-platform"></a>Configuración y prueba del inicio de sesión único de Azure AD para Zero Trust Remote Access Platform

Configure y pruebe el inicio de sesión único de Azure AD con Zero Trust Remote Access Platform mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Zero Trust Remote Access Platform.

Para configurar y probar el inicio de sesión único de Azure AD con Zero Trust Remote Access Platform, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Zero Trust Remote Access Platform](#configure-zero-trust-remote-access-platform-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Zero Trust Remote Access Platform](#create-zero-trust-remote-access-platform-test-user)** : para tener un homólogo de B.Simon en Zero Trust Remote Access Platform que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Zero Trust Remote Access Platform**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico para clientes de Zero Trust Remote Access Platform](mailto:support@banyansecurity.io) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

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

En esta sección, va a permitir que B.Simon acceda a Zero Trust Remote Access Platform mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Zero Trust Remote Access Platform**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-zero-trust-remote-access-platform-sso"></a>Configuración del inicio de sesión único de Zero Trust Remote Access Platform

1. Inicie sesión como administrador en el sitio web de Zero Trust Remote Access Platform.

1. Vaya a **Admin Settings -> Admin Sign-on** (Configuración del administrador > Inicio de sesión de administrador).

1. Realice los pasos siguientes en la página **Sign-on Settings** (Configuración de inicio de sesión).

    ![Captura de pantalla de la configuración de inicio de sesión.](./media/banyan-command-center-tutorial/configuration.png)

    a. En **Sign-On Method** (Método de inicio de sesión), seleccione **Single Sign On - SAML 2.0** (Inicio de sesión único: SAML 2.0) en la lista desplegable.

    b. Copie el valor de **IDP Issuer** (Emisor del IDP) y péguelo en el cuadro de texto **Identificador de Azure AD** de la sección Configuración básica de SAML en Azure Portal.

    c. Pegue el valor de la **dirección URL de metadatos de federación de la aplicación** en el cuadro de texto **IDP Metadata URL** (Dirección URL de metadatos del IDP).

    d. Haga clic en el botón **Update** (Actualizar).

### <a name="create-zero-trust-remote-access-platform-test-user"></a>Creación de un usuario de prueba de Zero Trust Remote Access Platform

En esta sección se crea un usuario llamado Britta Simon en Zero Trust Remote Access Platform. Zero Trust Remote Access Platform admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si todavía no hay ningún usuario en Zero Trust Remote Access Platform, se creará uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Zero Trust Remote Access Platform, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Zero Trust Remote Access Platform e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Zero Trust Remote Access Platform para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Zero Trust Remote Access Platform en Mis aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de Zero Trust Remote Access Platform para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Banyan Command Center, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
