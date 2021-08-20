---
title: 'Tutorial: Integración de Azure Active Directory con XaitPorter | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y XaitPorter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: 1acc14d2d776236914f6be8403e77ee70179629d
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111748257"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Tutorial: Integración de Azure Active Directory con XaitPorter

En este tutorial, aprenderá a integrar XaitPorter con Azure Active Directory (Azure AD). Al integrar XaitPorter con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a XaitPorter.
* Permitir que los usuarios inicien sesión automáticamente en XaitPorter con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con XaitPorter, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para inicio de sesión único en XaitPorter.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* XaitPorter admite el inicio de sesión único iniciado por **SP**.

## <a name="add-xaitporter-from-the-gallery"></a>Adición de XaitPorter desde la galería

Para configurar la integración de XaitPorter en Azure AD, debe agregar XaitPorter desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **XaitPorter** en el cuadro de búsqueda.
1. Seleccione **XaitPorter** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-xaitporter"></a>Configuración y prueba del SSO de Azure AD para XaitPorter

Configure y pruebe el inicio de sesión único de Azure AD con XaitPorter mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de XaitPorter.

Para configurar y probar el inicio de sesión único de Azure AD con XaitPorter, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en XaitPorter](#configure-xaitporter-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en XaitPorter](#create-xaitporter-test-user)** : para tener un homólogo de B.Simon en XaitPorter que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **XaitPorter**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.xaitporter.com`

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.xaitporter.com/saml/login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico de XaitPorter](https://www.xait.com/support/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

6. Proporcione la **dirección IP** o la **dirección URL de metadatos de federación de aplicación** al [equipo de soporte técnico de SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/), de modo que XaitPorter pueda garantizar que la dirección IP sea accesible desde la instancia de XaitPorter mediante su inclusión en la lista de aprobados. 

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

En esta sección, va a permitir que B.Simon acceda a XaitPorter mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **XaitPorter**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-xaitporter-sso"></a>Configuración del inicio de sesión único de XaitPorter

1. Para automatizar la configuración de XaitPorter, debe instalar la **extensión de explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Install the extension** (Instalar la extensión).

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar XaitPorter** para ir a la aplicación XaitPorter. Desde allí, proporcione las credenciales de administrador para iniciar sesión en XaitPorter. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 6.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar XaitPorter manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de XaitPorter como administrador y realice los pasos siguientes:

4. Haga clic en **Admin** (Administrador).

    ![Captura de pantalla que muestra la opción Admin (Administrador) seleccionada en el sitio de XaitPorter.](./media/xaitporter-tutorial/admin.png)

5. Seleccione **Manage Single Sign-On** (Administrar inicio de sesión único) desde la lista desplegable **System Setup** (Configuración del sistema).

    ![Captura de pantalla que muestra la opción Manage Single Sign-On (Administrar el inicio de sesión único [Single Sign-on, SSO]) seleccionada en System Setup (Configuración del sistema).](./media/xaitporter-tutorial/user.png)

6. En la sección **MANAGE SINGLE SIGN-ON** (Administrar inicio de sesión único), lleve a cabo los pasos siguientes:

    ![Captura de pantalla que muestra la sección MANAGE SINGLE SIGN-ON (ADMINISTRAR EL INICIO DE SESIÓN ÚNICO [SINGE SIGN-ON, SSO]), donde puede realizar estos pasos.](./media/xaitporter-tutorial/authentication.png)

    a. Seleccione **Enable Single Sign-On Authentication** (Habilitar autenticación de inicio de sesión único).

    b. En el cuadro de texto **Identity Provider Settings** (Configuración del proveedor de identidades), pegue la **dirección URL de metadatos de federación de aplicación** que ha copiado de Azure Portal y haga clic en **Fetch** (Recuperar).

    c. Seleccione **Enable Autocreation of Users** (Habilitar la creación automática de usuarios).

    d. Haga clic en **OK**.

### <a name="create-xaitporter-test-user"></a>Creación de un usuario de prueba de XaitPorter

En esta sección, creará un usuario llamado Britta Simon en XaitPorter. Trabaje con el [equipo de soporte técnico al cliente de XaitPorter](https://www.xait.com/support/) para agregar los usuarios a la plataforma de XaitPorter. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de XaitPorter, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de XaitPorter e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de XaitPorter en Mis aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado XaitPorter, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
