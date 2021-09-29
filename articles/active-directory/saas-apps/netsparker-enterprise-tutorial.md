---
title: 'Tutorial: Integración de inicio de sesión único (SSO) de Azure Active Directory con Netsparker Enterprise | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Netsparker Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: e105bd22eb99a0371ef40b7b8b8c133abda401d6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746873"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsparker-enterprise"></a>Tutorial: Integración de inicio de sesión único (SSO) de Azure Active Directory con Netsparker Enterprise

En este tutorial, aprenderá a integrar Netsparker Enterprise con Azure Active Directory (Azure AD). Al integrar Netsparker Enterprise con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Netsparker Enterprise.
* Permitir que los usuarios inicien sesión automáticamente en Netsparker Enterprise con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Netsparker Enterprise.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Netsparker Enterprise admite el inicio de sesión único iniciado por **SP e IDP**.
* Netsparker Enterprise admite el aprovisionamiento de usuarios **Just-In-Time**

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.


## <a name="adding-netsparker-enterprise-from-the-gallery"></a>Incorporación de Netsparker Enterprise desde la galería

Para configurar la integración de Netsparker Enterprise en Azure AD, es preciso agregar la aplicación desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Netsparker Enterprise** en el cuadro de búsqueda.
1. Seleccione **Netsparker Enterprise** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-netsparker-enterprise"></a>Configuración y prueba de inicio de sesión único de Azure AD para Netsparker Enterprise

Configure y pruebe el inicio de sesión único de Azure AD con Netsparker Enterprise con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Netsparker Enterprise.

Para configurar y probar el inicio de sesión único de Azure AD con Netsparker Enterprise, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración de inicio de sesión único en Netsparker Enterprise](#configure-netsparker-enterprise-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Netsparker Enterprise](#create-netsparker-enterprise-test-user)** , para tener un homólogo de B.Simon en Netsparker Enterprise que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Netsparker Enterprise**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.netsparkercloud.com/account/assertionconsumerservice/?spId=<SPID>`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://www.netsparkercloud.com/account/ssosignin/`

    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. Actualícelo con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de Netsparker Enterprise](mailto:support@netsparker.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal. 

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Netsparker Enterprise**, copie las direcciones URL adecuadas en función de sus necesidades.

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

En esta sección va a permitir que B.Simon acceda a Netsparker Enterprise mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Netsparker Enterprise**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-netsparker-enterprise-sso"></a>Configuración del inicio de sesión único de Netsparker Enterprise

1.  Inicie sesión en Netsparker Enterprise como administrador.

1. Vaya a **Configuración > Inicio de sesión único**.

1. En la ventana **Inicio de sesión único**, seleccione la pestaña **Azure Active Directory**. 

1. En la página siguiente, realice estos pasos.

    ![Pestaña Azure Active Directory](./media/netsparker-enterprise-tutorial/configure-sso.png)

    a. Copie el valor de **Identifier** (Identificador) y péguelo en el cuadro de texto **Identificador** de la sección **Configuración básica de SAML** en Azure Portal.

    b. Copie el valor de **SAML 2.0 Service URL** (URL del servicio SAML 2.0), péguelo en el cuadro de texto **URL de respuesta**, en la sección **Configuración básica de SAML** de Azure Portal.

    c. Pegue el valor de **Identificador**, que ha copiado de Azure Portal, en **IdP Identifier** (Identificador de IdP).

    e. Pegue el valor de **URL de respuesta**, que ha copiado de Azure Portal, en el campo **SAML 2.0 Endpoint** (Punto de conexión de SAML 2.0).

    f. Abra el archivo del **certificado (Base64)** que descargó de Azure Portal en el Bloc de notas, copie el contenido y péguelo en el cuadro de texto **Certificado X.509**.

    g. Active **Enable Auto Provisioning** (Habilitar aprovisionamiento automático) y **Require SAML assertions to be encrypted** (Exigir cifrado de aserciones SAML) según sea necesario.

    h. Haga clic en **Guardar cambios**.

### <a name="create-netsparker-enterprise-test-user"></a>Creación de un usuario de prueba de Netsparker Enterprise

En esta sección, se creará una usuaria llamada Britta Simon en Netsparker Enterprise. Netsparker Enterprise admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe ya en Netsparker Enterprise, se creará uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Netsparker Enterprise, donde puede poner en marcha el flujo de inicio de sesión.  

* Acceda directamente a la dirección URL de inicio de sesión de Netsparker Enterprise y comience el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Netsparker Enterprise para la que configuró el inicio de sesión único. 

También puede usar el Panel de acceso de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Netsparker Enterprise en el panel de acceso: si está configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si está configurado en modo IDP, debería iniciarse sesión automáticamente en la instancia de Netsparker Enterprise para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Netsparker Enterprise, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).