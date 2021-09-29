---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con FortiWeb Web Application Firewall| Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y FortiWeb Web Application Firewall.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: c8e79373a4a3823ec616adec4a5c29d782b60913
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124747071"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con FortiWeb Web Application Firewall

En este tutorial, aprenderá a integrar FortiWeb Web Application Firewall con Azure Active Directory (Azure AD). Al integrar FortiWeb Web Application Firewall con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a FortiWeb Web Application Firewall.
* Permitir que los usuarios inicien sesión automáticamente en FortiWeb Web Application Firewall con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en FortiWeb Web Application Firewall.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* FortiWeb Web Application Firewall admite el inicio de sesión único habilitado por **SP**.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Incorporación de FortiWeb Web Application Firewall desde la galería

Para configurar la integración de FortiWeb Web Application Firewall en Azure AD, debe agregar la aplicación desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **FortiWeb Web Application Firewall** en el cuadro de búsqueda.
1. Seleccione **FortiWeb Web Application Firewall** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Configuración y prueba del inicio de sesión único de Azure AD para FortiWeb Web Application Firewall

Configure y pruebe el inicio de sesión único de Azure AD con FortiWeb Web Application Firewall con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de FortiWeb Web Application Firewall.

Para configurar y probar el inicio de sesión único de Azure AD con FortiWeb Web Application Firewall, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de FortiWeb Web Application Firewall](#configure-fortiweb-web-application-firewall-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en FortiWeb Web Application Firewall](#create-fortiweb-web-application-firewall-test-user)** , para tener un homólogo de B.Simon en FortiWeb Web Application Firewall vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **FortiWeb Web Application Firewall**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

   a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://www.<CUSTOMER_DOMAIN>.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.<CUSTOMER_DOMAIN>.com`

    d. En el cuadro de texto **URL de cierre de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`.
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` es un identificador de nombre que se usará más adelante al especificar la configuración para FortiWeb.
    > Póngase en contacto el [equipo de soporte técnico de FortiWeb Web Application Firewall](mailto:support@fortinet.com) para obtener los valores reales de URL. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)


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

En esta sección, va a permitir que B.Simon acceda a FortiWeb Web Application Firewall mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **FortiWeb Web Application Firewall**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Configuración del inicio de sesión único de FortiWeb Web Application Firewall

1.  Acceda a `https://<address>:8443`, en donde `<address>` representa el nombre de dominio completo o la dirección IP pública que se ha asignado a la máquina virtual de FortiWeb.

2.  Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la máquina virtual de FortiWeb.

