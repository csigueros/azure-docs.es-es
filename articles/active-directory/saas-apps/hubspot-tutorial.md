---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con HubSpot'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: b4e91ab02e02da602010cb06ff0b9e3f88d31c64
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670160"
---
# <a name="tutorial-azure-ad-sso-integration-with-hubspot"></a>Tutorial: Integración del inicio de sesión único de Azure AD con HubSpot

En este tutorial aprenderá a integrar HubSpot con Azure Active Directory (Azure AD). Al integrar HubSpot con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a HubSpot.
* Permitir que los usuarios inicien sesión automáticamente en HubSpot con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con HubSpot, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción de Azure AD, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* Una suscripción de HubSpot habilitada para el inicio de sesión único.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba e integrar HubSpot con Azure AD.

HubSpot admite las siguientes características:

* **Inicio de sesión único iniciado por SP**.
* **Inicio de sesión único iniciado por IDP**.

## <a name="add-hubspot-from-the-gallery"></a>Incorporación de HubSpot desde la galería

Para configurar la integración de HubSpot en Azure AD, será preciso que agregue HubSpot desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **HubSpot** en el cuadro de búsqueda.
1. Seleccione **HubSpot** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Configuración y prueba del inicio de sesión único de Azure AD para HubSpot

Configure y pruebe el inicio de sesión único de Azure AD con HubSpot mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de HubSpot.

Para configurar y probar el inicio de sesión único de Azure AD con HubSpot, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en HubSpot](#configure-hubspot-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de HubSpot](#create-hubspot-test-user)** : para tener un homólogo de B.Simon en HubSpot que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

1. En Azure Portal, en la página de integración de la aplicación **HubSpot**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En el panel **Configuración básica de SAML**, para configurar el **modo iniciado por IDP**, siga estos pasos:

    1. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>.

    1. En el cuadro de texto **Dirección URL de respuesta**, escriba una dirección URL con el siguiente patrón: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>.

    > [!NOTE]
    > Para dar formato a las direcciones URL, también puede hacer referencia a los patrones que se muestran en el panel **Configuración básica de SAML** de Azure Portal.

1. Para configurar la aplicación en modo *iniciado por SP*:

    1. Seleccione **Establecer direcciones URL adicionales**.

    1. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba **https:\//app.hubspot.com/login**.

1. En el panel **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, seleccione **Descargar** junto a **Certificado (base 64)** . Seleccione una opción de descarga según sus requisitos. Guarde el certificado en el equipo.

    ![Opción de descarga del certificado (Base64)](common/certificatebase64.png)

1. En la sección **Configurar HubSpot**, copie las direcciones URL siguientes según sus necesidades:

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

En esta sección va a conceder a B.Simon acceso a HubSpot mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **HubSpot**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-hubspot-sso"></a>Configuración del inicio de sesión único de HubSpot

1. Abra una nueva pestaña en el explorador e inicie sesión en su cuenta de administrador de HubSpot.

1. Seleccione el icono **Settings** (Configuración) en la esquina superior derecha de la página.

    ![Icono de configuración de HubSpot](./media/hubspot-tutorial/icon.png)

1. Seleccione **Account Defaults** (Valores predeterminados de cuenta).

    ![Opción Valores predeterminados de cuenta en HubSpot](./media/hubspot-tutorial/account.png)

1. Desplácese hacia abajo hasta la sección **Security** (Seguridad) y, a continuación, seleccione **Set up** (Configurar).

    ![Opción de configuración en HubSpot](./media/hubspot-tutorial/security.png)

1. En la sección **Set Up Single Sign-On** (Configurar inicio de sesión único), realice los pasos siguientes:

    1. En el cuadro de texto **Audience URl (Service Provider Entity ID)** (Identificador URl de audiencia [identificador de entidad del proveedor de servicio]), seleccione **Copy** (Copiar) para copiar el valor. En Azure Portal, en el panel **Configuración básica de SAML**, pegue el valor en el cuadro **Identificador**.

    1. En el cuadro de texto **Sign on URL, ACS, Recipient, or Redirect** (Dirección URL de inicio de sesión, ACS, Destinatario o Redirigir), seleccione **Copy** (Copiar) para copiar el valor. En Azure Portal, en el panel **Configuración básica de SAML**, pegue el valor en el cuadro **Dirección URL de respuesta**.

    1. En HubSpot, en el cuadro de texto **Identity Provider Identifier or Issuer URL** (Dirección URL del emisor o identificador de proveedor de identidades), pegue el valor de **Identificador de Azure AD** que ha copiado de Azure Portal.

    1. En HubSpot, en el cuadro de texto **Identity Provider Single Sign-On URL** (dirección URL de inicio de sesión único del proveedor de identidades), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    1. En el Bloc de notas de Windows, abra el archivo de **certificado (Base64)** descargado. Seleccione y copie el contenido del archivo. A continuación, en HubSpot, péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509).

    1. Seleccione **Comprobar**.

        ![Sección de configuración del inicio de sesión único en HubSpot](./media/hubspot-tutorial/certificate.png)

### <a name="create-hubspot-test-user"></a>Creación de un usuario de prueba de HubSpot

Para permitir que los usuarios de Azure AD inicien sesión en HubSpot, deben aprovisionarse en HubSpot. En HubSpot, el aprovisionamiento es una tarea manual.

Para aprovisionar una cuenta de usuario en HubSpot:

1. Inicie sesión en el sitio de la compañía de HubSpot como administrador.

1. Seleccione el icono **Settings** (Configuración) en la esquina superior derecha de la página.

    ![Icono de configuración de HubSpot](./media/hubspot-tutorial/icon.png)

1. Seleccione **Users & Teams** (Usuarios y equipos).

    ![Opción de usuarios y equipos de HubSpot](./media/hubspot-tutorial/users.png)

1. Seleccione **Create User** (Crear usuario).

    ![Opción Crear usuario en HubSpot](./media/hubspot-tutorial/teams.png)

1. En el cuadro **Add email addess(es)** (Agregar direcciones de correo electrónico), escriba la dirección de correo electrónico del usuario con el formato brittasimon\@contoso.com y, a continuación, seleccione **Next** (Siguiente).

    ![Cuadro de direcciones de correo electrónico de la sección Crear usuarios de HubSpot](./media/hubspot-tutorial/add-user.png)

1. En la sección **Create users** (Crear usuarios), seleccione cada pestaña. En cada pestaña, establezca las opciones y los permisos adecuados para el usuario. Después, seleccione **Siguiente**.

    ![Pestañas de la sección Crear usuarios de Hubspot](./media/hubspot-tutorial/create-user.png)

1. Seleccione **Send** (Enviar) para enviar la invitación al usuario.

    ![Opción de envío de HubSpot](./media/hubspot-tutorial/invitation.png)

    > [!NOTE]
    > El usuario se activa después de que el usuario acepta la invitación.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de HubSpot, donde podrá iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de HubSpot e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; se debería iniciar sesión automáticamente en la instancia de HubSpot para la que haya configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de HubSpot en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y, si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de HubSpot para la que configurara el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado HubSpot, podrá aplicar el control de sesión, que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).