---
title: 'Tutorial: Integración de Azure Active Directory con Freedcamp | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Freedcamp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: 88f942d48e99e33d72e2c207cd8cd48e10208057
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747893"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Tutorial: Integración de Freedcamp con Azure Active Directory

En este tutorial, aprenderá a integrar Freedcamp con Azure Active Directory (Azure AD). Cuando integre Freedcamp con Azure AD, podrá hacer lo siguiente:

* Controlar desde Azure AD quién tiene acceso a Freedcamp.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Freedcamp con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) de Freedcamp.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Freedcamp admite el SSO iniciado por **SP y IDP**.

## <a name="add-freedcamp-from-the-gallery"></a>Incorporación de Freedcamp desde la galería

Para poder configurar la integración de Freedcamp en Azure AD, es necesario que agregue Freedcamp a la lista de aplicaciones SaaS administradas desde la galería.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Freedcamp** en el cuadro de búsqueda.
1. Seleccione **Freedcamp** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-freedcamp"></a>Configuración y prueba del inicio de sesión único de Azure AD para Freedcamp

Configure y pruebe el inicio de sesión único de Azure AD con Freedcamp utilizando un usuario de prueba llamado **Britta Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Freedcamp.

Para configurar y probar el inicio de sesión único de Azure AD con Freedcamp, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Freedcamp](#configure-freedcamp-sso)** , para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Freedcamp](#create-freedcamp-test-user)** , para tener un homólogo de B. Simon en Freedcamp que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Freedcamp**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    1. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Los usuarios también pueden especificar los valores de las direcciones URL con arreglo a su propio dominio de cliente, por lo que no tienen que ajustarse necesariamente al patrón `freedcamp.com`, y escribir cualquier valor del dominio del cliente específico de la instancia de la aplicación. También puede ponerse en contacto con el [equipo de soporte técnico de Freedcamp](mailto:devops@freedcamp.com) si necesita más información sobre los patrones de las direcciones URL.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

   ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up Freedcamp** (Configurar Freedcamp), copie las direcciones URL adecuadas según sus necesidades.

   ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado Britta Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `Britta Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `BrittaSimon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que Britta Simon pueda acceder a Freedcamp utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Freedcamp**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En la lista de usuarios del cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-freedcamp-sso"></a>Configuración del inicio de sesión único en Freedcamp

1. Para automatizar la configuración en Freedcamp, debe instalar la **extensión de inicio de sesión seguro de Mis aplicaciones** en el explorador. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Setup Freedcamp** (Configurar Freedcamp) para acceder a la aplicación Freedcamp. Una vez allí, especifique las credenciales del administrador para iniciar sesión en Freedcamp. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 5.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Freedcamp manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Freedcamp como administrador y siga estos pasos:

4. En la esquina superior derecha de la página, haga clic en su **perfil** y vaya a **Mi cuenta**.

    ![Captura de pantalla que muestra "Profile"(Perfil) y "My Account" (Mi cuenta) seleccionados.](./media/freedcamp-tutorial/config01.png)

5. En la parte izquierda de la barra de menús, haga clic en **SSO** y, en la página **Your SSO connections** (Sus conexiones de SSO), siga estos pasos:

    ![Captura de pantalla que muestra "SSO" seleccionado en la barra de menús de la izquierda y la página "Your SSO connections" (Sus conexiones de SSO) con los valores especificados y el botón "Submit" (Enviar) seleccionado.](./media/freedcamp-tutorial/config02.png)

    a. En el cuadro de texto **Título**, escriba el nombre.

    b. En el cuadro de texto **Id. de entidad**, pegue el valor de **Identificador de Azure AD** que copió en Azure Portal.

    c. En el cuadro de texto **Dirección URL de inicio de sesión**, pegue el valor de **Dirección URL de inicio de sesión** que copió en Azure Portal.

    d. Abra el certificado codificado en Base64 con el Bloc de notas, copie su contenido y péguelo en el cuadro de texto **Certificado**.

    e. Haga clic en **Enviar**.

### <a name="create-freedcamp-test-user"></a>Creación de un usuario de prueba de Freedcamp

Para permitir que los usuarios de Azure AD puedan iniciar sesión en Freedcamp, deben estar aprovisionados en esta aplicación. En Freedcamp, el aprovisionamiento debe hacerse manualmente.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. En otra ventana del explorador web, inicie sesión en Freedcamp como administrador de seguridad.

2. En la esquina superior derecha de la página, haga clic en **Profile** (Perfil) y vaya a **Manage System** (Administrar sistema).

    ![Configuración de Freedcamp](./media/freedcamp-tutorial/config03.png)

3. En el lado derecho de la página Manage System (Administrar sistema), siga estos pasos:

    ![Captura de pantalla que muestra el botón "Add Or Invite Users" (Agregar o invitar usuarios) seleccionado, el campo "Email" (Correo electrónico) resaltado y el botón "Add User" (Agregar usuario) seleccionado.](./media/freedcamp-tutorial/config04.png)

    a. Haga clic en **Add or invite Users** (Agregar o invitar usuarios).

    b. En el cuadro de texto **Email** (Correo electrónico), escriba el correo electrónico del usuario; por ejemplo, `Brittasimon@contoso.com`.

    c. Haga clic en **Agregar usuario**.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Freedcamp, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Freedcamp e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Freedcamp para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Freedcamp en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Freedcamp para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Freedcamp, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).