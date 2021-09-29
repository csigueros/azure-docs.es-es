---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con HowNow WebApp SSO | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y HowNow WebApp SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: jeedes
ms.openlocfilehash: 63209118be15bb61a0b85e27fe9541601169274f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124772873"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hownow-webapp-sso"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con HowNow WebApp SSO

En este tutorial, aprenderá a integrar HowNow WebApp SSO con Azure Active Directory (Azure AD). Al integrar HowNow WebApp SSO con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a HowNow WebApp SSO.
* Permitir que los usuarios inicien sesión automáticamente en HowNow WebApp SSO con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) de HowNow WebApp SSO.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* HowNow WebApp SSO admite el inicio de sesión único habilitado mediante **SP**.

* HowNow WebApp SSO admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="adding-hownow-webapp-sso-from-the-gallery"></a>Incorporación de HowNow WebApp SSO desde la galería

Para configurar la integración de HowNow WebApp SSO en Azure AD, es necesario agregar la aplicación desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **HowNow WebApp SSO** en el cuadro de búsqueda.
1. Seleccione **HowNow WebApp SSO** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-hownow-webapp-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD para HowNow WebApp SSO

Configure y pruebe el inicio de sesión único de Azure AD con HowNow WebApp SSO mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de HowNow WebApp SSO.

Para configurar y probar el inicio de sesión único de Azure AD con HowNow WebApp SSO, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en HowNow WebApp SSO](#configure-hownow-webapp-sso-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de HowNow SSO](#create-hownow-webapp-sso-test-user)** , para tener un usuario equivalente a B.Simon en la aplicación que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **HowNow WebApp SSO**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<CUSTOMER_NAME>.hownow.app/users/saml/sign_in`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<CUSTOMER_NAME>.hownow.app/users/saml/metadata`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<CUSTOMER_NAME>.hownow.app/users/saml/auth`

    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de respuesta, el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte al cliente de HowNow WebApp SSO](mailto:support@gethownow.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la sección **Certificado de firma de SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Certificado de firma de SAML**.

    ![Edición del certificado de firma de SAML](common/edit-certificate.png)

1. En la sección **Certificado de firma de SAML**, copie el valor de **Huella digital** y guárdelo en el equipo.

    ![Copia del valor de la huella digital](common/copy-thumbprint.png)

1. En la sección **Configurar HowNow WebApp SSO**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a HowNow WebApp SSO mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **HowNow WebApp SSO**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-hownow-webapp-sso-sso"></a>Configuración del inicio de sesión único de HowNow WebApp SSO

Para configurar el inicio de sesión único en **HowNow WebApp SSO**, es preciso enviar el **valor de la huella digital** y las URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de HowNow WebApp SSO](mailto:support@gethownow.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-hownow-webapp-sso-test-user"></a>Creación de un usuario de prueba de HowNow WebApp SSO

En esta sección, se creará una usuaria llamada Britta Simon en HowNow WebApp SSO. HowNow WebApp SSO admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si todavía no hay ningún usuario en HowNow WebApp SSO, se creará uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de HowNow WebApp SSO, donde puede poner en marcha el flujo de inicio de sesión. 

* Acceda directamente a la URL de inicio de sesión de HowNow WebApp SSO y ponga en marcha el flujo de inicio de sesión desde ahí.

* Puede usar el Panel de acceso de Microsoft. Al hacer clic en el icono de HowNow WebApp SSO en el panel de acceso, se le redirigirá a URL de inicio de sesión de la aplicación. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado HowNow WebApp SSO, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).