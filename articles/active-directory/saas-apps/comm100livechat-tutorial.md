---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Comm100 Live Chat | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Comm100 Live Chat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: jeedes
ms.openlocfilehash: 53a7466cf59d6b21bb6a8a32c3534cffd8045f71
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817291"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Comm100 Live Chat

En este tutorial, aprenderá a integrar Comm100 Live Chat con Azure Active Directory (Azure AD). Si integra Comm100 Live Chat con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Comm100 Live Chat.
* Permitir que los usuarios inicien sesión automáticamente en Comm100 Live Chat con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único de Comm100 Live Chat.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Comm100 Live Chat admite el inicio de sesión único iniciado por **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-comm100-live-chat-from-the-gallery"></a>Adición de Comm100 Live Chat desde la galería

Para configurar la integración de Comm100 Live Chat en Azure AD, será preciso que agregue Comm100 Live Chat Comm100 Live Chat desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Comm100 Live Chat** en el cuadro de búsqueda.
1. Seleccione **Comm100 Live Chat** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-comm100-live-chat"></a>Configuración y prueba del inicio de sesión único de Azure AD para Comm100 Live Chat

Configure y pruebe el inicio de sesión único de Azure AD con Comm100 Live Chat utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Comm100 Live Chat.

Para configurar y probar el inicio de sesión único de Azure AD con Comm100 Live Chat, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
   1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
   1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Comm100 Live Chat](#configure-comm100-live-chat-sso)** , para configurar el inicio de sesión único en la aplicación.
   1. **[Creación de un usuario de prueba de Comm100 Live Chat](#create-comm100-live-chat-test-user)** , para tener un homólogo de B.Simon en Comm100 Live Chat que esté vinculado a la representación de este usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Comm100 Live Chat**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > El valor de la dirección URL de inicio de sesión no es real. El valor de la dirección URL de inicio de sesión se actualizará con el real, lo que se explica más adelante en el tutorial.

1. La aplicación Comm100 Live Chat espera las aserciones de SAML en un formato específico. Para ello, es necesario agregar asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/edit-attribute.png)

1. Además de lo anterior, la aplicación Comm100 Live Chat espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre |  Atributo de origen|
    | ---------------| --------------- |
    |   email    | user.mail |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Comm100 Live Chat**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, permitirá que B.Simon pueda acceder a Comm100 Live Chat utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Comm100 Live Chat**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-comm100-live-chat-sso"></a>Configuración del inicio de sesión único en Comm100 Live Chat

1. En otra ventana del explorador web, inicie sesión en Comm100 Live Chat como administrador de seguridad.

1. En la parte superior derecha de la página, haga clic en **My Account** (Mi cuenta).

   ![Mi cuenta de Comm100 Live Chat.](./media/comm100livechat-tutorial/account.png)

1. En el lado izquierdo del menú, haga clic en **Seguridad** y, a continuación, haga clic en **Agent Single Sign-On** (Agente Inicio de sesión único).

   ![Captura de pantalla que muestra el menú de la cuenta del lado izquierdo con las opciones "Seguridad" y "Agent Single Sign-On" (Agente Inicio de sesión único) resaltadas.](./media/comm100livechat-tutorial/security.png)

1. En la página **Agent Single Sign-On** (Agente Inicio de sesión único) realice los pasos siguientes:

   ![Seguridad de Comm100 Live Chat.](./media/comm100livechat-tutorial/certificate.png)

   a. Copie el primer vínculo resaltado y cópielo en el cuadro de texto **URL de inicio de sesión** de la sección **Configuración básica de SAML** de Azure Portal.

   b. En el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión único de SAML), pegue el valor de **Dirección URL de inicio de sesión** que copió de Azure Portal.

   c. En el cuadro de texto **Remote Logout URL** (Dirección URL de cierre de sesión remoto), pegue el valor de **Dirección URL de cierre de sesión** que copió de Azure Portal.

   d. Haga clic en **Choose a File** (Elegir un archivo) para cargar el certificado codificado en base 64 que descargó de Azure Portal en **Certificado**.

   e. Haga clic en **Guardar cambios**.

### <a name="create-comm100-live-chat-test-user"></a>Creación de un usuario de prueba de Comm100 Live Chat

Para permitir que los usuarios de Azure AD puedan iniciar sesión en Comm100 Live Chat, es necesario aprovisionarlos en esta aplicación. En Comm100 Live Chat, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en Comm100 Live Chat como administrador de seguridad.

2. En la parte superior derecha de la página, haga clic en **My Account** (Mi cuenta).

    ![Mi cuenta de Comm100 Live Chat.](./media/comm100livechat-tutorial/account.png)

3. En el lado izquierdo del menú, haga clic en **Agents** (Agentes) y, a continuación, haga clic en **New Agent** (Nuevo agente).

    ![Agente de Comm100 Live Chat.](./media/comm100livechat-tutorial/agent.png)

4. En la página **New Agent** (Nuevo agente), realice los pasos siguientes:

    ![Nuevo agente de Comm100 Live Chat.](./media/comm100livechat-tutorial/new-agent.png)

    a. a. En el cuadro de texto **Email** (Correo electrónico), escriba el correo electrónico del usuario; por ejemplo, **B.simon\@contoso.com**.

    b. En el cuadro de texto **First Name** (Nombre), escriba el nombre del usuario, en este caso **B**.

    c. En el cuadro de texto **Last Name** (Apellidos), escriba los apellidos del usuario, en este caso **Simon**.

    d. En el cuadro de texto **Display Name** (Nombre para mostrar), escriba el nombre para mostrar del usuario; en este caso, **B.simon**.

    e. En el cuadro de texto **Password** (Contraseña), escriba su contraseña.

    f. Haga clic en **Save**(Guardar).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Comm100 Live Chat, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Comm100 Live Chat y comience el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Comm100 Live Chat en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Comm100 Live Chat. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Comm100 Live Chat, puede aplicar el control de sesión, que protege a la organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).