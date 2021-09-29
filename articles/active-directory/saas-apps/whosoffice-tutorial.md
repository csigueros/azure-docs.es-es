---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con WhosOffice | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y WhosOffice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 69cbd4007754199cd35cd8982bfd2237f5b15e43
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124745803"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosoffice"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con WhosOffice

En este tutorial, aprenderá a integrar WhosOffice con Azure Active Directory (Azure AD). Al integrar WhosOffice con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a WhosOffice.
* Permitir que los usuarios inicien sesión automáticamente en WhosOffice con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en WhosOffice.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* WhosOffice admite el inicio de sesión único iniciado por **SP e IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-whosoffice-from-the-gallery"></a>Adición de WhosOffice desde la galería

Para configurar la integración de WhosOffice en Azure AD, deberá agregar WhosOffice desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **WhosOffice** en el cuadro de búsqueda.
1. Seleccione **WhosOffice** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-whosoffice"></a>Configuración y prueba del inicio de sesión único de Azure AD para WhosOffice

Configure y pruebe el inicio de sesión único de Azure AD con WhosOffice mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de WhosOffice.

Para configurar y probar el inicio de sesión único de Azure AD con WhosOffice, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en WhosOffice](#configure-whosoffice-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de WhosOffice](#create-whosoffice-test-user)** : para tener un homólogo de B.Simon en WhosOffice que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **WhosOffice**, busque la sección **Administrar** y seleccione **inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o del lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.my.whosoffice.com/int/azure/consume.aspx`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.my.whosoffice.com/int/azure`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico al cliente de WhosOffice](mailto:support@whosoffice.com). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar WhosOffice**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a WhosOffice mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **WhosOffice**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-whosoffice-sso"></a>Configuración del inicio de sesión único de WhosOffice

1. Para automatizar la configuración en WhosOffice, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Setup WhosOffice** (Configurar WhosOffice) para acceder a la aplicación WhosOffice. Especifique ahí las credenciales de administrador para iniciar sesión en esta aplicación. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 7.

    ![Configuración](common/setup-sso.png)

3. Si desea configurar WhosOffice manualmente, en otra ventana del explorador web, inicie sesión en el sitio de la empresa WhosOffice como administrador.

1. Haga clic en **Settings** (Configuración) y seleccione **Company** (Empresa).

    ![Captura de pantalla que muestra la empresa seleccionada en la configuración.](./media/whosoffice-tutorial/configuration1.png)

1. Haga clic en **Apps/Integrations** (Aplicaciones/Integraciones).

    ![Captura de pantalla que muestra las aplicaciones o integraciones seleccionadas en la configuración de la empresa.](./media/whosoffice-tutorial/configuration2.png)

1. Seleccione **Microsoft Azure** en la lista desplegable de proveedores y haga clic en **Activate Login Provider** (Activar proveedor de inicio de sesión).

    ![Captura de pantalla que muestra la opción de activación de proveedor de inicio de sesión seleccionada para Microsoft Azure.](./media/whosoffice-tutorial/configuration3.png)

1. Cargue el archivo de metadatos de federación descargado de Azure Portal; para ello, haga clic en la opción **Upload** (Cargar).
    
    ![Captura de pantalla que muestra la opción de carga para un archivo de metadatos.](./media/whosoffice-tutorial/configuration4.png)

### <a name="create-whosoffice-test-user"></a>Creación de un usuario de prueba de WhosOffice

1. En otra ventana del explorador web, inicie sesión en el sitio web de WhosOffice como administrador.

1. Haga clic en **Settings** (Configuración) y seleccione **Users** (Usuarios).

    ![Captura de pantalla que muestra la opción de usuarios seleccionada en la configuración.](./media/whosoffice-tutorial/user1.png)

1. Seleccione **Create new User** (Crear nuevo usuario).

    ![Captura de pantalla que muestra la opción de creación de nuevo usuario seleccionada.](./media/whosoffice-tutorial/user2.png)

1. Proporcione los detalles necesarios del usuario según los requisitos de la organización.

    ![Captura de pantalla muestra el cuadro de diálogo nuevo usuario, donde puede especificar los datos de usuario.](./media/whosoffice-tutorial/user3.png)

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de WhosOffice, donde puede iniciar el flujo de inicio de sesión.

* Vaya directamente a la dirección URL de inicio de sesión de WhosOffice e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de WhosOffice para la que haya configurado el inicio de sesión único.

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de WhosOffice en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de WhosOffice para la que haya configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado WhosOffice, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).