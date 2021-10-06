---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Timeclock 365 SAML | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Timeclock 365 SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: b65a5cb4e202f2610613003b6d4339e0bb5354ce
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733774"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Timeclock 365 SAML

En este tutorial, aprenderá a integrar Timeclock 365 SAML con Azure Active Directory (Azure AD). Al integrar Timeclock 365 SAML con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Timeclock 365 SAML.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Timeclock 365 SAML con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Timeclock 365 SAML.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Timeclock 365 SAML admite el inicio de sesión único iniciado por **SP**.
* Timeclock 365 SAML admite el [aprovisionamiento de usuarios automatizado](timeclock-365-provisioning-tutorial.md).

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Adición de TimeClock 365 SAML desde la galería

Para configurar la integración de Timeclock 365 SAML en Azure AD, tiene que agregar Timeclock 365 SAML desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Timeclock 365 SAML** en el cuadro de búsqueda.
1. Seleccione **Timeclock 365 SAML** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Configuración y prueba del inicio de sesión único de Azure AD para Timeclock 365 SAML

Configure y pruebe el inicio de sesión único de Azure AD con Timeclock 365 SAML con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Timeclock 365 SAML.

Para configurar y probar el inicio de sesión único de Azure AD con Timeclock 365 SAML, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Timeclock 365 SAML](#configure-timeclock-365-saml-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Timeclock 365 SAML](#create-timeclock-365-saml-test-user)** , para tener un usuario equivalente a B.Simon en Timeclock 365 SAML que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Timeclock 365 SAML**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://live.timeclock365.com/login`


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

En esta sección, va a conceder a B.Simon acceso a Timeclock 365 SAML utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Timeclock 365 SAML**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-timeclock-365-saml-sso"></a>Configuración del inicio de sesión único de TimeClock 365 SAML

1. Para automatizar la configuración en Timeclock 365 SAML, debe instalar la **extensión del explorador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar Timeclock 365 SAML** para ir a la aplicación Timeclock 365 SAML. Desde allí, proporcione las credenciales de administrador para iniciar sesión en Timeclock 365 SAML. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 y 4.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Timeclock 365 SAML manualmente, en otra ventana del explorador web, inicie sesión en su sitio empresarial de Timeclock 365 SAML como administrador.

1. Complete los pasos mencionados a continuación.

    ![Configuración de Timeclock](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Vaya a la pestaña **Configuración > Perfil de la empresa > Configuración**.

    b. En el cuadro de texto **IDP metadata path** (Ruta de metadatos de IDP), pegue la **dirección URL de metadatos de federación de aplicación** que ha copiado de Azure Portal.

    c. A continuación, haga clic en **Actualizar**.

### <a name="create-timeclock-365-saml-test-user"></a>Creación de un usuario de prueba de Timeclock 365 SAML

1. Abra una nueva pestaña en el explorador e inicie sesión en el sitio de la empresa de Timeclock 365 SAML como administrador.

1. Vaya a **Usuarios > Agregar nuevo usuario**.

    ![Creación de un usuario de prueba 1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Proporcione toda la información necesaria en la página **Información de usuario** y haga clic en **Guardar**.

    ![Creación de un usuario de prueba 2 ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Haga clic en el botón **Crear** para crear el usuario de prueba.

> [!NOTE]
> Timeclock 365 SAML también admite el aprovisionamiento automático de usuarios. [Aquí](./timeclock-365-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurarlo.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Timeclock 365 SAML, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Timeclock 365 SAML e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Timeclock 365 SAML en Mis aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Timeclock 365 SAML. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Timeclock 365 SAML, puede aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).