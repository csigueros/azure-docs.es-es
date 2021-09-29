---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con People | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y People.
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
ms.openlocfilehash: e8ff577be6299b65d6923cf26578c42ea35610e2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785978"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con People

En este tutorial, aprenderá a integrar People con Azure Active Directory (Azure AD). Al integrar People con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a People.
* Permitir que los usuarios inicien sesión automáticamente en People con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en People.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* People admite el inicio de sesión único iniciado por **SP**
* Ahora se puede configurar la aplicación People Mobile con Azure AD para habilitar el inicio de sesión único. En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

>[!NOTE]
>El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-people-from-the-gallery"></a>Agregar People desde la galería

Para configurar la integración de People en Azure AD, deberá agregar People desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **People** en el cuadro de búsqueda.
1. Seleccione **People** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-people"></a>Configuración y prueba del inicio de sesión único de Azure AD para Pluralsight

Configure y pruebe el inicio de sesión único de Azure AD con People mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de People.

Para configurar y probar el inicio de sesión único de Azure AD con People, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en People](#configure-people-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de People](#create-people-test-user)** : el objetivo es tener un homólogo de B. Simon en People que esté vinculado a la representación del usuario en Azure AD.
6. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **People**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.peoplehr.net`

    b. En el cuadro **Identificador**, escriba la dirección URL: `https://www.peoplehr.com`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de People](mailto:customerservices@peoplehr.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Configurar People**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B. Simon acceda a People mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **People**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-people-sso"></a>Configuración del inicio de sesión único de People

1. Para automatizar la configuración en People, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar People** para ir a la aplicación People. En ella, escriba las credenciales de administrador para iniciar sesión en People. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 6.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar People manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de People como administrador y haga lo siguiente:
   
4. En el menú de la izquierda, haga clic en **Configuración**.

    ![Captura de pantalla que muestra el menú de la izquierda con la opción Configuración seleccionada.](./media/people-tutorial/settings.png)

5. Haga clic en **Compañía**.

    ![Screenshot that shows "Company" (Empresa) seleccionada en el menú "Settings" (Configuración).](./media/people-tutorial/company.png)

6. En **Upload “Single Sign On” SAML meta-data file** (Cargar archivo de metadatos SAML de "Inicio de sesión único"), haga clic en **Browse** (Examinar) para cargar el archivo de metadatos descargado.

    ![Configurar inicio de sesión único](./media/people-tutorial/xml.png)

### <a name="create-people-test-user"></a>Creación de un usuario de prueba de People

En esta sección, creará un usuario llamado B. Simon en People. Colabore con el [equipo de atención al cliente de People](mailto:customerservices@peoplehr.com) para agregar los usuarios a la plataforma People. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de People, desde donde puede iniciar el flujo de inicio de sesión. 

* Acceda directamente a la URL de inicio de sesión de People y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de People en Aplicaciones, se le redirigirá a la URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="test-sso-for-people-mobile"></a>Prueba del inicio de sesión único para People (Mobile)

1. Abra la aplicación People Mobile. En la página de inicio de sesión, escriba el **identificador de correo electrónico** y, a continuación, haga clic en **Inicio de sesión único**.

    ![Inicio de sesión](./media/people-tutorial/test01.png)

2. Escriba el **identificador de usuario de la organización**  y haga clic en **Siguiente**.

    ![El correo electrónico](./media/people-tutorial/test02.png)

3. Finalmente, después de iniciar sesión correctamente, aparecerá la página principal de la aplicación que se muestra a continuación:

    ![Una vez](./media/people-tutorial/test03.png)

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado People, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).