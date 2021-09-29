---
title: 'Tutorial: Integración de Azure Active Directory con LaunchDarkly | Microsoft Docs'
description: Obtenga información para configurar el inicio de sesión único entre Azure Active Directory y LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 8831666015663eedd3ab779cd23b58bb8cf0df24
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739146"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Tutorial: Integración de Azure Active Directory con LaunchDarkly

En este tutorial, aprenderá a integrar LaunchDarkly con Azure Active Directory (Azure AD). Al integrar LaunchDarkly con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a LaunchDarkly.
* Permitir que los usuarios inicien sesión automáticamente en LaunchDarkly con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

    > [!NOTE]
    > La integración de Azure Active Directory y LaunchDarkly es unidireccional. Después de configurar la integración, puede usar Azure AD para administrar usuarios, el inicio de sesión único y cuentas en LaunchDarkly, pero **no** puede usar LaunchDarkly para hacer eso mismo en Azure.

## <a name="prerequisites"></a>Prerrequisitos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único en LaunchDarkly.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* LaunchDarkly admite el inicio de sesión único mediante **SP e IDP**.
* LaunchDarkly admite el aprovisionamiento de usuarios **Just-In-Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-launchdarkly-from-the-gallery"></a>Adición de LaunchDarkly desde la galería

Para configurar la integración de LaunchDarkly en Azure AD, debe agregar LaunchDarkly desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **LaunchDarkly** en el cuadro de búsqueda.
1. Seleccione **LaunchDarkly** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Configuración y prueba del inicio de sesión único de Azure AD en LaunchDarkly

Configure y pruebe el inicio de sesión único de Azure AD con LaunchDarkly mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de LaunchDarkly.

Para configurar y probar el inicio de sesión único de Azure AD con LaunchDarkly, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en LaunchDarkly](#configure-launchdarkly-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de LaunchDarkly](#create-launchdarkly-test-user)** : para tener un homólogo de B.Simon en LaunchDarkly que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **LaunchDarkly**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `app.launchdarkly.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. El valor se actualizará con la dirección URL de respuesta real, que se explica más adelante en el tutorial. Si intenta usar la aplicación en el modo **IDP**, debe dejar en blanco el campo **Dirección URL de inicio de sesión**; de lo contrario, no podrá empezar el inicio de sesión desde **IDP**. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Set up LaunchDarkly** (Configurar LaunchDarkly), copie las direcciones URL adecuada según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a LaunchDarkly mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **LaunchDarkly**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-launchdarkly-sso"></a>Configuración del inicio de sesión único de LaunchDarkly

1. En otra ventana del explorador web, inicie sesión en el sitio de LaunchDarkly de la compañía como administrador.

2. Seleccione **Configuración de cuenta** en el panel de navegación izquierdo.

    ![Captura de pantalla que muestra el elemento Account Settings (Configuración de la cuenta) seleccionado en Production (Producción).](./media/launchdarkly-tutorial/configure-1.png)

3. Haga clic en la pestaña **Seguridad**.

    ![Captura de pantalla que muestra la pestaña Security (Seguridad) en Account Settings (Configuración de la cuenta).](./media/launchdarkly-tutorial/configure-2.png)

4. Haga clic en **HABILITAR SSO** y luego en **EDITAR CONFIGURACIÓN DE SAML**.

    ![Captura de pantalla que muestra la página de inicio de sesión único, donde puede habilitar el inicio de sesión único y editar la configuración de SAML.](./media/launchdarkly-tutorial/configure-3.png)

5. En la sección **Editar configuración de SAML**, realice los siguientes pasos:

    ![Captura de pantalla que muestra la sección Edit your SAML configuration (Editar configuración de SAML), donde puede realizar los cambios que se describen aquí.](./media/launchdarkly-tutorial/configure-4.png)

    a. Copie la **dirección URL del servicio del consumidor de SAML** de la instancia y péguela en el cuadro de texto URL de respuesta de la sección **Dominio y direcciones URL de LaunchDarkly** de Azure Portal.

    b. En el cuadro de texto **Dirección URL de inicio de sesión**, pegue el valor de la **URL de inicio de sesión** que ha copiado de Azure Portal.

    c. Abra el certificado descargado desde Azure Portal en el Bloc de notas, copie el contenido y luego péguelo en el cuadro **Certificado X.509** o cárguelo directamente al hacer clic **Cargar**.

    d. Haga clic en **Save**(Guardar).

### <a name="create-launchdarkly-test-user"></a>Creación de un usuario de prueba de LaunchDarkly

En esta sección, se va a crear un usuario llamado B.Simon en LaunchDarkly. LaunchDarkly admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si aún no existe un usuario en LaunchDarkly, se crea uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de LaunchDarkly para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de LaunchDarkly en Aplicaciones, debería iniciar sesión automáticamente en la versión de LaunchDarkly para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que se ha configurado LaunchDarkly, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).