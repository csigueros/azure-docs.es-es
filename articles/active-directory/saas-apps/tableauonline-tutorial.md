---
title: 'Tutorial: Integración de Azure Active Directory con Tableau Online | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tableau Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: cb3efdc1bc03535abcf1f50ed4cd7a5c847ed957
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Tableau Online

En este tutorial, aprenderá a integrar Tableau Online con Azure Active Directory (Azure AD). Al integrar Tableau Online con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Tableau Online.
* Permitir que los usuarios inicien sesión automáticamente en Tableau Online con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Tableau Online.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Tableau Online admite SSO iniciado por **SP**
* Tableau Online admite el [aprovisionamiento y desaprovisionamiento **automático** de usuarios](tableau-online-provisioning-tutorial.md) (se recomienda).

## <a name="adding-tableau-online-from-the-gallery"></a>Incorporación de Tableau Online desde la galería

Para configurar la integración de Tableau Online en Azure AD, será preciso que agregue Tableau Online desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Tableau Online** en el cuadro de búsqueda.
1. Seleccione **Tableau Online** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Configuración y prueba del inicio de sesión único de Azure AD para Tableau Online

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Tableau Online con un usuario de prueba llamado **Britta Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tableau Online.

Para configurar el inicio de sesión único de Azure AD con Tableau Online, realice los pasos siguientes:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Tableau Online](#configure-tableau-online-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Tableau Online](#create-tableau-online-test-user)** , para tener un homólogo de B.Simon en Tableau Online que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Tableau Server**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba la dirección URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`.

    > [!NOTE]
    > El valor `<entityid>` lo obtendrá en la sección **Configuración de Tableau Online** de este tutorial. El valor del Id. de entidad será el valor de **Identificador de Azure AD** de la sección **Configuración de Tableau Online**.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Set up Tableau Online** (Configurar Tableau Online), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Tableau Server mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Tableau Online**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-tableau-online-sso"></a>Configuración del inicio de sesión único de Tableau Online

1. Para automatizar la configuración en Tableau Online, debe instalar la **extensión del navegador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instale la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Setup Tableau Online** (Configurar Tableau Online) para ir a la aplicación del mismo nombre. Desde allí, proporcione las credenciales de administrador para iniciar sesión en Tableau Online. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 7.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Tableau Online manualmente, en otra ventana del explorador web, inicie sesión en el sitio de la empresa Tableau Online como administrador.

1. Vaya a **Settings** (Configuración) y luego a **Authentication** (Autenticación).

    ![Captura de pantalla que muestra la opción Authentication (Autenticación) seleccionada en el menú de configuración.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Para habilitar SAML, en la sección **Authentication types** (Tipos de autenticación) seleccione **Enable an additional authentication method** (Habilitar un método de autenticación adicional) y, después, seleccione la casilla **SAML**.

    ![Captura de pantalla que muestra la sección Tipos de autenticación, en la que puede seleccionar valores.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Desplácese hacia abajo hasta la sección **Import metadata file into Tableau Online** (Importar archivo de metadatos en Tableau Online).  Haga clic en Examinar e importe el archivo de metadatos que ha descargado desde Azure AD. A continuación, haga clic en **Aplicar**.

   ![Captura de pantalla que muestra la sección en la que puede importar el archivo de metadatos.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. En la sección **Match assertions** (Aserciones de coincidencia), inserte el nombre de aserción del proveedor de identidades correspondiente para la **dirección de correo electrónico**, el **nombre** y los **apellidos**. Para obtener esta información a partir de Azure AD: 
  
    a. Vaya a la página de integración de aplicaciones de **Tableau Online** de Azure Portal.

    b. En la sección **User Attributes & Claims** (Atributos de usuario y notificaciones), haga clic en el icono de edición.

   ![Captura de pantalla que muestra la sección de atributos y notificaciones del usuario, desde donde puede seleccionar el icono de edición.](./media/tableauonline-tutorial/attributesection.png)

    c. Copie el valor del espacio de nombres de estos atributos: givenname, email y surname mediante los pasos siguientes:

   ![Captura de pantalla que muestra los atributos Givenname, Surname y Emailaddress.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Haga clic en el valor **user.givenname**

    e. Copie el valor del cuadro de texto **Namespace** (Espacio de nombres).

    ![Captura de pantalla que muestra la sección Administrar las notificaciones del usuario, en la que puede especificar el espacio de nombres.](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar los valores del espacio de nombres del correo electrónico y el apellido, repita los pasos anteriores.

    g. Abra la aplicación Tableau Online y en la sección **Tableau Online Attributes** (Atributos de Tableau Online) seleccione las siguientes opciones:

    * Correo electrónico: **mail** o **userprincipalname**

    * Nombre: **givenname**

    * Apellidos: **surname**

    ![Captura de pantalla que muestra la sección Match attributes (Coincidencia de atributos), en la que puede especificar los valores.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Creación de un usuario de prueba en Tableau Online

En esta sección, creará una usuaria llamada Britta Simon en Tableau Online.

1. En **Tableau Online**, haga clic en **Settings** (Configuración) y en la sección **Authentication** (Autenticación). Desplácese hacia abajo hasta la sección **Manage Users** (Administrar usuarios). Haga clic en **Add users** (Agregar usuarios) y, después, en **Enter Email Addresses** (Especificar direcciones de correo electrónico).
  
    ![Captura de pantalla que muestra la sección Administrar usuarios, en la que puede seleccionar Agregar usuarios.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Seleccione **Add users for (SAML) authentication** [Agregar usuarios para la autenticación (SAML)]. En el cuadro de texto **Especificar direcciones de correo electrónico**, agregue britta.simon\@contoso.com.
  
    ![Captura de pantalla que muestra la página Agregar usuarios, en la que puede especificar una dirección de correo electrónico.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Haga clic en **Agregar usuarios**.

### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Tableau Online donde podrá iniciar el flujo de inicio de sesión.

* Vaya directamente a la dirección URL de inicio de sesión de Tableau Online e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Tableau Online en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Tableau Online. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurada Tableau Online, puede aplicar el control de sesión, que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).