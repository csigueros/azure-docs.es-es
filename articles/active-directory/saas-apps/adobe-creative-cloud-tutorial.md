---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Adobe Creative Cloud | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Adobe Creative Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 5ba23a1bc5740eece8aa5e48d4f6cd81fab7b87e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803449"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Adobe Creative Cloud

En este tutorial aprenderá a integrar Adobe Creative Cloud con Azure Active Directory (Azure AD). Al integrar Adobe Creative Cloud con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Adobe Creative Cloud.
* Permitir que los usuarios inicien sesión automáticamente en Adobe Creative Cloud con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Adobe Creative Cloud.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Adobe Creative Cloud admite el inicio de sesión único iniciado por **SP**

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>Incorporación de Adobe Creative Cloud desde la galería

Para configurar la integración de Adobe Creative Cloud en Azure AD, necesita agregar Adobe Creative Cloud desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Adobe Creative Cloud** en el cuadro de búsqueda.
1. Seleccione **Adobe Creative Cloud** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Configuración y prueba del inicio de sesión único de Azure AD para Adobe Creative Cloud

Configure y pruebe el inicio de sesión único de Azure AD con Adobe Creative Cloud medinte un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Adobe Creative Cloud.

Para configurar y probar el inicio de sesión único de Azure AD con Adobe Creative Cloud, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Adobe Creative Cloud](#configure-adobe-creative-cloud-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** : para tener un homólogo de B.Simon en Adobe Creative Cloud vinculado a la representación de usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Adobe Creative Cloud**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL: `https://adobe.com`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > El valor del identificador no es real. Siga las indicaciones del paso 4 de la sección **Configuración del inicio de sesión único de Adobe Creative Cloud**. Desde ahí puede abrir el **archivo XML de metadatos de federación**, obtener el valor de identificador de entidad e incluirlo como un valor de identificador en la configuración de Azure AD. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Adobe Creative Cloud espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/edit-attribute.png)

1. Además de lo anterior, la aplicación Adobe Creative Cloud espera obtener algunos atributos más, que se muestran en la respuesta SAML a continuación. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre | Atributo de origen|
    |----- | --------- |
    | Nombre | user.givenname |
    | Apellidos | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > Los usuarios deben tener una licencia de Microsoft 365 ExO válida para que el valor de notificación por correo electrónico se rellene en la respuesta de SAML.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el archivo de metadatos XML y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Setup Adobe Creative Cloud** (Configurar Adobe Creative Cloud), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Adobe Creative Cloud mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Adobe Creative Cloud**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista de usuarios. A continuación, elija **Seleccionar** en la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

## <a name="configure-adobe-creative-cloud-sso"></a>Configuración del inicio de sesión único en Adobe Creative Cloud

1. En otra ventana del explorador web, inicie sesión como administrador de sistema en [Adobe Admin Console](https://adminconsole.adobe.com).

1. Vaya a **Settings** (Configuración) en la barra de navegación superior y, después, elija **Identity** (Identidad). Se abre la lista de directorios. Seleccione el directorio federado que desee.

1. En la página **Directory Details** (Detalles del directorio), seleccione **Configure** (Configurar).

1. Copie el identificador de entidad y la dirección URL de ACS (dirección URL del servicio de consumidor de aserciones o dirección URL de respuesta). Escriba las direcciones URL en los campos adecuados de Azure Portal.

    ![Configuración del inicio de sesión único en la aplicación](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use el valor del identificador de entidad que le proporciona Adobe como **Identificador** en el cuadro de diálogo **Configurar las opciones de la aplicación**.

    b. Use el valor de dirección URL de ACS (dirección URL del servicio de consumidor de aserciones) que le proporciona Adobe como **URL de respuesta** en el cuadro de diálogo **Configurar las opciones de la aplicación**.

1. Cerca de la parte inferior de la página, cargue el archivo **XML de metadatos de federación** que descargó de Azure Portal. 

    ![Archivo XML de metadatos de federación](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "XML de metadatos de IdP")

1. Seleccione **Guardar**.

### <a name="create-adobe-creative-cloud-test-user"></a>Creación de un usuario de prueba de Adobe Creative Cloud

Para permitir que los usuarios de Azure AD inicien sesión en Adobe Creative Cloud,deben haberse aprovisionado en Adobe Creative Cloud. En el caso de Adobe Creative Cloud, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1. Inicie sesión como administrador en el sitio [Adobe Admin Console](https://adminconsole.adobe.com).

2. Agregue el usuario en la consola de Adobe como identificador federado y asígnelo a un perfil de producto. Para obtener información detallada sobre cómo agregar usuarios, consulte [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) (Agregar usuarios en Adobe Admin Console).

3. Escriba ahora su dirección de correo electrónico o UPN en el formulario de inicio de sesión de Adobe, presione el tabulador y debería estar federado de nuevo en Azure AD:
   * Acceso web: www\.adobe.com/es > Inicio de sesión
   * En la utilidad de una aplicación de escritorio > Inicio de sesión
   * En la aplicación > Ayuda > Inicio de sesión

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Adobe Creative Cloud, desde donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la URL de inicio de sesión de Adobe Creative Cloud y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Adobe Creative Cloud en Mis aplicaciones, se le redirigirá a la URL de inicio de sesión de Adobe Creative Cloud. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que configure Adobe Creative Cloud, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).