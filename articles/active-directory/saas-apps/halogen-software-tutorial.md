---
title: 'Tutorial: Integración de Azure Active Directory con Saba TalentSpace | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: b6e63743de3cbeeaa1f65b2d00750baaf6f58b95
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739768"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Saba TalentSpace

En este tutorial aprenderá a integrar Saba TalentSpace con Azure Active Directory (Azure AD). Al integrar Saba TalentSpace con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Saba TalentSpace.
* Permitir que los usuarios inicien sesión automáticamente en Saba TalentSpace con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Saba TalentSpace.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Saba TalentSpace admite el inicio de sesión único iniciado por **SP**

## <a name="add-saba-talentspace-from-the-gallery"></a>Adición de Saba TalentSpace desde la galería

Para configurar la integración de Saba TalentSpace en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Saba TalentSpace** en el cuadro de búsqueda.
1. Seleccione **Saba TalentSpace** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-saba-talentspace"></a>Configuración y prueba del inicio de sesión único de Azure AD para Saba TalentSpace

Configure y pruebe el inicio de sesión único de Azure AD con Saba TalentSpace mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Saba TalentSpace.

Para configurar y probar el inicio de sesión único de Azure AD con Saba TalentSpace, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Saba TalentSpace](#configure-saba-talentspace-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba en Saba TalentSpace](#create-saba-talentspace-test-user)** : para tener un homólogo de B.Simon en Saba TalentSpace vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación de **Saba TalentSpace**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL con el siguiente patrón: `https://global.hgncloud.com/[companyname]/saml/login`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba la dirección URL con el siguiente patrón: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. En el cuadro de texto **URL de respuesta (URL del Servicio de consumidor de aserciones)** , escriba la dirección URL con el siguiente patrón: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de Saba TalentSpace](https://support.saba.com/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Saba TalentSpace** (Configurar Saba TalentSpace), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Saba TalentSpace mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Saba TalentSpace**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-saba-talentspace-sso"></a>Configuración del inicio de sesión único en Saba TalentSpace

1. En una ventana de explorador diferente, inicie sesión en su aplicación **Saba TalentSpace** como administrador.

2. Haga clic en la pestaña **Opciones** .
  
    ![Captura de pantalla que muestra la página principal de "Saba TalentSpace" con la pestaña "Options" (Opciones) seleccionada.](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. En el panel de navegación izquierdo, haga clic en **Configuración de SAML**.
  
    ![Captura de pantalla que muestra el panel de navegación izquierdo "User Interface" (Interfaz de usuario) con la opción "S A M L Configuration" (Configuración de S A M L) seleccionada.](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. En la página **Configuración de SAML** , realice los siguientes pasos:

    ![Captura de pantalla que muestra la página "S A M L Configuration" (Configuración de S A M L) con la opción "Settings" (Configuración) resaltada.](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. En **Identificador único**, seleccione **NameID**.

    b. En **El identificador único se asigna a**, seleccione **Nombre de usuario**.
  
    c. Para cargar el archivo de metadatos descargado, haga clic en **Examinar** para seleccionar el archivo y en **Cargar archivo**.

    d. Para probar la configuración, haga clic en **Ejecutar prueba**.

    > [!NOTE]
    > Deberá esperar a que aparezca el mensaje "*La prueba de SAML está completa. Cierre esta ventana* ". A continuación, cierre la ventana del explorador abierta. La casilla de verificación **Habilitar SAML** solo está habilitada si se ha completado la prueba.

    e. Seleccione **Habilitar SAML**.

    f. Haga clic en **Guardar cambios**.

### <a name="create-saba-talentspace-test-user"></a>Creación de un usuario de prueba en Saba TalentSpace

El objetivo de esta sección es crear un usuario de prueba llamado B.Simon en Saba TalentSpace.

**Para crear un usuario llamado Britta Simon en Saba TalentSpace, realice los pasos siguientes:**

1. Inicie sesión en la aplicación **Saba TalentSpace** como administrador.

2. Haga clic en la pestaña **Centro de usuarios** y en **Crear usuario**.

    ![Captura de pantalla que muestra la pestaña "User Center" (Centro de usuarios) y la opción "Create User" (Crear usuario) seleccionada.](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. En la página del cuadro de diálogo **Nuevo usuario** , realice los pasos siguientes:

    ![Qué es Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso, **B**.

    b. En el cuadro de texto **Apellidos**, escriba el apellido del usuario, en este caso **Simon**.

    c. En el cuadro de texto **Nombre de usuario**, escriba el nombre de usuario **B.Simon** como en Azure Portal.

    d. En el cuadro de texto **Contraseña**, escriba una contraseña para B.Simon.

    e. Haga clic en **Save**(Guardar).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Saba TalentSpace, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Saba TalentSpace e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Saba TalentSpace en Aplicaciones, debería iniciar sesión automáticamente en la aplicación Saba TalentSpace para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

 Una vez configurado Saba TalentSpace, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).