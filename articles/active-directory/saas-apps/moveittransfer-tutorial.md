---
title: 'Tutorial: Integración de Azure Active Directory con MOVEit Transfer - Azure AD integration | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y MOVEit Transfer - Azure AD integration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 0db5d434e2442019fc441b5443c50df16bc4559e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742232"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integración de Azure Active Directory con MOVEit Transfer - Azure AD integration

En este tutorial, aprenderá a integrar MOVEit Transfer - Azure AD integration con Azure Active Directory. Al integrar MOVEit Transfer - Azure AD integration con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a MOVEit Transfer - Azure AD integration.
* Permitir que los usuarios inicien sesión automáticamente en MOVEit Transfer - Azure AD integration con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en MOVEit Transfer - Azure AD integration.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* MOVEit Transfer - Azure AD integration admite el inicio de sesión único por **SP**.

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Incorporación de MOVEit Transfer - Azure AD integration desde la galería

Para configurar la integración de MOVEit Transfer - Azure AD integration en Azure AD, deberá agregar MOVEit Transfer - Azure AD integration desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **MOVEit Transfer - Azure AD integration** en el cuadro de búsqueda.
1. Seleccione **MOVEit Transfer - Azure AD integration** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Configuración y prueba del inicio de sesión único de Azure AD para MOVEit Transfer - Azure AD integration

Configure y pruebe el inicio de sesión único de Azure AD con MOVEit Transfer - Azure AD integration mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de MOVEit Transfer - Azure AD integration.

Para configurar y probar el inicio de sesión único de Azure AD con MOVEit Transfer - Azure AD integration, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de MOVEit Transfer - Azure AD integration](#configure-moveit-transfer---azure-ad-integration-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en MOVEit Transfer - Azure AD integration](#create-moveit-transfer---azure-ad-integration-test-user)** : para tener un homólogo de B.Simon en MOVEit Transfer - Azure AD integration que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **MOVEit Transfer - Azure AD integration**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si tiene el **archivo de metadatos del proveedor de servicios**, lleve a cabo los siguientes pasos:

    a. Haga clic en **Cargar el archivo de metadatos**.

    ![Carga del archivo de metadatos](common/upload-metadata.png)

    b. Haga clic en el **logotipo de la carpeta** para seleccionar el archivo de metadatos y luego en **Cargar**.

    ![Elección del archivo de metadatos](common/browse-upload-metadata.png)

    c. Una vez que se haya cargado correctamente el archivo de metadatos, el valor de **Identificador** y **URL de respuesta** se rellena automáticamente en la sección **Configuración básica de SAML**:

    ![Información de dominio y direcciones URL de inicio de sesión único de MOVEit Transfer - Azure AD integration](common/sp-identifier-reply.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://contoso.com`

    > [!NOTE]
    > El valor de la **dirección URL de inicio de sesión** no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de MOVEit Transfer - Azure AD integration Client](https://community.ipswitch.com/s/support) para obtener el valor. Puede descargar el **archivo de metadatos del proveedor de servicios** desde la **dirección URL de metadatos del proveedor de servicios**, que se explica más adelante en la sección **Configuración del inicio de sesión único de MOVEit Transfer - Azure AD integration** del tutorial. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Set up MOVEit Transfer - Azure AD integration** (Configurar MOVEit Transfer - Azure AD integration), copie las siguientes direcciones URL según sus necesidades.

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

En esta sección, habilitará a B.Simon para que use el inicio de sesión único de Azure concediéndole acceso a MOVEit Transfer - Azure AD integration.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **MOVEit Transfer - Azure AD integration**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Configuración del inicio de sesión único de MOVEit Transfer - Azure AD integration

1. Inicie la sesión en el inquilino de MOVEit Transfer como administrador.

2. En la barra de navegación de la izquierda, haga clic en **Settings**(Configuración).

    ![Sección Configuración en la aplicación](./media/moveittransfer-tutorial/settings.png)

3. Haga clic en el vínculo **Single Signon** (Inicio de sesión único) que se encuentra en **Security Policies -> User Auth** (Directivas de seguridad -> Autenticación de usuario).

    ![Directivas de seguridad en la aplicación](./media/moveittransfer-tutorial/sso.png)

4. Haga clic en el vínculo Metadata URL (Dirección URL de metadatos) para descargar el documento de metadatos.

    ![Dirección URL de metadatos del proveedor de servicios](./media/moveittransfer-tutorial/metadata.png)
    
   * Compruebe que **entityID** coincide con el valor de **Identificador** en la sección **Configuración básica de SAML**.
   * Compruebe que la dirección URL de la ubicación de **AssertionConsumerService** coincide con el valor de **URL DE RESPUESTA** de la sección **Configuración básica de SAML**.
    
     ![Configuración del inicio de sesión único en la aplicación](./media/moveittransfer-tutorial/xml.png)

5. Haga clic en el botón **Add Identity Provider** (Agregar proveedor de identidades) para agregar un nuevo proveedor de identidad federada.

    ![Agregación del proveedor de identidades](./media/moveittransfer-tutorial/idp.png)

6. Haga clic en **Browse...** (Examinar...) para seleccionar el archivo de metadatos que descargó de Azure Portal y, después, haga clic en **Add Identity Provider** (Agregar proveedor de identidades) para cargar el archivo descargado.

    ![Proveedor de identidades de SAML](./media/moveittransfer-tutorial/saml.png)

7. Seleccione "**Yes**" (Sí) en la opción **Enabled** (Habilitado) de la página **Edit Federated Identity Provider Settings** (Editar configuración de proveedor de identidades federada) y haga clic en **Save** (Guardar).

    ![Configuración del proveedor de identidades federadas](./media/moveittransfer-tutorial/save.png)

8. En la página **Edit Federated Identity Provider Settings** (Editar configuración del proveedor de identidades federadas), realice las siguientes acciones:
    
    ![Edición de la configuración del proveedor de identidades federadas](./media/moveittransfer-tutorial/attributes.png)
    
    a. Seleccione **SAML NameID** en **Login name** (Nombre de inicio de sesión).
    
    b. Seleccione **Other** (Otros) como **Full name** (Nombre completo) y en el cuadro de texto **Attribute name** (Nombre de atributo) coloque el valor: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Seleccione **Other** (Otros) como **Email** (Correo electrónico) y en el cuadro de texto **Attribute name** (Nombre de atributo) coloque el valor: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Seleccione **Yes** (Sí) en **Auto-create account on signon** (Crear automáticamente cuenta al iniciar sesión).
    
    e. Haga clic en el botón **Guardar** .

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Creación de un usuario de prueba de MOVEit Transfer - Azure AD integration

El objetivo de esta sección es crear un usuario llamado Britta Simon en MOVEit Transfer - Azure AD integration. MOVEit Transfer - Azure AD integration admite el aprovisionamiento Just-In-Time que ha habilitado. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a MOVEit Transfer - Azure AD integration se creará un nuevo usuario, en caso de que no exista.

>[!NOTE]
>Si necesita crear un usuario manualmente, es preciso que se ponga en contacto con el [equipo de soporte técnico de MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de MOVEit Transfer - Azure AD integration, desde donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya a la URL de inicio de sesión de MOVEit Transfer - Azure AD integration directamente y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de MOVEit Transfer - Azure AD integration en Aplicaciones, debería iniciar sesión automáticamente en la instancia de MOVEit Transfer - Azure AD integration para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la integración de MOVEit Transfer - Azure AD integration, puede aplicar el control de sesión, que protege su organización, en tiempo real, frente a la filtración y la infiltración de la información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).