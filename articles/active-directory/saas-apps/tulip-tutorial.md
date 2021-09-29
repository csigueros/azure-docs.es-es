---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Tulip | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tulip.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: jeedes
ms.openlocfilehash: 335f81f573aa76d287ca54d58390a31116fc2888
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124765125"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tulip"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Tulip

En este tutorial aprenderá a integrar Tulip con Azure Active Directory (Azure AD). Al integrar Tulip con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Tulip.
* Permitir que los usuarios inicien sesión automáticamente en Tulip con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Tulip.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.


* Tulip admite el inicio de sesión único iniciado por **IDP**.

## <a name="adding-tulip-from-the-gallery"></a>Adición de Tulip desde la galería

Para configurar la integración de Tulip en Azure AD, tendrá que agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Tulip** en el cuadro de búsqueda.
1. Seleccione **Tulip** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-tulip"></a>Configuración y prueba del inicio de sesión único de Azure AD para Tulip

Configure y pruebe el inicio de sesión único de Azure AD con Tulip mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tulip.

Para configurar y probar el inicio de sesión único de Azure AD con Tulip, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Tulip](#configure-tulip-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Tulip](#create-tulip-test-user)** : para tener un homólogo de B.Simon en Tulip que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Tulip**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si tiene el **archivo de metadatos del proveedor de servicios**, lleve a cabo los siguientes pasos:

    a. Haga clic en **Cargar el archivo de metadatos**.

    ![image1](common/upload-metadata.png)

    b. Haga clic en el **logotipo de la carpeta** para seleccionar el archivo de metadatos y luego en **Cargar**.

    ![imagen2](common/browse-upload-metadata.png)

    c. Una vez que se haya cargado correctamente el archivo de metadatos, los valores de **Identificador** y **URL de respuesta** se rellenan automáticamente en la sección Configuración básica de SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Si los valores **Identificador** y **Dirección URL de respuesta** no se rellenan automáticamente, hágalo manualmente según sus necesidades.

1. La aplicación Tulip espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Tulip espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre |Atributo de origen|
    | -------------- | --------- |
    | DisplayName | user.displayname |
    | emailAddress |user.mail |
    | badgeID | user.employeeid |
    | groups |user.groups |


1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Tulip**, copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon acceda a Tulip mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Tulip**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-tulip-sso"></a>Configuración del inicio de sesión único de Tulip

1. Inicie sesión en el sitio de Tulip como administrador.

1. Vaya a **Settings** -> **SAML** (Configuración > SAML) y siga estos pasos en la página siguiente.

    ![Captura de pantalla de la configuración de Tulip.](./media/tulip-tutorial/configuration.png)

    a. **Enable SAML Logins** (Habilitación de los inicios de sesión de SAML). 

    b. Haga clic en **metadata xml file** (Archivo XML de metadatos) para descargar el **archivo de metadatos del proveedor de servicios** y cárguelo en la sección **Configuración básica de SAML** de Azure Portal.

    c. En el cuadro de texto **SSO Login URL** (Dirección URL de inicio de sesión único), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    d. En el cuadro de texto **SSO Logout URL** (URL de cierre de sesión de SSO), pegue la **URL de cierre de sesión** que ha copiado de Azure Portal.

    e. Abra en el Bloc de notas el archivo del **certificado (Base64)** que ha descargado de Azure Portal y pegue el contenido en el cuadro de texto **Certificados**.

    f. En **Attribute Mapping** (Asignación de atributos),
    
     * asigne el valor **Name Attribute** (Atributo de nombre) como **displayName**.

     * asigne el valor **Email Attribute** (Atributo de correo electrónico) como **emailAdress**.

     * asigne el valor **Badge Attribute** (Atributo de distintivo) como **badgeID**.

     * asigne el valor **Role Attribute** (Atributo de rol) como **groups**.

    g. En **Role Mapping** (Asignación de roles), asigne el valor ObjectID del grupo con el propietario de la cuenta.

    h. Haga clic en **Save SAML Configuration** (Guardar configuración de SAML).

### <a name="create-tulip-test-user"></a>Creación del usuario de prueba de Tulip

En esta sección, creará un usuario llamado Britta Simon en Tulip. Trabaje con el [equipo de soporte técnico de Tulip](mailto:support@tulip.co) para agregar los usuarios a la plataforma de Tulip. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal y debería iniciar sesión automáticamente en la instancia de Tulip para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Tulip en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de Tulip para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Tulip, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).