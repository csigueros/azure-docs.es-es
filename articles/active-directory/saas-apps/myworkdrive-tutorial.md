---
title: 'Tutorial: Integración de Azure Active Directory con MyWorkDrive | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y MyWorkDrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 1e3c8632428e6ba23e2b8e7149b57ef7cfe2d536
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284527"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Tutorial: Integración de MyWorkDrive con Azure Active Directory

En este tutorial, obtendrá información sobre cómo integrar MyWorkDrive con Azure Active Directory (Azure AD). Al integrar MyWorkDrive con Azure AD, puede hacer lo siguiente:

* Puede controlar en Azure AD quién tiene acceso a MyWorkDrive.
* Permitir que los usuarios puedan iniciar sesión automáticamente en MyWorkDrive con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único en MyWorkDrive.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. 
* MyWorkDrive admite el inicio de sesión único iniciado por **SP** e **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-myworkdrive-from-the-gallery"></a>Adición de MyWorkDrive desde la galería

Para configurar la integración de MyWorkDrive en Azure AD, deberá agregar MyWorkDrive desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **MyWorkDrive** en el cuadro de búsqueda.
1. Seleccione **MyWorkDrive** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-myworkdrive"></a>Configuración y prueba del inicio de sesión único de Azure AD en MyWorkDrive

Configure y pruebe el inicio de sesión único de Azure AD con MyWorkDrive con un usuario de prueba llamado **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de MyWorkDrive.

Para configurar y probar el inicio de sesión único de Azure AD con MyWorkDrive, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en MyWorkDrive](#configure-myworkdrive-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en MyWorkDrive](#create-myworkdrive-test-user)** : para tener un homólogo de B.Simon en MyWorkDrive que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **MyWorkDrive**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configuración básica de SAML**, si quiere configurar la aplicación en modo iniciado por **IDP**, siga estos pasos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Escriba el nombre de host de su propia compañía, por ejemplo, MyWorkDrive Server.
    > 
    > URL de respuesta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de inicio de sesión: `https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Póngase en contacto con el [equipo de soporte técnico de MyWorkDrive](mailto:support@myworkdrive.com) si no está seguro de cómo configurar su propio nombre de host y el certificado TLS/SSL para estos valores.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en el Portapapeles.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado Britta Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `Britta Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `BrittaSimon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a MyWorkDrive.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **MyWorkDrive**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En la lista de usuarios del cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-myworkdrive-sso"></a>Configuración del inicio de sesión único en MyWorkDrive

1. Para automatizar la configuración en MyWorkDrive, debe instalar **My Apps Secure Sign-in browser extension** (Extensión del explorador de inicio de sesión seguro de Aplicaciones). Para ello, haga clic en **Install the extension** (Instalar la extensión).

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

1. Después de agregar la extensión al explorador, haga clic en **Setup MyWorkDrive** (Configurar MyWorkDrive) para ir a la aplicación. En ella, escriba las credenciales de administrador para iniciar sesión en MyWorkDrive. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 y 4.

    ![Configuración](common/setup-sso.png)

1. Si desea configurar MyWorkDrive manualmente, en otra ventana del explorador web, inicie sesión en la aplicación como administrador de seguridad.

1. En el servidor de MyWorkDrive en el panel de administración, haga clic en **ENTERPRISE** y realice los pasos siguientes:

    ![El administrador](./media/myworkdrive-tutorial/admin.png)

    a. Habilite **SSO de SAML/ADFS**.

    b. Seleccione **SAML - Azure AD**.

    c. En el cuadro de texto **Azure App Federation Metadata Url** (Dirección URL de metadatos de federación de aplicación de Azure), pegue el valor de la **dirección URL de metadatos de federación de aplicación** que ha copiado de Azure Portal.

    d. Haga clic en **Save**(Guardar).

    > [!NOTE]
    > Para obtener más información, consulte el [artículo de soporte técnico de Azure AD de MyWorkDrive](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-myworkdrive-test-user"></a>Creación de un usuario de prueba de MyWorkDrive

En esta sección, creará un usuario llamado Britta Simon en MyWorkDrive. Trabaje con el [equipo de soporte técnico de MyWorkDrive](mailto:support@myworkdrive.com) para agregar los usuarios a la plataforma de MyWorkDrive. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de MyWorkDrive, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de MyWorkDrive y comience el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; iniciará sesión automáticamente en la instancia de MyWorkDrive para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de MyWorkDrive en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, iniciará sesión automáticamente en la instancia de MyWorkDrive para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que ha configurado MyWorkDrive, puede aplicar el control de sesión, que protege a la organización en tiempo real contra la filtración y la infiltración de la información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
