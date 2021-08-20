---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Leapsome | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Leapsome.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/22/2021
ms.author: jeedes
ms.openlocfilehash: 152b90117066f12be0f9b2039f65d2f511b69d88
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688941"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Leapsome

En este tutorial aprenderá a integrar Leapsome con Azure Active Directory (Azure AD). Al integrar Leapsome con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Leapsome.
* Permitir que los usuarios inicien sesión automáticamente en Leapsome con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Leapsome.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Leapsome admite el inicio de sesión único iniciado por **SP e IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-leapsome-from-the-gallery"></a>Incorporación de Leapsome desde la galería

Para configurar la integración de Leapsome en Azure AD, deberá agregar Leapsome desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Leapsome** en el cuadro de búsqueda.
1. Seleccione **Leapsome** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-leapsome"></a>Configuración y prueba del inicio de sesión único de Azure AD para Leapsome

Configure y pruebe el inicio de sesión único de Azure AD con Leapsome mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Leapsome.

Para configurar y probar el inicio de sesión único de Azure AD con Leapsome, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Leapsome](#configure-leapsome-sso)**: para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Leapsome](#create-leapsome-test-user)**: para tener un homólogo de B.Simon en Leapsome vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Leapsome**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://www.leapsome.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > El valor de URL de respuesta y URL de inicio de sesión no es un valor real. Estos se actualizarán con los valores reales, lo que se explica más adelante en el tutorial.

1. La aplicación Leapsome espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Leapsome espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre | Atributo de origen | Espacio de nombres |
    | ---------------| --------------- | --------- |  
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | picture | URL de una fotografía del empleado | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > El valor del atributo picture no es real. Actualice este valor con la URL de la imagen real. Para obtener este valor, póngase en contacto con el [equipo de soporte técnico de Leapsome](mailto:support@leapsome.com).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up Leapsome** (Configurar Leapsome), copie las direcciones URL que necesite.

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

En esta sección, va a conceder a B.Simon acceso a Leapsome mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Leapsome**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-leapsome-sso"></a>Configuración del inicio de sesión único en Leapsome

1. En otra ventana del explorador web, inicie sesión en Leapsome como administrador de seguridad.

1. En la parte superior derecha, haga clic en el logotipo de configuración y luego en **Configuración de administración**.

    ![Conjunto de Leapsome](./media/leapsome-tutorial/admin.png)

1. En la barra de menús izquierda, haga clic en **Inicio de sesión único (SSO)** y en la página **Inicio de sesión único basado en SAML (SSO)** siga estos pasos:

    ![SAML de Leapsome](./media/leapsome-tutorial/settings.png)

    a. Seleccione **Enable SAML-based single sign-on** (Habilitar inicio de sesión único basado en SAML).

    b. Copie el valor **Login URL (point your users here to start login)** [URL de inicio de sesión (apuntar a los usuarios aquí para iniciar el inicio de sesión)] y péguelo en el cuadro de texto **URL de inicio de sesión** en la sección **Configuración básica de SAML** de Azure Portal.

    c. Copie el valor **Reply URL (receives response from your identity provider)** [URL de respuesta (recibe respuesta desde el proveedor de identidades)] y péguelo en el cuadro de texto **URL de respuesta** en la sección **Configuración básica de SAML** de Azure Portal.

    d. En el cuadro de texto **SSO Login URL (provided by identity provider)** [Dirección URL de inicio de sesión único (proporcionada por el proveedor de identidades)], pegue el valor de **URL de inicio de sesión**, que ha copiado de Azure Portal.

    e. Copie el certificado que ha descargado desde Azure Portal sin los comentarios `--BEGIN CERTIFICATE and END CERTIFICATE--` y péguelo en el cuadro de texto **Certificate (provided by identity provider)** [Certificado (proporcionado por el proveedor de identidades)].

    f. Haga clic en **UPDATE SSO SETTINGS** (ACTUALIZAR CONFIGURACIÓN DE SSO).

### <a name="create-leapsome-test-user"></a>Creación de un usuario de prueba de Leapsome

En esta sección, creará un usuario llamado Britta Simon en Leapsome. Colabore con el [equipo de soporte técnico de Leapsome](mailto:support@leapsome.com) para agregar el dominio o los usuarios necesarios a una lista blanca de la plataforma de Leapsome. Si el dominio lo agrega el equipo, los usuarios se aprovisionarán automáticamente en la plataforma de Leapsome. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Leapsome, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Leapsome e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Leapsome para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Leapsome en Mis aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de Leapsome para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Leapsome, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
