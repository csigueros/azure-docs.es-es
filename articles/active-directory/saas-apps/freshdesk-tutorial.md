---
title: 'Tutorial: Integración de Azure Active Directory con Freshdesk | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 53ab7bcc6944d3f7650018238bd326037c5fff41
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746941"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integración de Azure Active Directory con Freshdesk

En este tutorial, obtendrá información sobre cómo integrar FreshDesk con Azure Active Directory (Azure AD). Al integrar FreshDesk con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a FreshDesk.
* Permitir que los usuarios inicien sesión automáticamente en FreshDesk con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:
 
* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en FreshDesk.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* FreshDesk admite el inicio de sesión único iniciado por **SP**.

## <a name="add-freshdesk-from-the-gallery"></a>Incorporación de FreshDesk desde la galería

Para configurar la integración de FreshDesk en Azure AD, deberá agregar FreshDesk desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **FreshDesk** en el cuadro de búsqueda.
1. Seleccione **FreshDesk** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Configuración y prueba del inicio de sesión único de Azure AD para FreshDesk

Configure y pruebe el inicio de sesión único de Azure AD con FreshDesk mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de FreshDesk.

Para configurar y probar el inicio de sesión único de Azure AD con FreshDesk, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de FreshDesk](#configure-freshdesk-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de FreshDesk](#create-freshdesk-test-user)** : para tener un homólogo de Britta Simon en FreshDesk que esté vinculado a la representación de ella en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

1. En Azure Portal, en la página de integración de la aplicación **FreshDesk**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz para **Configuración básica de SAML** y edite la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.freshdesk.com` u otro valor que Freshdesk haya sugerido.

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.freshdesk.com` u otro valor que Freshdesk haya sugerido.
     
    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de respuesta, el identificador y la dirección URL de inicio de sesión reales. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación FreshDesk espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. En la captura de pantalla siguiente se muestra la lista de atributos predeterminados; aunque al **identificador de usuario único** se le asigna **user.userprincipalname**, FreshDesk supone que a esta notificación se le asignará **user.mail**, por lo que debe editar la asignación de atributos haciendo clic en el icono Editar y cambiarla.

    ![imagen](common/edit-attribute.png)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar FreshDesk**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a FreshDesk mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **FreshDesk**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-freshdesk-sso"></a>Configuración del inicio de sesión único de FreshDesk

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Freshdesk como administrador.

2. Seleccione el icono **Security** (Seguridad) y, en la sección **Security** (Seguridad), siga estos pasos:

    ![Inicio de sesión único](./media/freshdesk-tutorial/configure-1.png "Inicio de sesión único")
  
    a. En **Single Sign On** (Inicio de sesión único), seleccione **On** (Activado).

    b. En **Login Method** (Método de inicio de sesión), seleccione **SAML SSO** (SSO de SAML).

    c. En el cuadro de texto **Entity ID provided by the IdP** (Identificador de entidad proporcionado por el IdP), pegue el valor de **Entity ID** (Id. de entidad) que ha copiado de Azure Portal.

    d. En el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión único de SAML), pegue el valor de **URL de inicio de sesión** que copió en Azure Portal.

    e. En **Signing Options** (Opciones de firma), seleccione **Only Signed Assertions** (Solo aserciones firmadas) en la lista desplegable.

    f. En el cuadro de texto **Sign Out URL** (URL de cierre de sesión), pegue el valor de **Logout URL** (Dirección URL de cierre de sesión) que ha copiado de Azure Portal.

    g. En el cuadro de texto **Security Certificate** (Certificado de seguridad), pegue el valor de **Certificate (Base64)** (Certificado [Base64]), que ha obtenido anteriormente.
  
    h. Haga clic en **Save**(Guardar).

## <a name="create-freshdesk-test-user"></a>Creación de un usuario de prueba de FreshDesk

Para permitir que los usuarios de Azure AD inicien sesión en FreshDesk, deben aprovisionarse en FreshDesk.  
En el caso de FreshDesk, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Freshdesk** .

1. En el menú de la izquierda, haga clic en **Admin** (Administrador) y, en la pestaña **General Settings** (Configuración general), haga clic en **Agents** (Agentes).
  
    ![Agentes](./media/freshdesk-tutorial/create-user-1.png "Agentes")

1. Haga clic en **Nuevo agente**.

    ![New Agent (Nuevo agente)](./media/freshdesk-tutorial/create-user-2.png "Nuevo agente")

1. En el cuadro de diálogo Agent Information (Información del agente), rellene los campos obligatorios y haga clic en **Create agent** (Crear agente).

    ![Agent Information (Información de agente)](./media/freshdesk-tutorial/create-user-3.png "Información de agente")

    >[!NOTE]
    >El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico que incluye un vínculo para confirmar la cuenta antes de que se active.
    >
    >[!NOTE]
    >Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Freshdesk que proporcione Freshdesk para aprovisionar cuentas de usuario de Azure AD en FreshDesk.

### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de FreshDesk, desde donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la URL de inicio de sesión de FreshDesk y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de FreshDesk en el Panel de acceso, iniciará sesión automáticamente en la instancia de FreshDesk para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado FreshDesk, puede aplicar el control de sesión, que protege contra la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).