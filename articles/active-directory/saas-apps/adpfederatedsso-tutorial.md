---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con ADP | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ADP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: ab53dc218bee83476811e6d42dec76479559b50b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803240"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con ADP

En este tutorial, aprenderá a integrar ADP en Azure Active Directory (Azure AD). Al integrar ADP en Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a ADP.
* Permitir que los usuarios puedan iniciar sesión automáticamente en ADP con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).


## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en ADP.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* ADP admite inicio de sesión único iniciado por **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-adp-from-the-gallery"></a>Adición de ADP desde la galería

Para configurar la integración de ADP en Azure AD, será preciso agregar ADP desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **ADP** en el cuadro de búsqueda.
1. Seleccione **ADP** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adp"></a>Configuración y prueba del inicio de sesión único de Azure AD para ADP

Configure y pruebe el inicio de sesión único de Azure AD con ADP mediante una usuaria de prueba llamada **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de ADP.

Para configurar y probar el inicio de sesión único de Azure AD con ADP, realice los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en ADP](#configure-adp-sso)**, para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de ADP](#create-adp-test-user)**, para tener un homólogo de B.Simon en ADP que esté vinculado a la representación de ella en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, vaya a la página de integración de la aplicación **ADP**, haga clic en la **pestaña Propiedades** y realice los pasos siguientes: 

    ![Propiedades del inicio de sesión único](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Establezca el valor del campo **Habilitado para que los usuarios inicien sesión** en **Sí**.

    b. Copie la **dirección URL de acceso de usuario** y péguela en la **sección Configurar dirección URL de inicio de sesión** (este paso se explica más adelante en el tutorial).

    c. Establezca el valor del campo **Asignación de usuarios necesaria** en **Sí**.

    d. Establezca el valor del campo **Visible para los usuarios** en **No**.

1. En Azure Portal, en la página de integración de la aplicación **ADP**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **Identificador (Id. de entidad)** , escriba una dirección URL: `https://fed.adp.com`

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Configurar ADP**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a ADP mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **ADP**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-adp-sso"></a>Configuración del inicio de sesión único de ADP

Para configurar el inicio de sesión único en **ADP**, debe cargar el archivo **XML de metadatos** que descargó en el [sitio web de ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Este proceso puede tardar unos días.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurar los servicios de ADP para obtener acceso federado

>[!Important]
> Los empleados que necesiten obtener acceso federado a los servicios de ADP deben asignarse a la aplicación de servicio de ADP y, posteriormente, deben volver a asignarse al servicio ADP específico.
Una vez recibida la confirmación del representante de ADP, configure los servicios de ADP y asigne o administre los usuarios para controlar su acceso al servicio de ADP especificado.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **ADP** en el cuadro de búsqueda.
1. Seleccione **ADP** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.
1. En Azure Portal, vaya a la página de integración de la aplicación **ADP**, haga clic en la **pestaña Propiedades** y realice los pasos siguientes:  

    ![Propiedades vinculadas de inicio de sesión único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    1. Establezca el valor del campo **Habilitado para que los usuarios inicien sesión** en **Sí**.

    1. Establezca el valor del campo **Asignación de usuarios necesaria** en **Sí**.

    1. Establezca el valor del campo **Visible para los usuarios** en **Sí**.

1. En Azure Portal, en la página de integración de la aplicación **ADP**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.

1. En el cuadro de diálogo **Seleccione un método de inicio de sesión único**, elija como **Modo** la opción **Vinculado**. Para vincular la aplicación a **ADP**.

    ![Inicio de sesión único vinculado](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navegue hasta la sección **Configurar la dirección URL de inicio de sesión** y realice los pasos siguientes:

    ![Propiedades del inicio de sesión único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    1. Pegue la **dirección URL de acceso de usuario** que copió de la **pestaña Propiedades** anterior (de la aplicación principal ADP).

    1. Estas son las 5 aplicaciones que admiten diferentes **direcciones URL del estado de la retransmisión**. Tiene que anexar de forma manual el valor adecuado de la **dirección URL del estado de la retransmisión** de una aplicación en particular a la **dirección URL de acceso de usuarios**.

        * **ADP Workforce Now**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **ADP Workforce Now Enhanced Time**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vantage HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. Guarde los cambios mediante **Guardar**.

1. Tras recibir la confirmación del representante de ADP, comience la prueba con uno o dos usuarios.

    1. Asigne unos cuantos usuarios a la aplicación del servicio de ADP para que prueben el acceso federado.

    1. La prueba es correcta cuando los usuarios acceden a la aplicación del servicio de ADP en la galería y pueden obtener acceso a su servicio ADP.

1. Cuando se confirme una prueba correcta, asigne el servicio de ADP federado a usuarios individuales o a grupos de usuarios (esto se explica más adelante en el tutorial) e implántelo entre sus empleados.

### <a name="create-adp-test-user"></a>Creación de un usuario de prueba de ADP

El objetivo de esta sección es crear una usuaria de prueba llamada B.Simon en ADP. Colabore con el [equipo de soporte técnico de ADP](https://www.adp.com/contact-us/overview.aspx) para agregar los usuarios a la cuenta de ADP. 

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal y debería iniciar sesión automáticamente en la instancia de ADP para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de ADP en Aplicaciones, debería iniciar sesión automáticamente en la instancia de ADP para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado ADP, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).