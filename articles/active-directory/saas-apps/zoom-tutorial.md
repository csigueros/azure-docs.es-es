---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Zoom | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zoom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: ddbfb074753dbc0906021b84ddff482fcf8debd7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764723"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Zoom

En este tutorial, obtendrá información sobre cómo integrar Zoom con Azure Active Directory (Azure AD). Al integrar Zoom con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Zoom.
* Permitir que los usuarios inicien sesión automáticamente en Zoom con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Zoom.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Zoom admite el inicio de sesión único iniciado por **SP**. 
* Zoom admite el [**aprovisionamiento automatizado** de usuarios](./zoom-provisioning-tutorial.md).

## <a name="adding-zoom-from-the-gallery"></a>Adición de Zoom desde la galería

Para configurar la integración de Zoom en Azure AD, deberá agregar Zoom desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Zoom** en el cuadro de búsqueda.
1. Seleccione **Zoom** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zoom"></a>Configuración y prueba del inicio de sesión único de Azure AD para Zoom

Configure y pruebe el inicio de sesión único de Azure AD con Zoom mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Zoom.

Para configurar y probar el inicio de sesión único de Azure AD con Zoom, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en Zoom](#configure-zoom-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Zoom](#create-zoom-test-user)** , para tener un homólogo de B. Simon en Zoom que esté vinculado a su representación en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Zoom**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.zoom.us`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `<companyname>.zoom.us`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.zoom.us`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Contacte con el [equipo de soporte al cliente de Zoom](https://support.zoom.us/hc/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Zoom**, copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

> [!NOTE]
> Para aprender a configurar el rol en Azure AD, consulte [Configuración de la notificación de rol emitida en el token SAML para aplicaciones empresariales](../develop/active-directory-enterprise-app-role-management.md).

> [!NOTE]
> El zoom puede esperar una notificación de grupo en la carga de SAML. Si ha creado algún grupo, póngase en contacto con el [equipo de soporte técnico al cliente de Zoom](https://support.zoom.us/hc/) con la información del grupo para que pueda configurarla en su extremo. También debe proporcionar el identificador de objeto al [equipo de soporte técnico de Zoom](https://support.zoom.us/hc/) para que puedan configurar el identificador de objeto en su extremo. Para obtener el identificador de objeto, consulte [Configuración de Zoom con Azure](https://support.zoom.us/hc/articles/115005887566).

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

En esta sección, va a permitir que B. Simon acceda a Zoom mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Zoom**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-zoom-sso"></a>Configuración del inicio de sesión único de Zoom

1. Para automatizar la configuración en Zoom, debe instalar la **extensión del navegador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Set up Zoom** (Configurar Zoom) para ir a la aplicación del mismo nombre. Ahí, escriba las credenciales de administrador para iniciar sesión en Zoom. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 6.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Zoom manualmente, en otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zoom como administrador.

2. Haga clic en la pestaña **Inicio de sesión único**.

    ![Pestaña Inicio de sesión único](./media/zoom-tutorial/zoom-sso1.png "Inicio de sesión único")

3. Haga clic en la pestaña **Control de seguridad** y vaya a la configuración **Inicio de sesión único**.

4. En la sección Inicio de sesión único, siga estos pasos:

    ![Sección Inicio de sesión único](./media/zoom-tutorial/zoom-sso2.png "Inicio de sesión único")

    a. En el cuadro de texto **Dirección URL de la página de inicio de sesión**, pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    b. Para el valor **Dirección URL de cierre de sesión**, debe ir a Azure Portal y hacer clic en **Azure Active Directory** a la izquierda y, a continuación, ir a **Registros de aplicaciones**.

    ![Botón Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Haga clic en **Puntos de conexión**.

    ![Botón del punto de conexión](./media/zoom-tutorial/endpoint.png)

    d. Copie el valor de **Punto de conexión de cierre de sesión de SAML-P** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.

    ![Botón de Copiar punto de conexión](./media/zoom-tutorial/endpoint1.png)

    e. Abra el certificado codificado en Base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado del proveedor de identidades**.

    f. En el cuadro de texto **Issuer** (Emisor), pegue el valor de **Identificador Azure AD** que ha copiado de Azure Portal. 

    g. Haga clic en **Guardar cambios**.

    > [!NOTE]
    > Para obtener más información, visite la documentación de Zoom [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566).

### <a name="create-zoom-test-user"></a>Creación de un usuario de prueba de Zoom

El objetivo de esta sección es crear una usuaria de prueba llamada B. Simon en Zoom. Zoom admite el aprovisionamiento automático de usuarios, que está habilitado de forma predeterminada. [Aquí](./zoom-provisioning-tutorial.md) puede encontrar más información sobre cómo configurar el aprovisionamiento automático de usuarios.

> [!NOTE]
> Si necesita crear manualmente un usuario, debe ponerse en contacto con el [equipo de soporte técnico al cliente de Zoom](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en **Probar esta aplicación** en Azure Portal. Se le redirigirá a la dirección URL de inicio de sesión de Zoom, donde puede iniciar el flujo de inicio de sesión.

* Vaya directamente a la dirección URL de inicio de sesión de Zoom e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Zoom en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de dicha aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Cuando haya configurado Zoom para Azure AD, puede aplicar el control de sesión, que protege a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).