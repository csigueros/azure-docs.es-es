---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con SD Elements | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SD Elements.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 41691e5c640e8cf9007bee08386dd1416aa99dd0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124741542"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con SD Elements

En este tutorial aprenderá a integrar SD Elements con Azure Active Directory (Azure AD). Al integrar SD Elements con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a SD Elements.
* Permitir que los usuarios inicien sesión automáticamente en SD Elements con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en SD Elements.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SD Elements admite el inicio de sesión único iniciado por **IDP**.

## <a name="add-sd-elements-from-the-gallery"></a>Adición de SD Elements desde la galería

Para configurar la integración de SD Elements en Azure AD, deberá agregar SD Elements desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SD Elements** en el cuadro de búsqueda.
1. Seleccione **SD Elements** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sd-elements"></a>Configuración y prueba del inicio de sesión único de Azure AD para SD Elements

Configure y pruebe el inicio de sesión único de Azure AD con SD Elements mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SD Elements.

Para configurar y probar el inicio de sesión único de Azure AD con SD Elements, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en SD Elements](#configure-sd-elements-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en SD Elements](#create-sd-elements-test-user)** : para tener un homólogo de B.Simon en SD Elements vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **SD Elements**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configurar inicio de sesión único con SAML** realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<TENANT_NAME>.sdelements.com/sso/saml2/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<TENANT_NAME>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico para clientes de SD Elements](mailto:support@sdelements.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación SD Elements espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación SD Elements espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre |  Atributo de origen|
    | --- | --- |
    | email |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up SD Elements** (Configurar SD Elements), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a SD Elements mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SD Elements**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sd-elements-sso"></a>Configuración del inicio de sesión único en SD Elements

1. Para que le habiliten el inicio de sesión único, póngase en contacto con su [equipo de soporte técnico de SD Elements](mailto:support@sdelements.com) y proporcióneles el archivo de certificado descargado.

1. En una ventana diferente del explorador, inicie sesión en su inquilino de SD Elements como administrador.

1. En el menú de la parte superior, haga clic en **Sistema** y luego en **Inicio de sesión único**.

    ![Captura de pantalla que muestra "System" (Sistema) y "Single Sign-on" (Inicio de sesión único) seleccionados en la lista desplegable.](./media/sd-elements-tutorial/system.png)

1. En el cuadro de diálogo **Configuración de inicio de sesión único** , siga estos pasos:

    ![Configurar inicio de sesión único](./media/sd-elements-tutorial/settings.png)

    a. Como **Tipo de inicio de sesión único**, seleccione **SAML**.

    b. En el cuadro de texto **Identity Provider Entity ID** (Id. de entidad de proveedor de identidades), pegue el valor del campo **Identificador Azure AD** que ha copiado de Azure Portal.

    c. En el cuadro de texto **Identity Provider Single Sign-On Service** (Servicio de inicio de sesión único del proveedor de identidades), pegue el valor de **Dirección URL de inicio de sesión**  que ha copiado de Azure Portal.

    d. Haga clic en **Save**(Guardar).

### <a name="create-sd-elements-test-user"></a>Creación de un usuario de prueba en SD Elements

El objetivo de esta sección es crear un usuario de prueba llamado B.Simon en SD Elements. En el caso de SD Elements, la creación de usuarios de SD Elements es una tarea manual.

**Para crear el usuario B.Simon en SD Elements, realice los pasos siguientes:**

1. En una ventana de explorador web, inicie sesión como administrador en el sitio de la empresa de SD Elements.

1. En el menú de la parte superior, haga clic en **User Management** (Administración de usuarios) y luego en **Users** (Usuarios).

    ![Captura de pantalla que muestra "Users" (Usuarios) seleccionado en la lista desplegable "User Management" (Administración de usuarios).](./media/sd-elements-tutorial/users.png) 

1. Haga clic en **Add New User**(Agregar nuevo usuario).

    ![Captura de pantalla que muestra el botón "Add New User" (Agregar nuevo usuario) seleccionado.](./media/sd-elements-tutorial/add-user.png)

1. En el cuadro de diálogo **Add New User** (Agregar nuevo usuario), realice los pasos siguientes:

    ![Creación de un usuario de prueba de SD Elements](./media/sd-elements-tutorial/new-user.png) 

    a. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario con el siguiente formato **b.simon@contoso.com** .

    b. En el cuadro de texto **First Name** (Nombre), escriba el nombre de usuario, en este caso **B.**

    c. En el cuadro de texto **Last Name** (Apellidos), escriba el apellido del usuario, en este caso **Simon**.

    d. Como **Rol**, seleccione **Usuario**.

    e. Haga clic en **Create User**(Crear usuario).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal y debería iniciar sesión automáticamente en la instancia de SD Elements para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de SD Elements en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de SD Elements para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado SD Elements, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).