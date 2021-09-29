---
title: 'Tutorial: Integración de Azure Active Directory con Absorb LMS | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Absorb LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 1347ee33905f9c39e752c1a78e3d5573f9fb6f2d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124770935"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integración de Azure Active Directory con Absorb LMS

En este tutorial, aprenderá a integrar Absorb LMS con Azure Active Directory (Azure AD). Al integrar Absorb LMS con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Absorb LMS.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Absorb LMS con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Absorb LMS, necesita lo siguiente:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único en Absorb LMS.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Absorb LMS admite SSO iniciado por **IDP**

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-absorb-lms-from-the-gallery"></a>Agregar Absorb LMS desde la galería

Para configurar la integración de Absorb LMS en Azure AD, será preciso que agregue Absorb LMS desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Absorb LMS** en el cuadro de búsqueda.
1. Seleccione **Absorb LMS** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Configuración y prueba del inicio de sesión único de Azure AD para Absorb LMS

Configure y pruebe el inicio de sesión único de Azure AD con Absorb LMS mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Absorb LMS.

Para configurar y probar el inicio de sesión único de Azure AD con Absorb LMS, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Absorb LMS](#configure-absorb-lms-sso)** : para configurar los valores de inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba de Absorb LMS](#create-absorb-lms-test-user)** : para tener un homólogo de B.Simon en Absorb LMS que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Absorb LMS**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Configuración básica de SAML**.

    Si usa **Absorb 5 - UI** (Absorb 5 - IU), utilice la siguiente configuración:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Si usa **Absorb 5 - New Learner Experience** (Absorb 5 - Nueva experiencia de aprendiz), utilice la siguiente configuración:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Absorb LMS](https://support.absorblms.com/hc/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. La siguiente captura de pantalla muestra la lista de atributos predeterminados, donde **nameidentifier** se asigna con **user.userprincipalname**.

    ![imagen](common/edit-attribute.png)

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

7. En la sección **Set up Absorb LMS** (Configurar Absorb LMS), copie las direcciones URL adecuada según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Absorb LMS mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Absorb LMS**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-absorb-lms-sso"></a>Configuración del inicio de sesión único de Absorb LMS

1. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Absorb LMS.

2. Haga clic en el botón **Cuenta** que se encuentra en la esquina superior derecha.

    ![Botón de cuenta](./media/absorblms-tutorial/account.png)

3. En el panel Cuenta, seleccione **Configuración del portal**.

    ![Vínculo de configuración del portal](./media/absorblms-tutorial/portal.png)

4. Seleccione la pestaña **Manage SSO Settings** (Administrar configuración de SSO).

    ![La pestaña Usuarios](./media/absorblms-tutorial/sso.png)

5. Siga estos pasos en la página **Manage Single Sign-On Settings** (Administrar configuración de SSO).

    ![Página de configuración de inicio de sesión único](./media/absorblms-tutorial/settings.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre como inicio de sesión único de Azure AD Marketplace.

    b. Seleccione **SAML** como **Método**.

    c. En el Bloc de notas, abra el certificado que descargó desde Azure Portal. Quite las etiquetas **---BEGIN CERTIFICATE---** y **---END CERTIFICATE---**. A continuación, pegue el resto del contenido en el cuadro de texto **Clave**.

    d. En el cuadro **Modo**, seleccione **Identity Provider Initiated (Iniciado por el proveedor de identidades)**.

    e. En el cuadro de texto **Propiedad de id.**, seleccione el atributo adecuado que ha configurado como identificador de usuario en Azure AD. Por ejemplo, si se selecciona *nameidentifier* en Azure AD, deberá seleccionar **Nombre de usuario**.

    f. Seleccione **Sha256** como **Tipo de firma**.

    g. En el cuadro **dirección URL de inicio de sesión**, pegue la **dirección URL de acceso de usuario** de la página **Propiedades** de la aplicación en Azure Portal.

    h. En **Dirección URL de cierre de sesión**, pegue el valor de la **dirección URL de cierre de sesión** que copió de la ventana **Configurar inicio de sesión** de Azure Portal.

    i. Cambie **Automatically Redirect** (Redirigir automáticamente) a **Activado**.

6. Seleccione **Guardar.**

    ![Opción Only Allow SSO Login (Solo permitir inicio de sesión SSO)](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Creación de un usuario de prueba de Absorb LMS

Para permitir que los usuarios de Azure AD inicien sesión en Absorb LMS, tienen configurar Absorb LMS. En el caso de Absorb LMS, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión como administrador en el sitio de la compañía de Absorb LMS.

2. En el panel **Usuarios**, seleccione **Usuarios**.

    ![Vínculo de usuarios](./media/absorblms-tutorial/users.png)

3. Seleccione la pestaña **Usuario**.

    ![Lista desplegable Agregar nuevo](./media/absorblms-tutorial/add.png)

4. En la página **Agregar usuario**, siga estos pasos:

    ![página Agregar usuario](./media/absorblms-tutorial/user.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre **Britta**.

    b. En el cuadro de texto **Apellido**, escriba el apellido **Simon**.

    c. En el cuadro de texto **Nombre de usuario**, escriba el nombre completo **Britta Simon**.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.

    e. En el cuadro **Confirmar contraseña** , vuelva a escribir la contraseña.

    f. Establezca la opción **Está activo** en **Activo**.

5. Seleccione **Guardar.**

    ![Opción Only Allow SSO Login (Solo permitir inicio de sesión SSO)](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > De forma predeterminada, el aprovisionamiento de usuarios no está habilitado en el inicio de sesión único. Si el cliente desea habilitar esta característica, debe establecerla como se mencionó en [esta](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentación. También tenga en cuenta que el aprovisionamiento de usuario solo está disponible en **Absorb 5 - New Learner Experience** (Absorb 5 - Nueva experiencia de aprendiz) con la dirección URL de ACS: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Absorb LMS para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Absorb LMS en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de Absorb LMS para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Absorb LMS, puede aplicar el control de sesión, que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).