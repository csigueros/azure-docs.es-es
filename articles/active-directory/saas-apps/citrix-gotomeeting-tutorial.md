---
title: 'Tutorial: Integración de Azure Active Directory con GoToMeeting | Microsoft Docs'
description: Obtenga información sobre los pasos necesarios para integrar GoToMeeting con Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: 27e8c3bf2f54541d8c96528472fbcb8d092aeca3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670621"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-gotomeeting"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con GoToMeeting

En este tutorial, aprenderá a integrar GoToMeeting con Azure Active Directory (Azure AD). Al integrar GoToMeeting con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a GoToMeeting.
* Permitir que los usuarios inicien sesión automáticamente en GoToMeeting con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en GoToMeeting.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* GoToMeeting admite el inicio de sesión único iniciado por **IDP**.
* GoToMeeting admite el [aprovisionamiento automatizado de usuarios](citrixgotomeeting-provisioning-tutorial.md).

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-gotomeeting-from-the-gallery"></a>Adición de GoToMeeting desde la galería

Para configurar la integración de GoToMeeting en Azure AD, tendrá que agregar GoToMeeting desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **GoToMeeting** en el cuadro de búsqueda.
1. Seleccione **GoToMeeting** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-gotomeeting"></a>Configuración y prueba del inicio de sesión único de Azure AD para GoToMeeting

Configure y pruebe el inicio de sesión único de Azure AD con GoToMeeting mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de GoToMeeting.

Para configurar y probar el inicio de sesión único de Azure AD con GoToMeeting, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en GoToMeeting](#configure-gotomeeting-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de GoToMeeting](#create-gotomeeting-test-user)** , para tener un homólogo de B.Simon en GoToMeeting vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **GoToMeeting**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://authentication.logmeininc.com/saml/sp`

    b. En el cuadro de texto **URL de respuesta**, escriba la dirección URL: `https://authentication.logmeininc.com/saml/acs`

    c. Haga clic en **Establecer direcciones URL adicionales** y configure las siguientes direcciones URL

    d. **Dirección URL de inicio de sesión** (mantener en blanco)

    e. En el cuadro de texto **RelayState**, escriba una de las siguientes direcciones URL:

   - Para la aplicación GoToMeeting, use `https://global.gotomeeting.com`

   - Para GoToTraining, use `https://global.gototraining.com`

   - Para GoToWebinar, use `https://global.gotowebinar.com` 

   - Para GoToAssist, use `https://app.gotoassist.com`

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up GoToMeeting** (Configurar GoToMeeting), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a GoToMeeting mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **GoToMeeting**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-gotomeeting-sso"></a>Configuración del inicio de sesión único de GoToMeeting

1. En otra ventana del explorador, inicie sesión en el [Centro de organización de GoToMeeting](https://organization.logmeininc.com/). Se le pedirá que confirme que se ha actualizado el IdP.

2. Habilite la casilla de verificación "Mi proveedor de identidad se ha actualizado con el nuevo dominio". Cuando haya terminado, haga clic en **Listo**.

### <a name="create-gotomeeting-test-user"></a>Creación del usuario de prueba de GoToMeeting

En esta sección, creará un usuario llamado a Britta Simon en GoToMeeting. GoToMeeting admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe aún en GoToMeeting., se crea uno nuevo cuando se intenta acceder a esta aplicación.

> [!NOTE]
> Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de GoToMeeting](https://support.logmeininc.com/gotomeeting).

> [!NOTE]
>GoToMeeting también admite el aprovisionamiento automático de usuarios. [Aquí](./citrixgotomeeting-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal y debería iniciar sesión automáticamente en la instancia de GoToMeeting para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de GoToMeeting en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de GoToMeeting para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado GoToMeeting, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).