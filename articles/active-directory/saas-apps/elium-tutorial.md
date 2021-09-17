---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Elium | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Elium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/27/2021
ms.author: jeedes
ms.openlocfilehash: a49dec3b7a87eebebbb6d4737618a387c80e4f1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742858"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Elium

En este tutorial aprenderá a integrar Elium con Azure Active Directory (Azure AD). Al integrar Elium con Azure AD, se puede:

* Controlar en Azure AD quién tiene acceso a Elium.
* Permitir que los usuarios inicien sesión automáticamente en Elium con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Elium.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Elium admite el inicio de sesión único iniciado por **SP e IDP**.
* Elium admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-elium-from-the-gallery"></a>Adición de Elium desde la galería

Para configurar la integración de Elium en Azure AD, deberá agregar Elium desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Elium** en el cuadro de búsqueda.
1. Seleccione **Elium** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Configuración y prueba del inicio de sesión único de Azure AD para Elium

Configure y pruebe el inicio de sesión único de Azure AD con Elium mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Elium.

Para configurar y probar el inicio de sesión único de Azure AD con Elium, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Elium](#configure-elium-sso)**: para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Elium](#create-elium-test-user)**: para tener un homólogo de B.Simon en Elium vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Elium**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Estos valores no son reales. Los obtendrá del **archivo de metadatos de SP** que se puede descargar en `https://<platform-domain>.elium.com/login/saml2/metadata`. Esto se explica más adelante en este tutorial.

1. La aplicación Elium espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Elium espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre | Atributo de origen|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Estas son las notificaciones predeterminadas. **Solo se requiere la notificación de correo electrónico**. Para el aprovisionamiento de JIT, también solo es obligatoria la notificación de correo electrónico. Otras notificaciones personalizadas pueden variar de una plataforma de cliente a otra.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Elium** (Configurar Elium), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Elium mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Elium**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-elium-sso"></a>Configuración del inicio de sesión único en Elium

1. Para automatizar la configuración en Elium, debe instalar la **extensión del explorador de inicio de sesión seguro Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

1. Después de agregar la extensión al explorador, haga clic en **Set up Elium** (Configurar Elium) para ir a la aplicación del mismo nombre. En ella, escriba las credenciales de administrador para iniciar sesión en Elium. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 6.

    ![Configuración](common/setup-sso.png)

1. Si quiere configurar Elium manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Elium como administrador y haga lo siguiente:

1. Haga clic en el **perfil de usuario** en la esquina superior derecha y, a continuación, seleccione **Settings** (Configuración).

    ![Configure el perfil de inicio de sesión único.](./media/elium-tutorial/profile.png)

1. Seleccione **Security** (Seguridad) en **Advanced** (Avanzado).

    ![Configure el inicio de sesión único avanzado.](./media/elium-tutorial/security.png)

1. Desplácese a la sección **Single sign-on (SSO)** (Inicio de sesión único [SSO]) y realice los pasos siguientes:

    ![Configure el inicio de sesión único.](./media/elium-tutorial/configuration.png)

    a. Copie el valor de **Verify that SAML2 authentication works for your account** (Verificar que la autenticación de SAML2 funciona para su cuenta) y péguelo en el cuadro de texto **Dirección URL de inicio de sesión** de la sección **Configuración básica de SAML** de Azure Portal.

    > [!NOTE]
    > Después de configurar el inicio de sesión único, siempre puede acceder a la página de inicio de sesión remoto predeterminada en la siguiente dirección URL: `https://<platform_domain>/login/regular/login`. 

    b. Active la casilla **Enable SAML2 federation** (Habilitar la federación de SAML2).

    c. Active la casilla **JIT Provisioning** (Aprovisionamiento de JIT).

    d. Abra el archivo de **metadatos de SP**; para ello, haga clic en el botón **Download** (Descargar).

    e. Busque **entityID** en el archivo de **metadatos de SP**, copie el valor de **entityID** y péguelo en el cuadro de texto **Identificador** de la sección **Configuración básica de SAML**  de Azure Portal. 

    ![Establezca la configuración de inicio de sesión único.](./media/elium-tutorial/metadata.png)

    f. Busque **AssertionConsumerService** en el archivo de **metadatos de SP**, copie el valor de **Location** (Ubicación) y péguelo en el cuadro de texto **Dirección URL de respuesta** de la sección **Configuración básica de SAML** de Azure Portal.

    ![Configure AssertionConsumerService de inicio de sesión único.](./media/elium-tutorial/service.png)

    g. Abra el archivo de metadatos descargado de Azure Portal en el Bloc de notas, copie el contenido y péguelo en el cuadro de texto **IdP Metadata** (Metadatos del IdP).

    h. Haga clic en **Save**(Guardar).

### <a name="create-elium-test-user"></a>Creación de un usuario de prueba en Elium

En esta sección se crea un usuario llamado B.Simon en Elium. Box admite el **aprovisionamiento Just-In-Time**, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Elium, se crea uno nuevo cuando se intenta acceder a Elium.

> [!Note]
> Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 
 
#### <a name="sp-initiated"></a>Iniciado por SP:
 
* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Elium, donde puede iniciar el flujo de inicio de sesión.  
 
* Vaya directamente a la dirección URL de inicio de sesión de Elium e inicie el flujo de inicio de sesión desde allí.
 
#### <a name="idp-initiated"></a>Iniciado por IDP:
 
* Haga clic en **Probar esta aplicación** en Azure Portal y debería iniciar sesión automáticamente en la instancia de Elium para la que configuró el inicio de sesión único. 
 
También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Elium en Aplicaciones, si está configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión y, si está configurado en modo IDP, debería iniciar sesión automáticamente en la instancia de Elium para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Elium puede aplicar el control de sesión, que protege a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).