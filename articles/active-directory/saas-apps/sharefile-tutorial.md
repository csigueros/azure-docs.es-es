---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Citrix ShareFile'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jeedes
ms.openlocfilehash: 2f876948276d69e859d68bc2cc4a779ccdd78803
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669823"
---
# <a name="tutorial-azure-ad-sso-integration-with-citrix-sharefile"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Citrix ShareFile

En este tutorial, aprenderá a integrar Citrix ShareFile con Azure Active Directory (Azure AD). Al integrar Citrix ShareFile con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Citrix ShareFile.
* Permitir que los usuarios inicien sesión automáticamente en Citrix ShareFile con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Citrix ShareFile.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Citrix ShareFile admite el inicio de sesión único iniciado por **SP**.

## <a name="add-citrix-sharefile-from-the-gallery"></a>Incorporación de Citrix ShareFile desde la galería

Para configurar la integración de Citrix ShareFile en Azure AD, deberá agregar Citrix ShareFile desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Citrix ShareFile** en el cuadro de búsqueda.
1. Seleccione **Citrix ShareFile** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Configuración y prueba del inicio de sesión único de Azure AD para Citrix ShareFile

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Citrix ShareFile con un usuario de prueba llamado **Britta Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Citrix ShareFile.

Para configurar y probar el inicio de sesión único de Azure AD con Citrix ShareFile, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de Citrix ShareFile](#configure-citrix-sharefile-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Citrix ShareFile](#create-citrix-sharefile-test-user)**: para tener un homólogo de Britta Simon en Citrix ShareFile que esté vinculado a la representación del usuario en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Citrix ShareFile**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos: 

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con uno de los patrones siguientes:

    | **Identificador** |
    |--------|
    | `https://<tenant-name>.sharefile.com` |
    | `https://<tenant-name>.sharefile.com/saml/info` |
    | `https://<tenant-name>.sharefile1.com/saml/info` |
    | `https://<tenant-name>.sharefile1.eu/saml/info` |
    | `https://<tenant-name>.sharefile.eu/saml/info` |

    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con uno de los siguientes patrones:
    
    | **URL de respuesta** |
    |-------|
    | `https://<tenant-name>.sharefile.com/saml/acs` |
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>` |
    | `https://<tenant-name>.sharefile.com/saml/<URL path>` |

    c. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.sharefile.com/saml/login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up Citrix ShareFile** (Configurar Citrix ShareFile), copie las direcciones URL que necesite.

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

En esta sección, va a conceder a B.Simon acceso a Citrix ShareFile utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Citrix ShareFile**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-citrix-sharefile-sso"></a>Configuración del inicio de sesión único de Citrix ShareFile

1. Para automatizar la configuración en **Citrix ShareFile**, debe instalar la **extensión del navegador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instale la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al navegador, haga clic en **Set up Citrix ShareFile** (Configurar Citrix ShareFile) para ir a la aplicación del mismo nombre. Desde aquí, escriba las credenciales de administrador para iniciar sesión en Citrix ShareFile. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 7.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Citrix ShareFile manualmente, en otra ventana del explorador web, inicie sesión en el sitio de la empresa Citrix ShareFile como administrador.

1. En **Dashboard** (Panel), haga clic en **Settings** (Configuración) y seleccione **Admin Settings** (Configuración de administración).

    ![Administración](./media/sharefile-tutorial/settings.png)

1. En la configuración de administración, vaya a **Security (Seguridad)**  -> **Login & Security Policy** (Inicio de sesión y directiva de seguridad).
   
    ![Account Administration (Administración de cuentas)](./media/sharefile-tutorial/settings-security.png "Administración de cuentas")

1. En la página de diálogo **Configuración de Inicio de sesión único/SAML 2.0**, en **Configuración básica**, siga estos pasos:
   
    ![Inicio de sesión único](./media/sharefile-tutorial/saml-configuration.png "Inicio de sesión único")
   
    a. Seleccione **YES** (SÍ) en **Enable SAML** (Habilitar SAML).

    b. Copie el valor de **ShareFile Issuer/ Entity ID** (Emisor/Id. de entidad de ShareFile) y péguelo en el cuadro **URL del identificador** del cuadro de diálogo **Configuración básica de SAML** de Azure Portal.
    
    c. En el cuadro de texto **Your IDP Issuer/Entity ID** (Emisor del IDP/Identificador de entidad), pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    d. Haga clic en **Cambiar** junto al campo **Certificado X.509** y luego cargue el certificado que descargó desde Azure Portal.
    
    e. En el cuadro de texto **Dirección URL de inicio de sesión**, pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.
    
    f. En el cuadro de texto **URL de cierre de sesión**, pegue el valor de **Sign-Out URL** (Dirección URL de cierre de sesión) que copió de Azure Portal.

    g. En **Optional Settings** (Configuración opcional), elija para **SP-Initiated Auth Context** (Contexto de autenticación iniciado por SP) las opciones **User Name and Password** (Nombre de usuario y contraseña) y **Exact** (Exacto).

5. Haga clic en **Guardar**.

## <a name="create-citrix-sharefile-test-user"></a>Creación de un usuario de prueba de Citrix ShareFile

1. Inicie sesión en el inquilino de **Citrix ShareFile**.

2. Haga clic en **People (Personas)**  -> **Manage Users Home (Administrar página de inicio de usuarios)**  -> **Create New Users (Crear nuevos usuarios)**  -> **Create Employee (Crear empleado)** .
   
    ![Create Employee (Crear empleado)](./media/sharefile-tutorial/create-user.png "Crear empleado")

3. En la sección **Basic Information** (Información básica), siga los siguientes pasos:
   
    ![Basic Information (Información básica)](./media/sharefile-tutorial/user-form.png "Información básica")
   
    a. En el cuadro de texto **First name** (Nombre), escriba el **nombre** del usuario, en este caso, **Britta**.
   
    b.  En el cuadro de texto **Last name** (Apellido), escriba el **apellido** del usuario, en este caso **Simon**.
   
    c. En el cuadro de texto **Email Address** (Dirección de correo electrónico), escriba la dirección de correo electrónico de Britta Simon, como **brittasimon\@contoso.com**.

4. Haga clic en **Agregar usuario**.
  
    >[!NOTE]
    >El titular de la cuenta de Azure AD recibirá un correo electrónico y seguirá un vínculo para confirmar la cuenta antes de que se active. Puede usar cualquier otra herramienta de creación de cuentas de usuario de Citrix ShareFile o API que Citrix ShareFile proporcione para aprovisionar las cuentas de usuario de Azure AD.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Citrix ShareFile, donde puede iniciar el flujo de inicio de sesión.

* Vaya directamente a la dirección URL de inicio de sesión de Citrix ShareFile e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Citrix ShareFile en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Citrix ShareFile. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Citrix ShareFile, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).