---
title: 'Tutorial: Integración de Azure Active Directory con Jive | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 6825889ca8a9545148d98e58e687977ab6ec78ae
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734098"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Jive

En este tutorial, aprenderá a integrar Jive con Azure Active Directory (Azure AD). Al integrar Jive con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Jive.
* Permitir que los usuarios inicien sesión automáticamente en Jive con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Jive.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Jive admite SSO iniciado por **SP**.
* Jive admite el aprovisionamiento de usuarios [**automático**](jive-provisioning-tutorial.md).

## <a name="add-jive-from-the-gallery"></a>Incorporación de Jive desde la galería

Para configurar la integración de Jive en Azure AD, deberá agregar Jive desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Jive** en el cuadro de búsqueda.
1. Seleccione **Jive** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Configuración y prueba del inicio de sesión único de Azure AD para Jive

Configure y pruebe el inicio de sesión único de Azure AD con Jive mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Jive.

Para configurar y probar el inicio de sesión único de Azure AD con Jive, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Jive](#configure-jive-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Jive](#create-jive-test-user)** , para tener un homólogo de B.Simon en Jive vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Jive**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

   a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<instance name>.jivecustom.com`

   b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Jive](https://www.jivesoftware.com/services-support/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Set up Jive** (Configurar Jive), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección va a permitir que B.Simon acceda a Jive mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Jive**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-jive-sso"></a>Configuración del inicio de sesión único de Jive

1. Para configurar el inicio de sesión único en **Jive**, inicie sesión en su inquilino de Jive como administrador.

1. En el menú de la parte superior, haga clic en **SAML**.

    ![Captura de pantalla que muestra la pestaña SAML con la opción Enabled (Habilitado) seleccionado.](./media/jive-tutorial/jive-2.png)

    a. Seleccione **Habilitado** en la pestaña **General**.

    b. Haga clic en el botón **SAVE ALL SAML SETTINGS** (GUARDAR TODA LA CONFIGURACIÓN DE SAML).

1. Vaya a la pestaña **METADATOS DE IDP**.

    ![Captura de pantalla que muestra la pestaña SAML con la pestaña I D P METADATA (METADATOS DE IDP) seleccionada.](./media/jive-tutorial/jive-3.png)

    a. Copie el contenido del archivo XML de metadatos descargado y, después, péguelo en el cuadro de texto **Identity Provider (IDP) Metadata** (Metadatos del proveedor de identidades [IDP]).

    b. Haga clic en el botón **SAVE ALL SAML SETTINGS** (GUARDAR TODA LA CONFIGURACIÓN DE SAML).

1. Seleccione la pestaña **USER ATTRIBUTE MAPPING** (Asignación de atributos de usuario).

    ![Captura de pantalla que muestra la pestaña SAML con la pestaña USER ATTRIBUTE MAPPING (Asignación de atributos de usuario) seleccionada.](./media/jive-tutorial/jive-4.png)

    a. En el cuadro de texto **Email** (Correo electrónico), copie y pegue el nombre del atributo de valor **mail**.

    b. En el cuadro de texto **First Name** (Nombre), copie y pegue el nombre del atributo de valor **givenname**.

    c. En el cuadro de texto **Last Name** (Apellidos), copie y pegue el nombre del atributo de valor **surname**.

### <a name="create-jive-test-user"></a>Crear usuario de prueba de Jive

El objetivo de esta sección es crear un usuario llamado Britta Simon en Jive. Jive admite el aprovisionamiento automático de usuarios, que está habilitado de forma predeterminada. [Aquí](jive-provisioning-tutorial.md) puede encontrar más información sobre cómo configurar el aprovisionamiento automático de usuarios.

Si necesita crear un usuario manualmente, trabaje con el [equipo de soporte técnico del cliente de Jive](https://www.jivesoftware.com/services-support/) para agregar los usuarios a la plataforma de Jive.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Jive, donde puede iniciar el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de Jive e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Jive en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de dicha aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Jive, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).