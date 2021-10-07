---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Sigma Computing | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Sigma Computing.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 6486445fcda372d76686fef26c63d3c07e03ffbd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733788"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sigma-computing"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Sigma Computing

En este tutorial, aprenderá a integrar Sigma Computing con Azure Active Directory (Azure AD). Al integrar Sigma Computing con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Sigma Computing.
* Permitir que los usuarios inicien sesión automáticamente en Sigma Computing con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Sigma Computing.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Sigma Computing admite el inicio de sesión único iniciado por **SP e IDP**.
* Sigma Computing admite el aprovisionamiento de usuarios **Just-In-Time**.
* Sigma Computing admite el [aprovisionamiento automatizado de usuarios](sigma-computing-provisioning-tutorial.md).

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-sigma-computing-from-the-gallery"></a>Adición de Sigma Computing desde la galería

Para configurar la integración de Sigma Computing en Azure AD, tendrá que agregar Sigma Computing desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Sigma Computing** en el cuadro de búsqueda.
1. Seleccione **Sigma Computing** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sigma-computing"></a>Configuración y prueba del inicio de sesión único de Azure AD para Sigma Computing

Configure y pruebe el inicio de sesión único de Azure AD con Sigma Computing mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Sigma Computing.

Para configurar y probar el inicio de sesión único de Azure AD con Sigma Computing, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Sigma Computing](#configure-sigma-computing-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Sigma Computing](#create-sigma-computing-test-user)** : para tener un homólogo de B.Simon en Sigma Computing que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Sigma Computing**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, el usuario no tiene que realizar ningún paso porque la aplicación ya se ha integrado previamente con Azure.

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

    | URL de inicio de sesión |
    |------------|
    | `https://app.sigmacomputing.com/<CustomerOrg>`|
    | `https://aws.sigmacomputing.com/<CustomerOrg>`|

    > [!NOTE]
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte al cliente de Sigma Computing](mailto:support@sigmacomputing.com) para obtener el valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Sigma Computing**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Sigma Computing utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Sigma Computing**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sigma-computing-sso"></a>Configuración del inicio de sesión único de Sigma Computing

1. Inicie sesión en su cuenta de Sigma.

1. Vaya al **portal de administración**, para lo que debe seleccionar **Administration** (Administración) en el menú de usuario.

1. Realice en la página siguiente los pasos que se indican a continuación.

    ![Sección Configuración del inicio de sesión único de Sigma Computing](./media/sigma-computing-tutorial/authentication.png)

    a. Seleccione la página **Authentication** (Autenticación) en el panel izquierdo.

    b. En **Authentication Method** (Método de autenticación), seleccione **SAML** o **SAML or password** (SAML o contraseña).

    c. En el cuadro de texto **Identity Provider Login URL** (Dirección URL de inicio de sesión único del proveedor de identidades), pegue el valor de **Dirección URL de inicio de sesión**  que ha copiado de Azure Portal.

    d. Abra en el Bloc de notas el archivo del **certificado (Base 64)** que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **Identity Provider X509 certificate** (Certificado X509 del proveedor de identidades).

    e.  Haga clic en **Guardar**.

### <a name="create-sigma-computing-test-user"></a>Creación de un usuario de prueba de Sigma Computing

En esta sección, se crea un usuario llamado a Britta Simon en Sigma Computing. Sigma Computing admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Sigma Computing, se creará uno después de la autenticación.

Sigma Computing también admite el aprovisionamiento automático de usuarios. [Aquí](./sigma-computing-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Sigma Computing, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Sigma Computing e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Sigma Computing para la que haya configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Sigma Computing en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Sigma Computing para la que haya configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Sigma Computing, puede aplicar el control de sesión, que protege a la organización frente a la filtración e infiltración de información confidencial en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).