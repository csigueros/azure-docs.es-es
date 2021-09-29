---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Kendis - Azure AD Integration | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Kendis - Azure AD Integration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: jeedes
ms.openlocfilehash: b3316e755b57baf6c877d36b6c82d683298dfa7c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124822089"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis---azure-ad-integration"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Kendis - Azure AD Integration

En este tutorial, aprenderá a integrar Kendis - Azure AD Integration con Azure Active Directory (Azure AD). Al integrar Kendis - Azure AD Integration con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Kendis - Azure AD Integration.
* Permitir que los usuarios inicien sesión automáticamente en Kendis - Azure AD Integration con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Kendis - Azure AD Integration.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Kendis - Azure AD Integration admite el inicio de sesión único iniciado por **SP e IDP**.
* Kendis - Azure AD Integration admite el aprovisionamiento de usuarios **Just-In-Time**.


## <a name="adding-kendis---azure-ad-integration-from-the-gallery"></a>Incorporación de Kendis - Azure AD Integration desde la galería

Para configurar la integración de Kendis - Azure AD Integration en Azure AD, es preciso agregar Kendis - Azure AD Integration desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Kendis - Azure AD Integration** en el cuadro de búsqueda.
1. Seleccione **Kendis - Azure AD Integration** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-kendis---azure-ad-integration"></a>Configuración y prueba del inicio de sesión único de Azure AD para Kendis - Azure AD Integration

Configure y pruebe el inicio de sesión único de Azure AD con Kendis - Azure AD Integration mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Kendis - Azure AD Integration.

Para configurar y probar el inicio de sesión único de Azure AD con Kendis - Azure AD Integration, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Kendis - Azure AD Integration](#configure-kendis-azure-ad-integration-sso)** : para configurar los valores de inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba de Kendis - Azure AD Integration](#create-kendis-azure-ad-integration-test-user)** : para tener un homólogo de B.Simon en Kendis - Azure AD Integration vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Kendis - Azure AD Integration**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<SUBDOMAIN>.kendis.io`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico para clientes de Kendis - Azure AD Integration](mailto:support@kendis.io) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Kendis - Azure AD Integration**, copie las direcciones URL adecuadas para sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Kendis - Azure AD Integration mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Kendis - Azure AD Integration**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-kendis-azure-ad-integration-sso"></a>Configuración del inicio de sesión único de Kendis - Azure AD Integration

1. Para automatizar la configuración en la integración de Kendis y Azure AD, debe instalar la **extensión del navegador de inicio de sesión seguro de Aplicaciones**; para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar la integración de Kendis y Azure AD** para ir a la aplicación de integración de Kendis y Azure AD. Desde allí, proporcione las credenciales de administrador para iniciar sesión en la integración de Kendis y Azure AD. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 5.

    ![Configuración](common/setup-sso.png)

3. Si desea configurar la integración de Kendis y Azure AD manualmente, en otra ventana del explorador Web, inicie sesión en el sitio de la compañía de la integración de Kendis y Azure AD como administrador.

4. Vaya a **Configuración > Configuraciones de SAML**.

    ![Configuración en Configuraciones de SAML](./media/kendis-scaling-agile-platform-tutorial/settings.png)

5. Haga clic en el botón **Editar** en la parte inferior de la página y realice los pasos siguientes.

    ![Configuraciones de SAML](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Copie el valor de **URL devolución de llamada**, péguelo en el cuadro de texto **Dirección URL de respuesta**, en la sección Configuración básica de SAML de Azure Portal.

    b. En el cuadro de texto **Dirección URL del inicio de sesión único del proveedor de identidades**, pegue el valor de la **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c.  En el cuadro de texto **Emisor de proveedor de identidades**, pegue el valor de **Identificador de Azure AD (Identificador de entidad)** que copió de Azure Portal.

    d. Abra en el Bloc de notas el archivo del **certificado (Base64)** que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **Certificate X.509** (Certificado).

    e. **Seleccione el grupo predeterminado** en la lista de opciones. 

    f. Haga clic en **Save**(Guardar).

### <a name="create-kendis-azure-ad-integration-test-user"></a>Creación de un usuario de prueba de Kendis - Azure AD Integration

En esta sección, se crea un usuario llamado Britta Simon en Kendis - Azure AD Integration. Kendis - Azure AD Integration admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario no existe en Kendis - Azure AD Integration, se creará uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Kendis - Azure AD Integration, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya a la URL de inicio de sesión de Kendis - Azure AD Integration directamente y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Debería iniciarse sesión automáticamente en la instancia de Kendis - Azure AD Integration para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Kendis - Azure AD Integration en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para poner en marcha el flujo de inicio de sesión y, si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Kendis - Azure AD Integration para la que configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la integración de Kendis - Azure AD Integration, puede aplicar el control de sesión, que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).