---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Velpic SAML | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Velpic SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 45917f0bda43b44b53f73a8d38d9a59e277654c4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812767"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Velpic SAML

En este tutorial, aprenderá a integrar Velpic SAML con Azure Active Directory (Azure AD). Al integrar Velpic SAML con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Velpic SAML.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Velpic SAML (SAML) con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Velpic SAML.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Velpic SAML admite el inicio de sesión único iniciado por **SP**.
* Velpic admite el [aprovisionamiento de usuarios automatizado](velpic-provisioning-tutorial.md).

## <a name="adding-velpic-saml-from-the-gallery"></a>Agregar Velpic SAML desde la galería

Para configurar la integración de Velpic SAML en Azure AD, es preciso agregar Velpic SAML desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Velpic SAML** en el cuadro de búsqueda.
1. Seleccione **Velpic SAML** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.    

## <a name="configure-and-test-azure-ad-sso-for-velpic-saml"></a>Configuración y prueba del inicio de sesión único de Azure AD en Velpic SAML

Configure y pruebe el inicio de sesión único de Azure AD con Velpic SAML con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Velpic SAML.

Para configurar y probar el inicio de sesión único de Azure AD con Velpic SAML, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Velpic SAML](#configure-velpic-saml-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Velpic SAML](#create-velpic-saml-test-user)** : para tener un homólogo de B.Simon en Velpic SAML que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Velpic SAML**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<sub-domain>.velpicsaml.net`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Tenga en cuenta que la URL de inicio de sesión se la proporcionará el equipo de Velpic SAML y el valor del identificador estará disponible cuando configure el complemento de SSO en Velpic SAML. Tiene que copiar ese valor de la página de la aplicación Velpic SAML y pegarlo aquí.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configuración de Velpic SAML**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Velpic SAML mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Velpic SAML**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-velpic-saml-sso"></a>Configuración del inicio de sesión único de Velpic SAML

1. Para automatizar la configuración en Velpic SAML, debe instalar la **extensión del explorador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar Velpic SAML** para ir a la aplicación Velpic SAML. Desde ahí, proporcione las credenciales de administrador para iniciar sesión en esta aplicación. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 8.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Velpic SAML manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Velpic SAML como administrador y haga lo siguiente:

4. Haga clic en la pestaña **Manage** (Administrar) y vaya a la sección **Integration** (Integración), donde tiene que hacer clic en el botón **Plugins** (Complementos) para crear el complemento Sign-In (Inicio de sesión).

    ![Captura de pantalla que muestra la página de integración, en la que se pueden seleccionar complementos.](./media/velpicsaml-tutorial/plugin.png)

5. Haga clic en el botón **Add plugin** (Agregar complemento).
    
    ![Captura de pantalla que muestra el botón Add plugin (Agregar complemento) seleccionado.](./media/velpicsaml-tutorial/add-button.png)

6. Haga clic en el icono **SAML** de la página Add Plugin (Agregar complemento).
    
    ![Captura de pantalla que muestra la opción SAML seleccionada en la página Add plugin (Agregar complemento).](./media/velpicsaml-tutorial/integration.png)

7. Escriba el nombre del nuevo complemento SAML y haga clic en el botón **Add** (Agregar).

    ![Captura de pantalla que muestra el cuadro de diálogo Add new SAML plugin (Agregar nuevo complemento SAML), con Azure AD especificado.](./media/velpicsaml-tutorial/new-plugin.png)

8. Especifique los detalles de la manera siguiente:

    ![Captura de pantalla que muestra la página de Azure AD,en la que puede especificar los valores descritos.](./media/velpicsaml-tutorial/details.png)

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre del complemento SAML.

    b. En el cuadro de texto **Issuer URL** (URL del emisor), pegue el **Id. de Azure AD** que copió de la ventana **Configurar inicio de sesión** de Azure Portal.

    c. En **Provider Metadata Config** (Config. de metadatos del proveedor), cargue el archivo XML de metadatos que descargó de Azure Portal.

    d. También puede habilitar aprovisionamiento Just-In-Time de SAML activando la casilla **Auto create new users** (Crear nuevos usuarios automáticamente). Si no existe ningún usuario de Velpic y no se habilita esta marca, el inicio desde Azure no se producirá. Si se habilita la marca, el usuario se aprovisionará automáticamente en Velpic en el momento de inicio de sesión. 

    e. Copie la **Single sign on URL** (URL de inicio de sesión) en el cuadro de texto y péguela en Azure Portal.
    
    f. Haga clic en **Save**(Guardar).

### <a name="create-velpic-saml-test-user"></a>Creación de un usuario de prueba de Velpic SAML

Este paso no suele ser necesario porque la aplicación admite aprovisionamiento de usuarios Just-In-Time. Si el aprovisionamiento automático de usuarios no se habilita, puede llevarse a cabo la creación manual de usuarios tal y como se describe a continuación.

Inicie sesión como administrador en su sitio de la compañía de Velpic SAML y realice los pasos siguientes:
    
1. Haga clic en la pestaña Manage (Administrar), vaya a la sección Users (Usuarios) y haga clic en el botón New (Nuevo) para agregar usuarios.

    ![Agregar usuario](./media/velpicsaml-tutorial/new-user.png)

2. En el cuadro de diálogo **"Create New User"** (Crear nuevo usuario), realice los pasos siguientes.

    ![Usuario](./media/velpicsaml-tutorial/create-user.png)
    
    a. En el cuadro de texto **First Name** (Nombre), escriba el nombre de B.

    b. En el cuadro de texto **Apellido**, escriba el apellido Simon.

    c. En el cuadro de texto **User Name** (Nombre de usuario), escriba el nombre del usuario de B.Simon.

    d. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de la cuenta B.Simon@contoso.com.

    e. El resto de la información es opcional, puede rellenarla si es necesario.
    
    f. Haga clic en **GUARDAR**.

> [!NOTE]
> Velpic SAML también admite el aprovisionamiento automático de usuarios. [Aquí](./velpic-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurarlo.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante Mis aplicaciones.

1. Al hacer clic en el icono de Velpic SAML en Aplicaciones, debería entrar en la página de inicio de sesión de dicha aplicación. En esta página, debería ver el botón **Log In With Azure AD** (Iniciar sesión con Azure AD).

    ![Captura de pantalla que muestra el portal de aprendizaje, con la opción Log In With Azure AD (Iniciar sesión con Azure AD) seleccionada.](./media/velpicsaml-tutorial/login.png)

1. Haga clic en el botón **Log In With Azure AD** (Iniciar sesión con Azure AD) para iniciar sesión en Velpic con su cuenta de Azure AD.

## <a name="next-steps"></a>Pasos siguientes

Una vez que ha configurado Velpic SAML, puede aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).