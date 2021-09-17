---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Adobe Identity Management | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 0b5d0e406186156e1983f0008b973aa0448092d1
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181222"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Adobe Identity Management

En este tutorial, aprenderá a integrar Adobe Identity Management con Azure Active Directory (Azure AD). Al integrar Adobe Identity Management con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Adobe Identity Management.
* Permitir que los usuarios inicien sesión automáticamente en Adobe Identity Management con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción a Adobe Identity Management con el inicio de sesión único habilitado.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Adobe Identity Management admite el inicio de sesión único (SSO) iniciado por **SP**.
* Adobe Identity Management admite el [aprovisionamiento y desaprovisionamiento **automático** de usuarios](adobe-identity-management-provisioning-tutorial.md) (se recomienda).

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Adición de Adobe Identity Management desde la galería

Para configurar la integración de Adobe Identity Management en Azure AD, es preciso agregar Adobe Identity Management desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Adobe Identity Management** en el cuadro de búsqueda.
1. Seleccione **Adobe Identity Management** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Configuración y prueba del inicio de sesión único de Azure AD para Adobe Identity Management

Configure y pruebe el inicio de sesión único de Azure AD con Adobe Identity Management con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Adobe Identity Management.

Para configurar y probar el inicio de sesión único de Azure AD con Adobe Identity Management, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Adobe Identity Management](#configure-adobe-identity-management-sso)** , para configurar los valores del inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Adobe Identity Management](#create-adobe-identity-management-test-user)** : para tener un homólogo de Britta Simon en Adobe Identity Management que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Adobe Identity Management**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL: `https://adobe.com`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > El valor del identificador no es real. Actualícelo con el identificador real. Póngase en contacto con el [equipo de soporte técnico de Adobe Identity Management](mailto:identity@adobe.com) para obtener el valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar Adobe Identity Management**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Adobe Identity Management mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Adobe Identity Management**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-adobe-identity-management-sso"></a>Configuración del inicio de sesión único de Adobe Identity Management

1. Para automatizar la configuración en Adobe Identity Management, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, al hacer clic en **Configurar Adobe Identity Management** se le dirigirá a la aplicación Adobe Identity Management. Desde allí, proporcione las credenciales de administrador para iniciar sesión en Adobe Identity Management. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 8.

    ![Configuración](common/setup-sso.png)

3. Si desea configurar Adobe Identity Management manualmente, en otra ventana del explorador web, inicie sesión en el sitio de Adobe Identity Management como administrador.

4. Vaya a la pestaña **Configuración** y haga clic en **Crear directorio**.

    ![Configuración de Adobe Identity Management](./media/adobe-identity-management-tutorial/settings.png)

5. Asigne el nombre del directorio que aparece en el cuadro de texto, seleccione **Id. federado** y haga clic en **Siguiente**.

    ![Creación del directorio de Adobe Identity Management](./media/adobe-identity-management-tutorial/create-directory.png)

6. Seleccione **Otros proveedores de SAML** y haga clic en **Siguiente**.
 
    ![Proveedores de SAML de Adobe Identity Manager](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Haga clic en **Seleccionar** para cargar el archivo **XML de metadatos** que descargó de Azure Portal.

    ![Configuración de SAML de Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Haga clic en **Done** (Acabado).

### <a name="create-adobe-identity-management-test-user"></a>Creación de un usuario de prueba de Adobe Identity Management

1. Vaya a la pestaña **Usuarios** y haga clic en **Agregar usuario**.

    ![Agregar usuario en Adobe Identity Management](./media/adobe-identity-management-tutorial/add-user.png)

2. En el cuadro de texto **Enter user’s email address** (Especificar la dirección de correo electrónico del usuario), indique la **dirección de correo electrónico**.

    ![Guardar el usuario de Adobe Identity Management](./media/adobe-identity-management-tutorial/save-user.png)

3. Haga clic en **Save**(Guardar).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Adobe Identity Management, desde donde puede poner en marcha el flujo de inicio de sesión.

* Vaya directamente a la URL de inicio de sesión de Adobe Identity Management y comience el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Adobe Identity Management en Mis aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Adobe Identity Management. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que se haya configurado Adobe Identity Management, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).