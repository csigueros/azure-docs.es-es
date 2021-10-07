---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Adobe Sign | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 62791850857770cba44eabf54719176e80cea48b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803387"
---
# <a name="tutorial-azure-ad-sso-integration-with-adobe-sign"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Adobe Sign

En este tutorial, obtendrá información sobre cómo integrar Adobe Sign con Azure Active Directory (Azure AD). Al integrar Adobe Sign con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Adobe Sign.
* Permitir que los usuarios inicien sesión automáticamente en Adobe Sign con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:
 
* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Adobe Sign.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Adobe Sign admite el inicio de sesión único iniciado por **SP**.

## <a name="add-adobe-sign-from-the-gallery"></a>Adición de Adobe Sign desde la galería

Para configurar la integración de Adobe Sign en Azure AD, será preciso que agregue Adobe Sign desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Adobe Sign** en el cuadro de búsqueda.
1. Seleccione **Adobe Sign** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Configuración y prueba del inicio de sesión único de Azure AD para Adobe Sign

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Adobe Sign con un usuario de prueba llamado **Britta Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Adobe Sign.

Para configurar y probar el inicio de sesión único de Azure AD con Adobe Sign, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Adobe Sign](#configure-adobe-sign-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Adobe Sign](#create-adobe-sign-test-user)** : para tener un homólogo de Britta Simon en Adobe Sign que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal.

Para configurar el inicio de sesión único de Azure AD con Adobe Sign, realice los pasos siguientes:

1. En Azure Portal, en la página de integración de la aplicación **Adobe Sign**, seleccione **Inicio de sesión único**.

1. En el cuadro de diálogo **Seleccionar un método de inicio de sesión único**, seleccione el modo **SAML/WS-Fed** para habilitar el inicio de sesión único.

1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono del lapicero para abrir el cuadro de diálogo **Configuración básica de SAML**.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.echosign.com/`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte al cliente de Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Configurar Adobe Sign**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Adobe Sign mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Adobe Sign**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-adobe-sign-sso"></a>Configuración del inicio de sesión único en Adobe Sign

1. Antes de la configuración, póngase en contacto con el [equipo de soporte técnico al cliente de Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para incluir su dominio en la lista de permitidos de Adobe Sign. Aquí se muestra cómo agregar el dominio:

    a. El [equipo de soporte técnico al cliente de Adobe Sign](https://helpx.adobe.com/in/contact/support.html) le envía un token generado aleatoriamente. Para el dominio, el token será similar a: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publique el token de comprobación en un registro de texto DNS y notifique al [equipo de soporte técnico al cliente de Adobe Sign](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Esto puede tardar unos cuantos días, o más. Tenga en cuenta que los retrasos de propagación de DNS significan que un valor que se publica en DNS puede no estar visible durante una hora o más. Su administrador de TI debe tener conocimientos sobre cómo publicar este token en un registro de texto DNS.

    c. Cuando notifique al [equipo de soporte técnico al cliente de Adobe Sign](https://helpx.adobe.com/in/contact/support.html) a través del vale de soporte, después de publicado el token, se valida el dominio y se agrega a su cuenta.

    d. Por lo general, estos son los pasos necesarios para publicar el token en un registro DNS:

    * Inicie sesión en su cuenta de dominio
    * Busque la página para actualizar el registro de DNS. Esta página se puede llamar Administración de DNS, Administración del servidor de nombres o Configuración avanzada.
    * Busque los registros TXT para el dominio.
    * Agregue un registro TXT con el valor completo del token proporcionado por Adobe.
    * Guarde los cambios.

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Adobe Sign como administrador.

1. En el menú SAML, seleccione **Configuración de cuenta** > **Configuración de SAML**.

    ![Captura de pantalla de la página Adobe Sign SAML Settings (Configuración de SAML de Adobe Sign).](./media/adobe-echosign-tutorial/settings.png "Cuenta")

1. En la sección **SAML Settings** (Configuración de SAML), realice los pasos siguientes:

    ![Captura de pantalla que resalta la configuración de SAML, incluido SAML Mandatory (SAML obligatorio).](./media/adobe-echosign-tutorial/profile.png "Configuración de SAML")

   ![Captura de pantalla de SAML Settings (Configuración de SAML).](./media/adobe-echosign-tutorial/certificate.png "Configuración de SAML")

   a. En **SAML Mode** (Modo de SAML), seleccione **SAML Mandatory** (SAML obligatorio).

   b. Seleccione **Allow EchoSign Account Administrators to log in using their EchoSign Credentials**(Permitir a los administradores de cuentas de EchoSign iniciar sesión con sus credenciales de EchoSign).

   c. En **User Creation** (Creación de usuario), seleccione **Automatically add users authenticated through SAML** (Agregar automáticamente usuarios autenticados a través de SAML).

   d. Pegue el **Identificador de Azure AD** que copió de Azure Portal en el cuadro de texto **Idp Entity ID** (Identificador de la entidad de IdP).

   e. Pegue la **URL de inicio de sesión** que ha copiado de Azure Portal en el cuadro de texto **Idp Login URL** (URL de inicio de sesión de IdP).

   f. Pegue la **URL de cierre de sesión** que ha copiado de Azure Portal en el cuadro de texto **Idp Logout URL** (URL de cierre de sesión de IdP).

   g. Abra el archivo **Certificado (Base64)** en el Bloc de notas. Copie el contenido del mismo en el Portapapeles y, después, péguelo en el cuadro de texto **IdP Certificate** (Certificado IDP).

   h. Seleccione **Save changes** (Guardar los cambios).

### <a name="create-adobe-sign-test-user"></a>Creación de un usuario de prueba de Adobe Sign

Para permitir que los usuarios de Azure AD inicien sesión en Adobe Sign, tienen que aprovisionarse en esta aplicación. Se trata de una tarea manual.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Adobe Sign que esta le ofrezca para aprovisionar cuentas de usuario de Azure AD. 

1. Inicie sesión en el sitio de la compañía de **Adobe Sign** como administrador.

2. En el menú de la parte superior, seleccione **Account** (Cuenta). A continuación, en el panel izquierdo, seleccione **Users & Groups** (Usuarios y grupos) > **Create a new user** (Crear un usuario nuevo).

    ![Captura de pantalla del sitio empresarial de Adobe Sign, con Account (Cuenta), Users & Groups (Usuarios y grupos) y Create a new user (Crear nuevo usuario) resaltados](./media/adobe-echosign-tutorial/account.png "Cuenta")

3. En la sección **Create New User** (Crear nuevo usuario), lleve a cabo estos pasos:

    ![Captura de pantalla de la sección Create New User (Crear nuevo usuario)](./media/adobe-echosign-tutorial/user.png "Crear usuario")

    a. Escriba en los campos de texto pertinentes los datos de **Email Address** (Dirección de correo electrónico), **Name** (Nombre) y **Last Name** (Apellidos) de la cuenta de correo válida de Azure AD que desea aprovisionar.

    b. Seleccione **Create User** (Crear usuario).

>[!NOTE]
>El titular de la cuenta de Azure Active Directory recibe un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active. 

### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Adobe Sign, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Adobe Sign e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Adobe Sign en Aplicaciones, debería iniciar sesión automáticamente en la instancia de Adobe Sign para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Adobe Sign, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).