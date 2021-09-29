---
title: 'Tutorial: Integración de Azure Active Directory con Aha! | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Aha!.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 7c973bdb6fad6b7974e170d455c46a095b4a7754
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124822906"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Tutorial: Integración de Aha! con Azure Active Directory

En este tutorial, aprenderá a integrar Aha! con Azure Active Directory (Azure AD). La integración de Aha! con Azure AD permite realizar lo siguiente:

* Controlar en Azure AD quién tiene acceso a Aha!.
* Permitir que los usuarios inicien sesión automáticamente en Aha! con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Aha! : suscripción habilitada para el inicio de sesión único (SSO) en Aha!

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Aha! admite el inicio de sesión único iniciado por **SP**.
* Aha! admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-aha-from-the-gallery"></a>Incorporación de Aha! desde la galería

Para configurar la integración de Aha! en Azure AD, tiene que agregar Aha! desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Aha!** en el cuadro de búsqueda.
1. Seleccione **Aha!** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-aha"></a>Configuración y prueba del inicio de sesión único de Azure AD para Aha!

Configure y pruebe el inicio de sesión único de Azure AD con Aha! mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Aha!.

Para configurar y probar el inicio de sesión único de Azure AD con Aha! realice los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de Aha!](#configure-aha-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Aha!](#create-aha-test-user)** , para tener un homólogo de B.Simon en Aha! que esté vinculado a la representación de Azure AD del usuario.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones **Aha!** , busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.aha.io/session/new`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<companyname>.aha.io`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Aha! ](https://www.aha.io/company/contact) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Set up Aha!** (Configurar Aha!) , copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Aha! mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Aha!** .
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-aha-sso"></a>Configuración de Aha! SSO

1. Para automatizar la configuración en Aha!, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar Aha!** para ir a la aplicación Aha!. En ella, escriba las credenciales de administrador para iniciar sesión en Aha!. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 8.

    ![Configuración](common/setup-sso.png)

3. Si desea configurar Aha! manualmente, abra una nueva ventana del explorador web e inicie sesión en su sitio de la compañía de Aha! como administrador y realice los pasos siguientes:

4. En el menú de la parte superior, haga clic en **Configuración**.

    ![Configuración](./media/aha-tutorial/setting.png "Configuración")

5. Haga clic en **Cuenta**.

    ![Perfil](./media/aha-tutorial/account.png "Perfil")

6. Haga clic en **Seguridad e inicio de sesión único**.

    ![Captura de pantalla que resalta la opción de menú Seguridad e inicio de sesión único.](./media/aha-tutorial/security.png "Seguridad e inicio de sesión único")

7. En la sección **Inicio de sesión único**, en **Proveedor de identidades**, seleccione **SAML2.0**.

    ![Seguridad e inicio de sesión único](./media/aha-tutorial/saml.png "Seguridad e inicio de sesión único")

8. Siga estos pasos en la página de configuración **Inicio de sesión único** :

    ![Inicio de sesión único](./media/aha-tutorial/sso.png "Inicio de sesión único")

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración.

    b. En **Configurar con**, seleccione **Archivo de metadatos**.

    c. Para cargar el archivo de metadatos descargado, haga clic en **Examinar**.

    d. Haga clic en **Update**(Actualizar).

### <a name="create-aha-test-user"></a>Creación de un usuario de prueba de Aha!

En esta sección, se crea un usuario llamado B.Simon en Aha!. Aha! admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario deja de existir en Aha!, se crea otro después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a Aha! Dirección URL de inicio de sesión donde puede iniciar el flujo de inicio de sesión. 

* Ir a Aha! Inicie sesión directamente en la dirección URL e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Cuando hace clic en el icono de Aha! en Aplicaciones, se le redirigirá a Aha! URL de inicio de sesión. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Aha! puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).