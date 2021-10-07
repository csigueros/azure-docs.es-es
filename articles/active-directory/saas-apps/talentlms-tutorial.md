---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con TalentLMS'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 055c3774acfa3930e7fa1663a46629e80448c25f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606035"
---
# <a name="tutorial-azure-ad-sso-integration-with-talentlms"></a>Tutorial: Integración del inicio de sesión único de Azure AD con TalentLMS

En este tutorial, aprenderá a integrar TalentLMS con Azure Active Directory (Azure AD). Si integra TalentLMS con Azure AD, podrá hacer lo siguiente:

* Controlar desde Azure AD quién tiene acceso a TalentLMS.
* Permitir que los usuarios inicien sesión automáticamente en TalentLMS con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con TalentLMS, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción que permita el inicio de sesión único en TalentLMS.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* TalentLMS admite el inicio de sesión único iniciado por **SP**.

## <a name="add-talentlms-from-the-gallery"></a>Adición de TalentLMS desde la galería

Para configurar la integración de TalentLMS en Azure AD, deberá agregar TalentLMS desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **TalentLMS** en el cuadro de búsqueda.
1. Seleccione **TalentLMS** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Configuración y comprobación del inicio de sesión único de Azure AD para TalentLMS

Configure y pruebe el inicio de sesión único de Azure AD con TalentLMS utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de TalentLMS.

Para configurar y probar el inicio de sesión único de Azure AD con TalentLMS, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en TalentLMS](#configure-talentlms-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en TalentLMS](#create-talentlms-test-user)** : para tener un homólogo de B.Simon en TalentLMS vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **TalentLMS**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `http://<tenant-name>.talentlms.com`

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.TalentLMSapp.com`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico de TalentLMS](https://www.talentlms.com/contact) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la sección **Certificado de firma de SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Certificado de firma de SAML**.

    ![Edición del certificado de firma de SAML](common/edit-certificate.png)

6. En la sección **Certificado de firma de SAML**, copie el valor de **Huella digital** y guárdelo en el equipo.

    ![Copia del valor de la huella digital](common/copy-thumbprint.png)

7. En la sección **Set up TalentLMS** (Configurar TalentLMS), copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon utilice el inicio de sesión único de Azure concediéndole acceso a TalentLMS.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **TalentLMS**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-talentlms-sso"></a>Configuración del inicio de sesión único de TalentLMS

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía TalentLMS como administrador.

1. En la sección **Account & Settings** (Cuenta y configuración), haga clic en la pestaña **Users** (Usuarios).

    ![Cuenta y configuración](./media/talentlms-tutorial/user.png "Cuenta y configuración")

1. Haga clic en **Inicio de sesión único (SSO)**.

1. En la sección Inicio de sesión único, siga estos pasos:

    ![Inicio de sesión único](./media/talentlms-tutorial/certificate.png "Inicio de sesión único")

    a. En la lista **Tipo de integración de SSO**, seleccione **SAML 2.0**.

    b. En el cuadro de texto **Identity Provider (IDP)** (Proveedor de identidades [IDP]), pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    c. Pegue el valor de **Huella digital** de Azure Portal en el cuadro de texto de **Certificate fingerprint** (Huella digital de certificado).

    d.  En el cuadro de texto **Dirección URL de inicio de sesión remoto**, pegue el valor de **Dirección URL de inicio de sesión** que copió de Azure Portal.

    e. En el cuadro de texto **Dirección URL de cierre de sesión remoto**, pegue el valor de **URL de cierre de sesión** que copió de Azure Portal.

    f. Rellene lo siguiente:

    * En el cuadro de texto **TargetedID** (Id. dirigido), escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    * En el cuadro de texto **First name** (Nombre), escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    * En el cuadro de texto **Last name** (Apellidos), escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    * En el cuadro de texto **Email** (Correo electrónico), escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

1. Haga clic en **Save**(Guardar).

### <a name="create-talentlms-test-user"></a>Creación de un usuario de prueba en TalentLMS

Para permitir que los usuarios de Azure AD inicien sesión en TalentLMS, deben aprovisionarse en la aplicación. En el caso de TalentLMS, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **TalentLMS**.

1. Haga clic en **Usuarios** y, a continuación, en **Agregar usuario**.

1. En la página de cuadro de diálogo **Agregar usuario**, lleve a cabo los pasos siguientes:

    ![Agregar usuario](./media/talentlms-tutorial/account.png "Agregar usuario")  

    a. En el cuadro de texto **First Name** (Nombre), escriba el nombre de usuario, por ejemplo, `Britta`.

    b. En el cuadro de texto **Apellidos**, escriba el apellido del usuario, en este caso `Simon`.
 
    c. En el cuadro de texto **Email address** (Dirección de correo electrónico), escriba el correo electrónico del usuario, en este caso, `brittasimon@contoso.com`.

    d. Haga clic en **Agregar usuario**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TalentLMS ofrecida por TalentLMS para aprovisionar cuentas de usuario de Azure AD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Al hacerlo, accederá automáticamente a la dirección URL de TalentLMS, donde podrá comenzar el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de TalentLMS y comience el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Si en Aplicaciones hace clic en el icono de TalentLMS, debería acceder automáticamente a la dirección URL de inicio de sesión de esta aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado TalentLMS, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).