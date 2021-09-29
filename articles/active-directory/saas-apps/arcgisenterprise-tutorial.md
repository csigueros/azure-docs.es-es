---
title: 'Tutorial: Integración de Azure Active Directory con ArcGIS Enterprise | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 3f9ae01dad8fb8c0764b3f70de8000c8857732ff
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796293"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutorial: Integración de Azure Active Directory con ArcGIS Enterprise

En este tutorial aprenderá a integrar ArcGIS Enterprise con Azure Active Directory (Azure AD). Al integrar ArcGIS Enterprise con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a ArcGIS Enterprise.
* Permitir que los usuarios inicien sesión automáticamente en ArcGIS Enterprise con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en ArcGIS Enterprise.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* ArcGIS Enterprise admite el inicio de sesión único iniciado por **SP e IDP**.
* ArcGIS Enterprise admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Adición de ArcGIS Enterprise desde la galería

Para configurar la integración de ArcGIS Enterprise en Azure AD, es preciso agregar dicha solución desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **ArcGIS Enterprise** en el cuadro de búsqueda.
1. Seleccione **ArcGIS Enterprise** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Configuración y prueba del inicio de sesión único de Azure AD para ArcGIS Enterprise

Configure y pruebe el inicio de sesión único de Azure AD con ArcGIS Enterprise mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de ArcGIS Enterprise.

Para configurar y probar el inicio de sesión único de Azure AD con ArcGIS Enterprise, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración de inicio de sesión único en ArcGIS Enterprise](#configure-arcgis-enterprise-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** : para tener un homólogo de B.Simon en ArcGIS Enterprise que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **ArcGIS Enterprise**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración de SAML básica**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por **IDP**:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `<EXTERNAL_DNS_NAME>.portal`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de ArcGIS Enterprise](mailto:support@esri.com) para obtener estos valores. El valor de Identificador se obtendrá en la **sección Set Identity Provider** (Definir proveedor de identidades), que se explica más adelante en este tutorial.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

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

En esta sección, va a permitir que B.Simon acceda a ArcGIS Enterprise mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **ArcGIS Enterprise**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-arcgis-enterprise-sso"></a>Configuración del inicio de sesión único de ArcGIS Enterprise

1. Para automatizar la configuración en ArcGIS Enterprise, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

1. Después de agregar la extensión al explorador, haga clic en **Set up ArcGIS Enterprise** (Configurar ArcGIS Enterprise) para ir a la aplicación del mismo nombre. Desde allí, proporcione las credenciales de administrador para iniciar sesión en ArcGIS Enterprise. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 7.

    ![Configuración](common/setup-sso.png)

1. Si desea configurar ArcGIS Enterprise manualmente, inicie sesión en el sitio de la empresa ArcGIS Enterprise como administrador.


1. Seleccione **Organization >EDIT SETTINGS** (Organización > Editar configuración).

    ![Captura de pantalla que muestra la pestaña Organization (Organización) ArcGIS Enterprise con la opción Edit settings (Editar configuración) seleccionada.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Seleccione la pestaña **Seguridad**.

    ![Captura de pantalla que muestra la pestaña Security (Seguridad) seleccionada.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Desplácese hacia abajo hasta la sección **Enterprise Logins via SAML** (Inicios de sesión empresariales mediante SAML) y seleccione **SET ENTERPRISE LOGIN** (Establecer inicio de sesión empresarial).

    ![Captura de pantalla que muestra inicios de sesión de empresa mediante SAML para seleccionar Set Enterprise Login (Establecer inicio de sesión de empresa).](./media/arcgisenterprise-tutorial/configure-3.png)

1. En la sección **Set Identity Provider** (Definición del proveedor de identidades), realice los siguientes pasos:

    ![Captura de pantalla que muestra Set Identity Provider (Establecer proveedor de identidades) para realizar los pasos descritos aquí.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Proporcione un nombre como **Azure Active Directory Test** (Prueba de Azure Active Directory) en el cuadro de texto **Name** (Nombre).

    b. En el cuadro de texto **URL**, pegue el valor de **Dirección URL de metadatos de federación de aplicación** que copió de Azure Portal.

    c. Haga clic en **Show advanced settings** (Mostrar configuración avanzada) y copie el valor de **Entity ID** (Id. de entidad) y péguelo en el cuadro de texto **Identificador** de la sección **Dominio y direcciones URL de ArcGIS Enterprise** de Azure Portal.

    ![Captura de pantalla que muestra dónde obtener el identificador de entidad y actualizar el proveedor de identidades.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Haga clic en **UPDATE IDENTITY PROVIDER** (Actualizar proveedor de identidades).

### <a name="create-arcgis-enterprise-test-user"></a>Creación de un usuario de prueba en ArcGIS Enterprise

En esta sección, se crea un usuario llamado a Britta Simon en ArcGIS Enterprise. ArcGIS Enterprise admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario deja de existir en ArcGIS Enterprise, se crea uno después de la autenticación.

> [!Note]
> Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de ArcGIS Enterprise](mailto:support@esri.com).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de ArcGIS Enterprise, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de ArcGIS Enterprise e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de ArcGIS Enterprise para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de ArcGIS Enterprise en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de ArcGIS Enterprise para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado ArcGIS Enterprise, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).