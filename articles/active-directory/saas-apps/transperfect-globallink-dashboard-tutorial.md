---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con TransPerfect GlobalLink Dashboard | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TransPerfect GlobalLink Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 62d009490ca321db2bf511168f4fa5ed0337cf17
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737860"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-transperfect-globallink-dashboard"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con TransPerfect GlobalLink Dashboard

En este tutorial, aprenderá a integrar TransPerfect GlobalLink Dashboard con Azure Active Directory (Azure AD). Al integrar TransPerfect GlobalLink Dashboard con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a TransPerfect GlobalLink Dashboard.
* Permitir que los usuarios inicien sesión automáticamente en TransPerfect GlobalLink Dashboard con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en TransPerfect GlobalLink Dashboard.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* TransPerfect GlobalLink Dashboard admite el inicio de sesión único iniciado por **SP e IDP**.
* TransPerfect GlobalLink Dashboard admite el aprovisionamiento de usuarios **Just In Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-transperfect-globallink-dashboard-from-the-gallery"></a>Adición de TransPerfect GlobalLink Dashboard desde la galería

Para configurar la integración de TransPerfect GlobalLink Dashboard en Azure AD, deberá agregar TransPerfect GlobalLink Dashboard desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **TransPerfect GlobalLink Dashboard** en el cuadro de búsqueda.
1. Seleccione **TransPerfect GlobalLink Dashboard** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-transperfect-globallink-dashboard"></a>Configuración y prueba del inicio de sesión único de Azure AD para TransPerfect GlobalLink Dashboard

Configure y pruebe el inicio de sesión único de Azure AD con TransPerfect GlobalLink Dashboard con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de TransPerfect GlobalLink Dashboard.

Para configurar y probar el inicio de sesión único de Azure AD con TransPerfect GlobalLink Dashboard, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de TransPerfect GlobalLink Dashboard](#configure-transperfect-globallink-dashboard-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de TransPerfect GlobalLink Dashboard](#create-transperfect-globallink-dashboard-test-user)** : para tener un homólogo de B.Simon en TransPerfect GlobalLink Dashboard que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **TransPerfect GlobalLink Dashboard**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, el usuario no tiene que realizar ningún paso porque la aplicación ya se ha integrado previamente con Azure.

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://sso.transperfect.com`

1. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)
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

En esta sección, va a permitir que B.Simon acceda a TransPerfect GlobalLink Dashboard mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **TransPerfect GlobalLink Dashboard**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-transperfect-globallink-dashboard-sso"></a>Configuración del inicio de sesión único de TransPerfect GlobalLink Dashboard

Para configurar el inicio de sesión único en **TransPerfect GlobalLink Dashboard**, debe enviar la **dirección URL de metadatos de federación de la aplicación** al [equipo de soporte técnico de TransPerfect GlobalLink Dashboard](mailto:TechOps_Consulting@transperfect.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-transperfect-globallink-dashboard-test-user"></a>Creación de un usuario de prueba de TransPerfect GlobalLink Dashboard

En esta sección, se crea un usuario llamado B.Simon en TransPerfect GlobalLink Dashboard. TransPerfect GlobalLink Dashboard admite el aprovisionamiento Just-in-Time, que está habilitado de manera predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario ya no existe en TransPerfect GlobalLink Dashboard, se crea uno nuevo cuando se intenta acceder a TransPerfect GlobalLink Dashboard.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de TransPerfect GlobalLink Dashboard, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de TransPerfect GlobalLink Dashboard e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de TransPerfect GlobalLink Dashboard para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de TransPerfect GlobalLink Dashboard en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de TransPerfect GlobalLink Dashboard para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado TransPerfect GlobalLink Dashboard, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).