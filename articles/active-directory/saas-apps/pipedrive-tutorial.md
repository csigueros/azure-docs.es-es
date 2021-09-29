---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Pipedrive | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Pipedrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 7713a1a502697a52ed811b8ed41b13485f454877
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Pipedrive

En este tutorial, aprenderá a integrar Pipedrive con Azure Active Directory (Azure AD). Al integrar Pipedrive con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Pipedrive.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Pipedrive con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Pipedrive.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Pipedrive admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="add-pipedrive-from-the-gallery"></a>Adición de Pipedrive desde la galería

Para configurar la integración de Pipedrive en Azure AD, es preciso agregar Pipedrive desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Pipedrive** en el cuadro de búsqueda.
1. Seleccione **Pipedrive** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-pipedrive"></a>Configuración y prueba del inicio de sesión único de Azure AD con Pipedrive

Configure y pruebe el inicio de sesión único de Azure AD con Pipedrive mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Pipedrive.

Para configurar y probar el inicio de sesión único de Azure AD con Pipedrive, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Pipedrive](#configure-pipedrive-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba de Pipedrive](#create-pipedrive-test-user)** : para tener un homólogo de B.Simon en Pipedrive que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Pipedrive**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Pipedrive](mailto:support@pipedrive.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Pipedrive espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Pipedrive espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre | Atributo de origen|
    | ------------ | --------- |
    | email | user.mail |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (base 64)** y seleccione **Descargar** para descargar el certificado y guardarlo en el equipo; copie también **Dirección URL de metadatos de federación de la aplicación** y guárdela en el equipo.

    ![Vínculo de descarga del certificado](./media/pipedrive-tutorial/certificate-data.png)

1. En la sección **Configurar Pipedrive**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Pipedrive mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Pipedrive**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-pipedrive-sso"></a>Configuración del inicio de sesión único de Pipedrive

1. En otra ventana del explorador, inicie sesión en el sitio web de Pipedrive como administrador.

1. Haga clic en **User profile** (Perfil de usuario) y seleccione **Settings** (Configuración).

    ![Captura de pantalla que muestra "Settings" (Configuración) seleccionado en el menú "User Profile" (Perfil de usuario).](./media/pipedrive-tutorial/configure-1.png)

1. Desplácese hacia abajo hasta Security Center y seleccione **Single sign-on** (Inicio de sesión único).

    ![Captura de pantalla que muestra "Single sign-on" (Inicio de sesión único) seleccionado en "Security Center".](./media/pipedrive-tutorial/configure-2.png)

1. En la sección **SAML configuration for Pipedrive** (Configuración de SAML para Pipedrive), siga estos pasos:

    ![Captura de pantalla que muestra la sección "S A M L configuration for Pipedrive" (Configuración de S A M L para Pipedrive) con todos los cuadros de texto resaltados.](./media/pipedrive-tutorial/configure-3.png)

    a. En el cuadro de texto **Issuer** (Emisor), pegue el valor de **Dirección URL de metadatos de federación de aplicación** que copió de Azure Portal.

    b. En el cuadro de texto **Single Sign On (SSO) URL** (Dirección URL de inicio de sesión único [SSO]), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c. En el cuadro de texto **Single Log Out (SLO) URL** (Dirección URL de cierre de sesión único [SLO]), pegue el valor de **Dirección URL de cierre de sesión** que ha copiado de Azure Portal.

    d. En el cuadro de texto **X.509 Certificate** (Certificado X.509), abra el archivo **Certificado (base 64)** descargado de Azure Portal en el Bloc de notas, copie el contenido del mismo, péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509) y guarde los cambios.

### <a name="create-pipedrive-test-user"></a>Creación de un usuario de prueba de Pipedrive

1. En otra ventana del explorador, inicie sesión en el sitio web de Pipedrive como administrador.

1. Desplácese hacia abajo hasta empresa y seleccione **Manage users** (Administrar usuarios).

    ![Captura de pantalla que muestra "Manage users" (Administrar usuarios) seleccionado en el menú "Company" (Empresa).](./media/pipedrive-tutorial/user-1.png)

1. Haga clic en **Add users** (Agregar usuarios).
    
    ![Captura de pantalla que muestra la página "Manage users" (Administrar usuarios) con el botón "Add users" (Agregar usuarios) seleccionado en el lado derecho.](./media/pipedrive-tutorial/user-2.png)

1. En la sección **Manage users** (Administrar usuarios), realice los siguientes pasos:

    ![Configuración de Pipedrive](./media/pipedrive-tutorial/user-3.png)

    a. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico del usuario, en este caso, `B.Simon@contoso.com`.

    b. En el cuadro de texto **First Name** (Nombre), escriba el nombre del usuario.

    c. En el cuadro de texto **Last name** (Apellidos), escriba los apellidos del usuario.

    d. Haga clic en **Confirm and invite users** (Confirmar e invitar a los usuarios).

## <a name="test-sso"></a>Prueba de SSO 


En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Pipedrive, donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Pipedrive y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal y debería iniciar sesión automáticamente en la instancia de Pipedrive para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Pipedrive en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Pipedrive para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Pipedrive, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).