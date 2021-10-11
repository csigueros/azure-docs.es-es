---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Zero Networks'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zero Networks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: 533e1dee16a303931987bc83f15f929d89d38b4f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669381"
---
# <a name="tutorial-azure-ad-sso-integration-with-zero-networks"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Zero Networks

En este tutorial aprenderá a integrar Zero Networks con Azure Active Directory (Azure AD). Al integrar Zero Networks con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Zero Networks.
* Permitir que los usuarios inicien sesión automáticamente en Zero Networks con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único en Zero Networks.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, configurará el inicio de sesión único de Azure AD para el portal de administración y de acceso de Zero Networks.

* Zero Networks admite el inicio de sesión único iniciado por **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-zero-networks-from-the-gallery"></a>Incorporación de Zero Networks desde la galería

Para configurar la integración de Zero Networks en Azure AD, es preciso agregar Zero Networks desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta profesional de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Zero Networks** en el cuadro de búsqueda.
1. Seleccione **Zero Networks** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Zero Networks**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga el paso a continuación.

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL: `https://portal.zeronetworks.com/#/login`

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up Zero Networks** (Configurar Zero Networks), copie las direcciones URL que necesite.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

## <a name="configure-zero-networks-sso"></a>Configuración del inicio de sesión único en Zero Networks

1. Inicie sesión en el Portal de administración de Zero Networks como administrador.

1. Vaya a **Settings** > **Identity Providers** (Configuración > Proveedores de identidades).

1. Haga clic en **Microsoft Azure** y siga los pasos a continuación.
    
    ![Captura de pantalla que muestra la configuración del inicio de sesión único.](./media/zero-networks-tutorial/settings.png "Cuenta")

    1. Copie el valor del **identificador de entidad** y péguelo en el cuadro de texto **Identificador** de la sección **Configuración básica de SAML** en Azure Portal.

    1. Copie el valor de **Reply URL (Assertion Consumer Service URL)** (Dirección URL de respuesta [URL del Servicio de consumidor de aserciones]) y péguelo en el cuadro **URL de respuesta**, en la sección **Configuración básica de SAML** de Azure Portal.

    1. En el cuadro de texto **Login URL** (Dirección URL de inicio de sesión), pegue el valor de **Dirección URL de inicio de sesión** que copió de Azure Portal.

    1. En el cuadro de texto **Logout URL** (Dirección URL de cierre de sesión), pegue el valor de **Logout URL** (Dirección URL de cierre de sesión) que copió de Azure Portal.

    1. En el Bloc de notas abra el archivo del **certificado (Base64)** que descargó de Azure Portal, copie el contenido y péguelo en el cuadro de texto **Certificate(Base64)** (Certificado [Base64]).

    1. Haga clic en **Guardar**.

## <a name="configure-user-assignment-requirement"></a>Configuración del requisito de asignación de usuarios

1. En Azure Portal, en la página de integración de la aplicación **Zero Networks**, busque la sección **Administrar** y seleccione **Propiedades**.
1. Establezca la opción **Asignación de usuarios necesaria** en **No**.

![Captura de pantalla para la opción de asignación de usuarios necesaria.](./media/zero-networks-tutorial/user-assignment.png)

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Zero Networks, donde podrá iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Zero Networks e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Zero Networksen Aplicaciones, se le redirigirá a la URL de inicio de sesión de esa plataforma. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).
