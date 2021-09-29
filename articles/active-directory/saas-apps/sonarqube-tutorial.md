---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Sonarqube | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Sonarqube.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/25/2021
ms.author: jeedes
ms.openlocfilehash: e6f366fcc1f268f3c0fa629bd1903be4f8c8d2b8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785807"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Sonarqube

En este tutorial, aprenderá a integrar Sonarqube con Azure Active Directory (Azure AD). Al integrar Sonarqube con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Sonarqube.
* Permitir que los usuarios inicien sesión automáticamente en Sonarqube con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Sonarqube.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Sonarqube admite el inicio de sesión único iniciado por **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-sonarqube-from-the-gallery"></a>Adición de Sonarqube desde la galería

Para configurar la integración de Sonarqube en Azure AD, deberá agregar Sonarqube desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Sonarqube** en el cuadro de búsqueda.
1. Seleccione **Sonarqube** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Configuración y prueba de inicio de sesión único de Azure AD para Sonarqube

Configure y pruebe el inicio de sesión único de Azure AD con Sonarqube mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Sonarqube.

Para configurar y probar el inicio de sesión único de Azure AD con Sonarqube, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Sonarqube](#configure-sonarqube-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Sonarqube](#create-sonarqube-test-user)** , para tener un homólogo de B.Simon en Salesforce que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Sonarqube**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://servicessonar.<YOUR_ORGANIZATION>.com`

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una de las siguientes direcciones URL:

    * **Para un entorno de producción**

        `https://servicessonar.corp.microsoft.com/`

    * **Para un entorno de desarrollo**

        `https://servicescode-dev.westus.cloudapp.azure.com`

    > [!NOTE]
    > Este valor no es real. Actualice el valor con la dirección URL de respuesta real, que se explica más adelante en el tutorial.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Sonarqube**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Sonarqube mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Sonarqube**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sonarqube-sso"></a>Configuración del inicio de sesión único en Sonarqube

1. Abra una nueva ventana del explorador web e inicie sesión en el sitio de la compañía Sonarqube como administrador.

1. Haga clic en **Administration > Configuration > Security** (Administración > Configuración > Seguridad), vaya al complemento **SAML** y complete los siguientes pasos.

1. Copie los siguientes detalles de los metadatos de IdP y péguelos en los campos de texto correspondientes en el complemento Sonarqube.
    1. IdP Entity ID (Identificador de entidad de IdP)
    2. URL de inicio de sesión
    3. Certificado X.509 

1. Guarde todos los detalles.

    ![IDP del complemento SAML](./media/sonarqube-tutorial/metadata.png)

1. En la página **SAML** , realice los siguientes pasos:

    ![Configuración de Sonarqube](./media/sonarqube-tutorial/configuration.png)

    a. Cambie la opción **Enabled** (Habilitado) a **yes** (sí).

    b. En el cuadro de texto **Application ID** (Id. de la aplicación), escriba el nombre de la siguiente manera: **sonarqube**.

    c. En el cuadro de texto **Provider Name** (Nombre del proveedor), escriba el nombre de la siguiente manera: **SAML**.

    d. En el cuadro de texto **Provider ID** (Id. de proveedor), pegue el valor de **Identificador Azure AD** que ha copiado de Azure Portal.

    e. En el cuadro de texto **SAML login url** (Dirección URL de inicio de sesión de SAML), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    f. Abra el certificado codificado en Base64 con el Bloc de notas, copie su contenido y péguelo en el cuadro de texto **Provider certificate** (Certificado del proveedor).

    g. En el cuadro de texto **SAML user login attribute** (Atributo de inicio de sesión de usuario de SAML), escriba el valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    h. En el cuadro de texto **SAML user name attribute** (Atributo de nombre de usuario de SAML), escriba el valor `http://schemas.microsoft.com/identity/claims/displayname`.

    i. En el cuadro de texto **SAML user email attribute** (Atributo de correo electrónico de usuario de SAML), escriba el valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    j. Haga clic en **Save**(Guardar).

### <a name="create-sonarqube-test-user"></a>Creación de un usuario de prueba de Sonarqube

En esta sección, creará un usuario llamado B.Simon en Sonarqube. Trabaje con el [equipo de soporte técnico al cliente de Sonarqube](https://www.sonarsource.com/support/) para agregar los usuarios a la plataforma de Sonarqube. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Sonarqube, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Sonarqube e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Sonarqube en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Sonarqube. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

* Una vez configurado Sonarqube, puede aplicar controles de sesión que protegen la filtración y la infiltración de la información confidencial de la organización en tiempo real. Los controles de sesión proceden del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).