---
title: 'Tutorial: Integración del inicio de sesión único de Azure Active Directory con Sentry | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Sentry.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 5a8cbdc77a7570037645b350b62787aeaea7374f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124821514"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sentry"></a>Tutorial: Integración del inicio de sesión único de Azure Active Directory con Sentry

En este tutorial aprenderá a integrar Sentry con Azure Active Directory (Azure AD). Al integrar Sentry con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Sentry.
* Permitir que los usuarios inicien sesión automáticamente en Sentry con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único en Sentry.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Sentry admite el inicio de sesión único iniciado por **SP e IDP**
* Sentry admite el aprovisionamiento de usuarios **Just-In-Time**

## <a name="adding-sentry-from-the-gallery"></a>Incorporación de Sentry desde la galería

Para configurar la integración de Sentry en Azure AD, deberá agregar Sentry desde la galería a la lista de aplicaciones de SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Sentry** en el cuadro de búsqueda.
1. Seleccione **Sentry** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-sentry"></a>Configuración y prueba del inicio de sesión único de Azure AD para Sentry

Configure y pruebe el inicio de sesión único de Azure AD con Sentry mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Sentry.

Para configurar y probar el inicio de sesión único de Azure AD con Sentry, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Sentry](#configure-sentry-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba en Sentry](#create-sentry-test-user)** : para tener un homólogo de B.Simon en Sentry vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Sentry**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://sentry.io/saml/metadata/<ORGANIZATION_SLUG>/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://sentry.io/saml/acs/<ORGANIZATION_SLUG>/`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://sentry.io/organizations/<ORGANIZATION_SLUG>/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los reales de Identificador, URL de respuesta y URL de inicio de sesión. Para obtener más información sobre cómo buscar estos valores, consulte la [documentación de Sentry](https://docs.sentry.io/product/accounts/sso/azure-sso/#installation). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el icono de copia para copiar el valor **Dirección URL de metadatos de la aplicación** y guárdelo en su equipo.

   ![Vínculo de descarga del certificado](common/copy-metadataurl.png)
    
### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, se crea un usuario llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección va a permitir que B.Simon acceda a Sentry mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Sentry**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sentry-sso"></a>Configuración del inicio de sesión único en Sentry

Para configurar el inicio de sesión único en **Sentry**, vaya a **Configuración de la organización** > **Aut.** (o vaya a `https://sentry.io/settings/<YOUR_ORG_SLUG>/auth/` y seleccione **Configurar** para Active Directory. Pegue la dirección URL de metadatos de federación de aplicación de la configuración de SAML de Azure.

### <a name="create-sentry-test-user"></a>Creación de un usuario de prueba en Sentry

En esta sección, se crea un usuario llamado B.Simon en Sentry. Sentry admite el aprovisionamiento de usuarios Just-In-Time, habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Sentry, se crea uno tras la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

1. En Azure Portal, seleccione **Probar esta aplicación**. Esta acción le redirigirá a la dirección URL de inicio de sesión de Sentry, donde puede comenzar el flujo de inicio de sesión.  

1. Vaya directamente a la dirección URL de inicio de sesión de Sentry e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* En Azure Portal, seleccione **Probar esta aplicación**. Debería iniciar sesión automáticamente en la aplicación Sentry para la que ha configurado el inicio de sesión único. 

#### <a name="either-mode"></a>Cualquier modo:

Puede usar el portal Aplicaciones para probar la aplicación en cualquier modo. Al hacer clic en el icono de Sentry del portal Aplicaciones, si está configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión. Si está configurado en modo IDP, debería iniciar sesión automáticamente en la aplicación Sentry para la que configuró el inicio de sesión único. Para más información acerca del portal Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Sentry, puede aplicar el control de sesión, que protege su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).