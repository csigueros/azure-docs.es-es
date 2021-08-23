---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Serraview Space Utilization Software Solutions | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Serraview Space Utilization Software Solutions.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: a93cc04d7172432f223cd0ae5553c374778fd11b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112292646"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-serraview-space-utilization-software-solutions"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Serraview Space Utilization Software Solutions

En este tutorial aprenderá a integrar Serraview Space Utilization Software Solutions con Azure Active Directory (Azure AD). Al integrar Serraview Space Utilization Software Solutions con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Serraview Space Utilization Software Solutions.
* Permitir que los usuarios inicien sesión automáticamente en Serraview Space Utilization Software Solutions con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) de Serraview Space Utilization Software Solutions.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Serraview Space Utilization Software Solutions admite inicio de sesión único iniciado por **SP e IDP**.

## <a name="add-serraview-space-utilization-software-solutions-from-the-gallery"></a>Incorporación de Serraview Space Utilization Software Solutions desde la galería

Para configurar la integración de Serraview Space Utilization Software Solutions en Azure AD, es preciso agregar Serraview Space Utilization Software Solutions desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Serraview Space Utilization Software Solutions** en el cuadro de búsqueda.
1. Seleccione **Serraview Space Utilization Software Solutions** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-serraview-space-utilization-software-solutions"></a>Configuración y prueba del inicio de sesión único de Azure AD para Serraview Space Utilization Software Solutions

Configure y pruebe el inicio de sesión único de Azure AD con Serraview Space Utilization Software Solutions mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Serraview Space Utilization Software Solutions.

Para configurar y probar el inicio de sesión único de Azure AD con Serraview Space Utilization Software Solutions, es preciso realizar los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Serraview Space Utilization Software Solutions](#configure-serraview-space-utilization-software-solutions-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación del usuario de prueba de Serraview Space Utilization Software Solutions](#create-serraview-space-utilization-software-solutions-test-user)** : para tener un homólogo de B.Simon en Serraview Space Utilization Software Solutions que esté vinculado a la representación de Azure AD de usuario.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Serraview Space Utilization Software Solutions**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba un valor con el siguiente patrón: `urn:Serraview:<SERRAVIEW_IDENTIFIER>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.serraview.com/SAML/AssertionConsumerService.aspx`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.serraview.com/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el equipo de soporte técnico de [Serraview Space Utilization Software Solutions](mailto:svprodops@serraview.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar Serraview Space Utilization Software Solutions**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección va a permitir que B.Simon acceda a Serraview Space Utilization Software Solutions mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Serraview Space Utilization Software Solutions**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-serraview-space-utilization-software-solutions-sso"></a>Configuración del inicio de sesión único de Serraview Space Utilization Software Solutions

Para configurar el inicio de sesión único en **Serraview Space Utilization Software Solutions**, es preciso enviar el **XML de metadatos de federación** descargado y las direcciones URL adecuadas copiadas desde Azure Portal al [equipo de soporte técnico de Serraview Space Utilization Software Solutions](mailto:svprodops@serraview.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-serraview-space-utilization-software-solutions-test-user"></a>Creación del usuario de prueba de Serraview Space Utilization Software Solutions

En esta sección, creará un usuario llamado B.Simon en Serraview Space Utilization Software Solutions. Trabaje con el [equipo de soporte técnico de Serraview Space Utilization Software Solutions](mailto:svprodops@serraview.com) para agregar usuarios a la plataforma de Serraview Space Utilization Software Solutions. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Serraview Space Utilization Software Solutions, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Serraview Space Utilization Software Solutions e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal, y debería iniciarse sesión automáticamente en la instancia de Serraview Space Utilization Software Solutions para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Serraview Space Utilization Software Solutions en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Serraview Space Utilization Software Solutions para la que haya configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Serraview Space Utilization Software Solutions, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
