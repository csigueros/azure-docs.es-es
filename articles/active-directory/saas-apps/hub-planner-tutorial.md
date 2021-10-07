---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Hub Planner'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Hub Planner.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: 66cbb4fa8e5f80e56b66b66802fb4c4b7ab8b9d2
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091623"
---
# <a name="tutorial-azure-ad-sso-integration-with-hub-planner"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Hub Planner

En este tutorial, obtendrá información sobre cómo integrar Hub Planner con Azure Active Directory (Azure AD). Al integrar Hub Planner con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Hub Planner.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Hub Planner con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Hub Planner.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Hub Planner admite el inicio de sesión único iniciado por **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-hub-planner-from-the-gallery"></a>Adición de Hub Planner desde la galería

Para configurar la integración de Hub Planner en Azure AD, será preciso que agregue Hub Planner desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Hub Planner** en el cuadro de búsqueda.
1. Seleccione **Hub Planner** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-hub-planner"></a>Configuración y prueba del inicio de sesión único de Azure AD para Hub Planner

Configure y pruebe el inicio de sesión único de Azure AD con Hub Planner mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Hub Planner.

Para configurar y probar el inicio de sesión único de Azure AD con Hub Planner, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Hub Planner](#configure-hub-planner-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Hub Planner](#create-hub-planner-test-user)** , para tener un homólogo de B.Simon en Hub Planner que esté vinculado a la representación de ella en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Hub Planner**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://app.hubplanner.com/sso/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.hubplanner.com/sso/callback`

    c. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.hubplanner.com`

    > [!NOTE]
    > Estos valores son los que va a usar. El único cambio que tiene que hacer es reemplazar \<SUBDOMAIN\> en la **dirección URL de inicio de sesión** con el subdominio que recibió al suscribirse a Hub Planner. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Hub Planner**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Hub Planner mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Hub Planner**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-hub-planner-sso"></a>Configuración del inicio de sesión único de Hub Planner

Para configurar el inicio de sesión único en **Hub Planner**, debe iniciar sesión en su cuenta de Hub Planner y completar las siguientes tareas. 

### <a name="install-the-extension-in-hub-planner"></a>Instalación de la extensión en Hub Planner

Para habilitar la funcionalidad de inicio de sesión único, primero debe habilitar la extensión. Como propietario de la cuenta o con permisos equivalentes, siga estos pasos:

1. Vaya a **Configuración**.
1. En el menú lateral, seleccione **Administrar extensiones** > **Agregar o quitar extensiones**.
1. Busque la extensión para el inicio de sesión único y agregar o probar gratis.
1. Cuando se le solicite, acepte los términos y condicione,s y, a continuación, seleccione **Agregar ahora**.

### <a name="enable-sso"></a>Habilitación de SSO

Una vez habilitada la extensión, debe habilitar el inicio de sesión único para su cuenta. 

1. Vaya a **Configuración**.
1. En el menú lateral, seleccione **Autenticación**.
1. Seleccione **SSO (inicio de sesión único)** .
1. Escriba la información de autenticación adicional que se muestra en la siguiente imagen y, a continuación, seleccione **Guardar**.

![Captura de pantalla de la configuración de SSO](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Creación de un usuario de prueba de Hub Planner

Si desea agregar otros usuarios, vaya a **Configuración** > **Administrar recursos** y agregue los usuarios desde aquí. Asegúrese de agregar su dirección de correo electrónico e invitarlos. Una vez invitados, recibirán un correo electrónico y podrán entrar a través del inicio de sesión único. 

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Hub Planner, donde podrá iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Hub Planner e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Hub Planner en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Hub Planner, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).