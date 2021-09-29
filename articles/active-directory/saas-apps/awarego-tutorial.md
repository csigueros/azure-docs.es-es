---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con AwareGo | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AwareGo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 096315d314555478b993151bd1b562010cb24949
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761284"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Tutorial: Integración del inicio de sesión único de Azure Active Directory con AwareGo

En este tutorial, aprenderá a integrar AwareGo con Azure Active Directory (Azure AD). Al integrar AwareGo con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a AwareGo.
* Permitir que los usuarios inicien sesión automáticamente en AwareGo con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central, Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en AwareGo.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. AwareGo admite el inicio de sesión único iniciado por un proveedor de servicios.


## <a name="adding-awarego-from-the-gallery"></a>Adición de AwareGo desde la galería

Para configurar la integración de AwareGo en Azure AD, debe agregar la aplicación desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal mediante una cuenta personal, profesional o educativa de Microsoft.
1. En el panel izquierdo, seleccione el servicio **Azure Active Directory**.
1. Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **AwareGo** en el cuadro de búsqueda.
1. En el panel de resultados, seleccione **AwareGo** y agregue la aplicación. En cuestión de segundos, la aplicación se agregará al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Configuración y prueba del inicio de sesión único de Azure AD para AwareGo

Configure y pruebe el inicio de sesión único de Azure AD con AwareGo mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de AwareGo.

Para configurar y probar el inicio de sesión único de Azure AD con AwareGo, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.  

    a. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** para probar el inicio de sesión único de Azure AD con el usuario B.Simon.  
    b. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** para que el usuario B.Simon pueda usar el inicio de sesión único de Azure AD.  

1. **[Configuración del inicio de sesión único de AwareGo](#configure-awarego-sso)** , para definir los valores de inicio de sesión único en la aplicación.

    a. **[Creación de un usuario de prueba de AwareGo](#create-an-awarego-test-user)** , para tener un homólogo de B.Simon en AwareGo vinculado a la representación del usuario en Azure AD.  
    b. **[Prueba del inicio de sesión único](#test-sso)** para comprobar que la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Para habilitar el inicio de sesión único de Azure AD en Azure Portal, siga estos pasos:

1. En Azure Portal, en la página de integración de aplicaciones de **AwareGo**, en **Administrar**, seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. Para editar la configuración, en el panel **Configurar inicio de sesión único con SAML**, seleccione el botón **Editar**.

   ![Captura de pantalla del botón Editar para la configuración básica de SAML.](common/edit-urls.png)

1. En el panel de edición, en **Configuración básica de SAML**, haga lo siguiente:

    a. En el cuadro **URL de inicio de sesión**, escriba cualquiera de las siguientes direcciones URL:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. En el cuadro **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente formato: `https://<SUBDOMAIN>.awarego.com`

    c. En el cuadro **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Los valores anteriores no son reales. Actualícelos con el identificador y la URL de respuesta reales. Para obtener los valores, póngase en contacto con el [equipo de soporte técnico al cliente de AwareGo](mailto:support@awarego.com). También puede hacer referencia a los patrones de la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, junto a **Certificado (Base64)** , seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Captura de pantalla del vínculo "Descargar" del certificado en el panel Certificado de firma de SAML.](common/certificatebase64.png)

1. En la sección **Set up AwareGo** (Configurar AwareGo), copie una o más direcciones URL en función de sus requisitos.

    ![Captura de pantalla del panel "Set up AwareGo" (Configurar AwareGo) para copiar las direcciones URL de configuración.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory** y, a continuación, **Usuarios** > **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En el panel de propiedades del **usuario**, haga lo siguiente:

   a. En el cuadro **Nombre**, escriba **B.Simon**.  
   b. En el cuadro **Nombre de usuario**, escriba el nombre de usuario con el siguiente formato: `<username>@<companydomain>.<extension>` (por ejemplo, B.Simon@contoso.com).  
   c. Seleccione la casilla **Mostrar contraseña** y tome nota del valor que se muestra en el cuadro **Contraseña** para usarlo posteriormente.  
   d. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a AwareGo mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
1. En la lista de **aplicaciones**, seleccione **AwareGo**.
1. En la sección **Administrar** de la página de información general de la aplicación, seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario** y, en el panel **Agregar asignación**, **Usuarios y grupos**.
1. En el panel **Usuarios y grupos**, en la lista **Usuarios** seleccione **B.Simon** y, a continuación, el botón **Seleccionar**.
1. Si desea asignar un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, se seleccionará el rol *Acceso predeterminado*.
1. En el panel **Agregar asignación**, seleccione el botón **Asignar**.

## <a name="configure-awarego-sso"></a>Configuración del inicio de sesión único de AwareGo

Para configurar el inicio de sesión único en el **AwareGo**, envíe el **certificado de (Base64)** que descargó y las direcciones URL que copió anteriormente de Azure Portal al [equipo de soporte técnico de AwareGo](mailto:support@awarego.com). El equipo de soporte técnico creará esta configuración para establecer la conexión de inicio de sesión único de SAML correctamente en ambos lados.

### <a name="create-an-awarego-test-user"></a>Creación de un usuario de prueba de AwareGo

En esta sección, creará un usuario llamado Britta Simon en AwareGo. Colabore con el [equipo de soporte técnico de AwareGo](mailto:support@awarego.com) para agregar los usuarios a la plataforma de AwareGo. Los usuarios deberán crearse y activarse antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, puede probar la configuración del inicio de sesión único de Azure AD mediante una de las siguientes acciones: 

* En Azure Portal, seleccione **Probar esta aplicación**. Esta acción le redirigirá a la página de inicio de sesión de AwareGo, desde donde puede comenzar el flujo de inicio de sesión. 

* Acceda directamente a la página de inicio de sesión de AwareGo y comience el flujo de inicio de sesión desde allí.

* Vaya a Mis aplicaciones de Microsoft. Al seleccionar el icono de **AwareGo** en Mis aplicaciones, se le redirigirá a la página de inicio de sesión de AwareGo. Para más información, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez haya configurado AwareGo, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del control de aplicaciones de acceso condicional. Para más información, consulte [Cómo aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).