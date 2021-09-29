---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Coupa Risk Assess | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Coupa Risk Assess.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: jeedes
ms.openlocfilehash: b9e0056360bc76a50d72f038e890943ab4be82c6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753801"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coupa-risk-assess"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Coupa Risk Assess

En este tutorial aprenderá a integrar Coupa Risk Assess con Azure Active Directory (Azure AD). Al integrar Coupa Risk Assess con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Coupa Risk Assess.
* Permitir que los usuarios inicien sesión automáticamente en Coupa Risk Assess con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Coupa Risk Assess.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Coupa Risk Assess admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="adding-coupa-risk-assess-from-the-gallery"></a>Incorporación de Coupa Risk Assess desde la galería

Para configurar la integración de Coupa Risk Assess en Azure AD, deberá agregar Coupa Risk Assess desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Coupa Risk Assess** en el cuadro de búsqueda.
1. Seleccione **Coupa Risk Assess** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-coupa-risk-assess"></a>Configuración y prueba del inicio de sesión único de Azure AD para Coupa Risk Assess

Configure y pruebe el inicio de sesión único de Azure AD con Coupa Risk Assess mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Coupa Risk Assess.

Para configurar y probar el inicio de sesión único de Azure AD con Coupa Risk Assess, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Coupa Risk Assess](#configure-coupa-risk-assess-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Coupa Risk Assess](#create-coupa-risk-assess-test-user)** : para tener un homólogo de B.Simon en Coupa Risk Assess vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Coupa Risk Assess**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente:
 
    * Para **Staging**: `https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Para **PROD**: `https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón:
 
    * Para **Staging**: `https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Para **PROD**: `https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **Dirección URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

    * Para **Staging**: `https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Para **PROD**: `https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico al cliente de Coupa Risk Assess](mailto:rasupport@coupa.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Coupa Risk Assess** (Configurar Coupa Risk Assess), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Coupa Risk Assess mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Coupa Risk Assess**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-coupa-risk-assess-sso"></a>Configuración del inicio de sesión único en Coupa Risk Assess

Para configurar el inicio de sesión único en **Coupa Risk Assess**, es preciso enviar el **XML de metadatos de federación** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de Coupa Risk Assess](mailto:rasupport@coupa.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-coupa-risk-assess-test-user"></a>Creación de un usuario de prueba en Coupa Risk Assess

En esta sección se crea un usuario llamado B.Simon en Coupa Risk Assess. Trabaje con el [equipo de soporte técnico de Coupa Risk Assess](mailto:rasupport@coupa.com) para agregar los usuarios a la plataforma de Coupa Risk Assess. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

1. Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Coupa Risk Assess, donde podrá iniciar el flujo de inicio de sesión.  

1. Vaya directamente a la dirección URL de inicio de sesión de Coupa Risk Assess e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Coupa Risk Assess para la que configurara el inicio de sesión único. 

También puede usar el Panel de acceso de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Coupa Risk Assess en el panel de acceso, si tiene la configuración del modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión y, si tiene la configuración del modo IDP, debería iniciar sesión automáticamente en la instancia de Coupa Risk Assess para la que configurara el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la aplicación Coupa Risk Assess, podrá aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).