---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con directprint.io Cloud Print Administration | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y directprint.io Cloud Print Administration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: fd525b3664a19a95b27069823514e7c25ac72074
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124822645"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-directprintio-cloud-print-administration"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con directprint.io Cloud Print Administration

En este tutorial, aprenderá a integrar directprint.io Cloud Print Administration con Azure Active Directory (Azure AD). Al integrar directprint.io Cloud Print Administration con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a ldirectprint.io Cloud Print Administration.
* Permitir que los usuarios inicien sesión automáticamente en directprint.io Cloud Print Administration con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción con el inicio de sesión único (SSO) habilitado para directprint.io Cloud Print Administration.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* directprint.io Cloud Print Administration admite el SSO iniciado por **IDP**.

* directprint.io Cloud Print Administration admite el aprovisionamiento de usuarios **Just In Time**.

## <a name="add-directprintio-cloud-print-administration-from-the-gallery"></a>Adición de directprint.io Cloud Print Administration desde la galería

Para configurar la integración de directprint.io Cloud Print Administration en Azure AD, es preciso agregar esta solución desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la Galería**, escriba **directprint.io Cloud Print Administration** en el cuadro de búsqueda.
1. Seleccione **directprint.io Cloud Print Administration en** el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-directprintio-cloud-print-administration"></a>Configuración y prueba del inicio de sesión único de Azure AD para directprint.io Cloud Print Administration

Configure y pruebe el inicio de sesión único de Azure AD con directprint.io Cloud Print Administration con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de directprint.io Cloud Print Administration.

Para configurar y probar el inicio de sesión único de Azure AD con directprint.io Cloud Print Administration, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del SSO de directprint.io Cloud Print Administration](#configure-directprintio-cloud-print-administration-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de directprint.io Cloud Print Administration](#create-directprintio-cloud-print-administration-test-user)** : para tener un homólogo de B.Simon en directprint.io Cloud Print Administration que esté vinculado a la representación del usuario de Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Porta, en la página de integración de la aplicación **directprint.io Cloud Print Administration**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada en el modo iniciado por IDP y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe hacer clic en el botón **Guardar**.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

1. En la sección **Configurar directprint.io Cloud Print Administration**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, para permitir que B.Simon use el inicio de sesión único de Azure, le concederá acceso a directprint.io Cloud Print Administration.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **directprint.io Cloud Print Administration**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-directprintio-cloud-print-administration-sso"></a>Configuración del i inicio de sesión único de directprint.io Cloud Print Administration

Para configurar el inicio de sesión único en **directprint.io Cloud Print Administration**, debe enviar la **URL de metadatos de federación de aplicación** correspondiente al [equipo de soporte técnico de directprint.io Cloud Print Administration](mailto:support@directprint.io). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-directprintio-cloud-print-administration-test-user"></a>Creación del usuario de prueba de directprint.io Cloud Print Administration

En esta sección, creará un usuario llamado B.Simon en directprint.io Cloud Print Administration. directprint.io Cloud Print Administration admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario deja de existir en directprint.io Cloud Print Administration, se crea otro después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de directprint.io Cloud Print Administration para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de directprint.io Cloud Print Administration en Aplicaciones, debería iniciar sesión automáticamente en la instancia de directprint.io Cloud Print Administration para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado directprint.io Cloud Print Administration, puede aplicar el control de sesión, que protege a su organización, en tiempo real, frente a la filtración y la infiltración de la información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).