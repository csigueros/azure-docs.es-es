---
title: 'Tutorial: Integración de Azure Active Directory con Zoho | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 9468f957f2679856a3f6d75a1d00a876ab66e37b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764742"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Integración de Azure Active Directory con Zoho

En este tutorial, aprenderá a integrar Zoho con Azure Active Directory (Azure AD). Si integra Zoho con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Zoho.
* Permitir que los usuarios inicien sesión automáticamente en Zoho con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Prerrequisitos

Para configurar la integración de Azure AD con Zoho One, se necesitan los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción que permita utilizar el inicio de sesión único en Zoho.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Zoho admite el inicio de sesión único iniciado por **SP**.

## <a name="add-zoho-from-the-gallery"></a>Incorporación de Zoho desde la galería

Para configurar la integración de Zoho en Azure AD, deberá agregar Zoho desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Zoho** en el cuadro de búsqueda.
1. Seleccione **Zoho** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Configuración y comprobación del inicio de sesión único de Azure AD con Zoho

Configure y pruebe el inicio de sesión único (SSO) de Azure AD con Zoho utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso vincular un usuario de Azure AD y el usuario correspondiente de Zoho.

Para configurar y probar el inicio de sesión único de Azure AD con Zoho, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Zoho](#configure-zoho-sso)** : para configurar los valores de inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba de Zoho](#create-zoho-test-user)** : para tener un homólogo de B.Simon en Zoho que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones **Zoho**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.zohomail.com`

    > [!NOTE]
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de Zoho](https://www.zoho.com/mail/contact.html) para obtener el valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up Zoho** (Configurar Zoho), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon utilice el inicio de sesión único de Azure concediéndole acceso a Zoho.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Zoho**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="configure-zoho-sso"></a>Configuración del inicio de sesión único en Zoho

1. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zoho Mail como administrador.

2. Vaya al **Panel de control**.
   
    ![Control Panel (Panel de control)](./media/zoho-mail-tutorial/control-panel.png "Panel de control")

3. Haga clic en la pestaña **Autenticación SAML** .
   
    ![SAML Authentication (Autenticación SAML)](./media/zoho-mail-tutorial/saml-authentication.png "Autenticación SAML")

4. En la sección **Detalles de la autenticación SAML** , realice los pasos siguientes:
   
    ![SAML Authentication Details (Detalles de la autenticación SAML)](./media/zoho-mail-tutorial/details.png "Detalles de la autenticación SAML")
   
    a. En el cuadro de texto **URL de inicio de sesión**, pegue la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.
   
    b. En el cuadro de texto **URL de cierre de sesión**, pegue la **dirección URL de cierre de sesión** que ha copiado de Azure Portal.
   
    c. En el cuadro de texto **Change Password Link** (Cambiar vínculo de contraseña), pegue el valor de **Cambiar dirección URL de contraseña** que copió de Azure Portal.
       
    d. Abra en el Bloc de notas el certificado codificado en Base 64 que descargó de Azure Portal, copie el contenido en el Portapapeles y, luego, péguelo en el cuadro de texto **PublicKey** (Clave pública).
   
    e. En **Algoritmo**, seleccione **RSA**.
   
    f. Haga clic en **OK**.

### <a name="create-zoho-test-user"></a>Creación de un usuario de prueba de Zoho

Para permitir que los usuarios de Azure AD inicien sesión en Zoho Mail, deben aprovisionarse a Zoho Mail. En el caso de Zoho Mail, el aprovisionamiento es una tarea manual.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zoho Mail ofrecida por Zoho Mail para aprovisionar cuentas de usuario de Azure AD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en su sitio de la compañía de **Zoho Mail** como administrador.

1. Vaya a **Control Panel \> Mail & Docs** (Panel de control > Correo y documentos) .

1. Vaya a **User Details \> Add User** (Detalles del usuario > Agregar usuario).
   
    ![Captura de pantalla que muestra el sitio de Zoho Mail con las opciones User Details (Detalles de usuario) y Add User (Agregar usuario) seleccionadas.](./media/zoho-mail-tutorial/add-user-1.png "Agregar usuario")

1. En el cuadro de diálogo **Agregar usuarios** , realice los pasos siguientes:
   
    ![Captura de pantalla que muestra el cuadro de diálogo para agregar usuarios, donde puede especificar los valores que se han indicado.](./media/zoho-mail-tutorial/add-user-2.png "Agregar usuario")
   
    a. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso, **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba los apellidos del usuario, en este caso, **Simon**.

    c. En el cuadro de texto **Identificador de correo electrónico**, escriba la dirección de correo electrónico de un usuario; por ejemplo, **brittasimon\@contoso.com**.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.
   
    e. Haga clic en **OK**.  
      
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

### <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. De este modo, accederá automáticamente a la dirección URL de inicio de sesión de Zoho, donde podrá comenzar el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de Zoho e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Si en Aplicaciones hace clic en el icono de Zoho, debería iniciar sesión automáticamente en la instancia de Zoho en la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Zoho, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).