1. En la página siguiente, realice estos pasos.

    ![Página del servidor SAML](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  En el menú de la izquierda, haga clic en **Usuario**.

    b.  En Usuario, haga clic en **Servidor remoto**.

    c.  Haga clic en **SAML Server** (Servidor SAML).

    d.  Haga clic en **Crear nuevo**.

    e.  En el campo **Name** (Nombre), proporcione el valor de `<fwName>` utilizado en la sección de configuración de Azure AD.

    f.  En el cuadro de texto **Identificador de entidad**, pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    g. Junto a **Metadata** (Metadatos), haga clic en **Choose File** (Elegir archivo) y seleccione el archivo **XML de metadatos de federación** que ha descargado de Azure Portal.

    h.  Haga clic en **OK**.

### <a name="create-a-site-publishing-rule"></a>Creación de una regla de publicación de sitio

1.  Acceda a `https://<address>:8443`, en donde `<address>` representa el nombre de dominio completo o la dirección IP pública que se ha asignado a la máquina virtual de FortiWeb.

1.  Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la máquina virtual de FortiWeb.
1. En la página siguiente, realice estos pasos.

    ![Regla de publicación de sitio](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  En el menú de la izquierda, haga clic en **Application Delivery** (Entrega de aplicaciones).
    
    b.  En **Application Delivery** (Entrega de aplicaciones), haga clic en **Site Publish** (Publicación de sitio).
    
    c.  Debajo de **Site Publish** (Publicación de sitio), haga clic en **Site Publish** (Publicación de sitio).
    
    d.  Haga clic en **Site Publish Rule** (Regla de publicación de sitio).
    
    e.  Haga clic en **Crear nuevo**.
    
    f.  Proporcione un nombre para la regla de publicación de sitio.
    
    g.  Junto a **Published Site Type** (Tipo de sitio publicado), haga clic en **Regular Expression** (Expresión regular).
    
    i.  Junto a **Published Site** (Sitio publicado), especifique una cadena que coincida con el encabezado de host del sitio web que va a publicar.
    
    j.  Junto a **Path** (Ruta de acceso), especifique /.
    
    k.  Junto a **Client Authentication Method** (Método de autenticación de cliente), seleccione **Autenticación SAML**.
    
    l.  En la lista desplegable **SAML Server** (Servidor SAML), seleccione el servidor SAML que creó anteriormente.
    
    m.  Haga clic en **OK**.


### <a name="create-a-site-publishing-policy"></a>Creación de una directiva de publicación de sitio

1.  Acceda a `https://<address>:8443`, en donde `<address>` representa el nombre de dominio completo o la dirección IP pública que se ha asignado a la máquina virtual de FortiWeb.

2.  Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la máquina virtual de FortiWeb.

1. En la página siguiente, realice estos pasos.

    ![Directiva de publicación de sitio](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  En el menú de la izquierda, haga clic en **Application Delivery** (Entrega de aplicaciones).

    b.  En **Application Delivery** (Entrega de aplicaciones), haga clic en **Site Publish** (Publicación de sitio).

    c.  Debajo de **Site Publish** (Publicación de sitio), haga clic en **Site Publish** (Publicación de sitio).

    d.  Haga clic en **Site Publish Policy** (Directiva de publicación de sitio).

    e.  Haga clic en **Crear nuevo**.

    f.  Proporcione un nombre para la directiva de publicación de sitio.

    g.  Haga clic en **OK**.

    h.  Haga clic en **Crear nuevo**.

    i.  En la lista desplegable **Rule** (Regla), seleccione la regla de publicación de sitio que creó anteriormente.

    j.  Haga clic en **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Creación y asignación de un perfil de protección web

1.  Acceda a `https://<address>:8443`, en donde `<address>` representa el nombre de dominio completo o la dirección IP pública que se ha asignado a la máquina virtual de FortiWeb.

2.  Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la máquina virtual de FortiWeb.
3.  En el menú de la izquierda, haga clic en **Policy** (Directiva).
4.  En **Policy** (Directiva), haga clic en **Web Protection Profile** (Perfil de protección web).
5.  Haga clic en **Inline Standard Protection** (Protección estándar incorporada) y en **Clone** (Clonar).
6.  Proporcione un nombre para el nuevo perfil de protección web y haga clic en **OK** (Aceptar).
7.  Seleccione el nuevo perfil de protección web y haga clic en **Edit** (Editar).
8.  Junto a **Site Publish** (Publicación de sitio), seleccione la directiva de publicación de sitio que creó anteriormente.
9.  Haga clic en **OK**.
 
    ![publicación de sitio](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. En el menú de la izquierda, haga clic en **Policy** (Directiva).
11. En **Policy** (Directiva), haga clic en **Server Policy** (Directiva del servidor).
12. Seleccione la directiva de servidor utilizada para publicar el sitio web para el que desea usar Azure Active Directory con fines de autenticación.
13. Haga clic en **Editar**.
14. En la lista desplegable **Web Protection Profile** (Perfil de protección web), seleccione el perfil de protección web que acaba de crear.
15. Haga clic en **OK**.
16. Intente acceder a la dirección URL externa en la que FortiWeb publica el sitio web. Debería redirigirle a Azure Active Directory con fines de autenticación.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Creación de un usuario de prueba de FortiWeb Web Application Firewall

En esta sección, creará un usuario llamado Britta Simon en FortiWeb Web Application Firewall. Trabaje con el [equipo de soporte técnico de FortiWeb Web Application Firewall](mailto:support@fortinet.com) para agregar los usuarios a la plataforma de FortiWeb Web Application Firewall. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de FortiWeb Web Application, desde donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la URL de inicio de sesión de FortiWeb Web Application y comience el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de FortiWeb Web Application en Mis aplicaciones, se le redirigirá a la URL de inicio de sesión de la aplicación FortiWeb Web Application. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado FortiWeb Web Application Firewall, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).