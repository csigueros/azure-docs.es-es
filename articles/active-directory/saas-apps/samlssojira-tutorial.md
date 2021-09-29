---
title: 'Tutorial: Integración de Azure Active Directory con SAML SSO for Jira by resolution GmbH | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAML SSO for Jira by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 79ec9389840582ea7841bfd0edc4bda3a6f16f01
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746552"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integración de Azure Active Directory con SAML SSO for Jira by resolution GmbH

En este tutorial, aprenderá a integrar SAML SSO for Jira by resolution GmbH con Azure Active Directory (Azure AD). Al integrar SAML SSO for Jira by resolution GmbH con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a SAML SSO for Jira by resolution GmbH.
* Permitir que los usuarios inicien sesión automáticamente en SAML SSO for Jira by resolution GmbH con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en SAML SSO for Jira by resolution GmbH.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SAML SSO for Jira by resolution GmbH admite el inicio de sesión único iniciado por **SP** e **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Adición de SAML SSO for Jira by resolution GmbH desde la galería

Para configurar la integración de SAML SSO for Jira by resolution GmbH en Azure AD, debe agregar SAML SSO for Jira by resolution GmbH desde la galería a la lista de aplicaciones administradas de SaaS.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SAML SSO for Jira by resolution GmbH** en el cuadro de búsqueda.
1. Seleccione **SAML SSO for Jira by resolution GmbH** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Configuración y prueba del inicio de sesión único de Azure AD con SAML SSO for Jira by resolution GmbH

Configure y pruebe el inicio de sesión único de Azure AD con SAML SSO for Jira by resolution GmbH utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso vincular un usuario de Azure AD y el usuario correspondiente de SAML SSO for Jira by resolution GmbH.

Para configurar el inicio de sesión único de Azure AD con SAML SSO for Jira by resolution GmbH, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de SAML SSO for Jira by resolution GmbH](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** , para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SAML SSO for Jira by resolution GmbH](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** , para tener un homólogo de B.Simon en SAML SSO for Jira by resolution GmbH que esté vinculado a la representación de ella en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure portal, en la página de integración de aplicaciones de **SAML SSO for Jira by resolution GmbH**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<server-base-url>/plugins/servlet/samlsso`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Para los valores de identificador, dirección URL de respuesta y dirección URL de inicio de sesión, reemplace **\<server-base-url>** por la dirección URL base de la instancia de Jira. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal. Si tiene algún problema, póngase en contacto con el [equipo de soporte técnico de clientes de SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support).

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, descargue el archivo **XML de metadatos de federación**  y guárdelo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

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

En esta sección, concederá acceso a B.Simon a SAML SSO for Jira by resolution GmbH para que use el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SAML SSO for Jira by resolution GmbH**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Configuración del inicio de sesión único de SAML SSO for Jira by resolution GmbH 

1. En otra ventana del explorador web, inicie sesión en la instancia de Jira como administrador.

2. Mantenga el puntero sobre el icono de engranaje en el lado derecho y haga clic en **Manage apps** (Administrar aplicaciones).
    
    ![Captura de pantalla que muestra una flecha que apunta al icono del engranaje y a la opción "Manage apps" (Administrar aplicaciones) seleccionada en la lista desplegable.](./media/samlssojira-tutorial/add-on-1.png)

3. Se le redirigirá a la página de acceso de administrador para especificar la **contraseña** y haga clic en el botón **Confirm** (Confirmar).

    ![Captura de pantalla que muestra la página "Administrator Access" (Acceso de administrador).](./media/samlssojira-tutorial/add-on-2.png)

4. Jira normalmente redirige a Atlassian Marketplace. En caso contrario, haga clic en **Find new apps** (Buscar nuevas aplicaciones) en el panel izquierdo. Busque **SAML Single Sign On (SSO) for JIRA** y haga clic en el botón **Install** (Instalar) para instalar el nuevo complemento SAML.

    ![Captura de pantalla que muestra la página "Atlassian Marketplace for JIRA" (Atlassian Marketplace para JIRA) con una flecha que señala al botón "Install" (Instalar) para la aplicación "S A M L Single Sign on (S S O) Jira, S A M L/S S O" (Inicio de sesión único [S S O] de S A M L en Jira, S A M L/S S O).](./media/samlssojira-tutorial/store.png)

5. Se iniciará la instalación del complemento. Cuando termine, haga clic en el botón **Close** (Cerrar).

    ![Captura de pantalla que muestra el cuadro de diálogo "Installing" (Instalando).](./media/samlssojira-tutorial/store-2.png)

    ![Captura de pantalla que muestra el cuadro de diálogo "Installed and ready to go!" (Instalado y listo para usarse) con el botón "Close" (Cerrar) seleccionado.](./media/samlssojira-tutorial/store-3.png)

6. A continuación, haga clic en **Manage** (Administrar).

    ![Captura de pantalla que muestra la aplicación "S A M L Single Sign On (S S O) Jira, S A M L/S S O (Inicio de sesión único [S S O] de S A M L en Jira, S A M L/S S O) con el botón "Manage" (Administrar) seleccionado.](./media/samlssojira-tutorial/store-4.png)
    
7. Después, haga clic en **Configure** (Configurar) para configurar el complemento que se acaba de instalar.

    ![Captura de pantalla que muestra la página "Manage apps" (Administrar aplicaciones), con el botón "Configure" (Configurar) seleccionado para la aplicación "S A M L SingleSignOn for JIRA".](./media/samlssojira-tutorial/store-5.png)

8. En el asistente **SAML SingleSignOn Plugin Configuration** (Configuración del complemento SingleSignOn de SAML), haga clic en **Add new IdP** (Agregar IdP nuevo) para configurar a Azure AD como el nuevo proveedor de identidades.

    ![Captura de pantalla que muestra la página de bienvenida con el botón "Add new I d P" (Agregar nuevo I d P) seleccionado.](./media/samlssojira-tutorial/add-on-4.png) 

9. En la página **Choose your SAML Identity Provider** (Elegir el proveedor de identidades SAML), siga estos pasos:

    ![Captura de pantalla que muestra la página "Choose your S A M L Identity Provider" (Elegir el proveedor de identidades de S A M L) con el cuadro de texto "Name" (Nombre) resaltado y el botón "Next" (Siguiente) seleccionado.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Establezca **Azure AD** como el tipo de IdP.
    
    b. Agregue el **nombre** del proveedor de identidades (p. ej., Azure AD).
    
    c. Agregue la **descripción** (opcional) del proveedor de identidades (p. ej., Azure AD).
    
    d. Haga clic en **Next**.
    
10. En la página **Identity provider configuration** (Configuración del proveedor de identidades), haga clic en el botón **Next** (Siguiente).
 
    ![Captura de pantalla que muestra la pantalla "Identity provider configuration" (Configuración del proveedor de identidades).](./media/samlssojira-tutorial/configuration.png)

11. En la página **Import SAML IdP Metadata** (Importar los metadatos del IdP de SAML), siga los pasos siguientes:

    ![Captura de pantalla que muestra la página "Import S A M L I d P Metadata" (Importar metadatos del I d P de S A M L) con la acción "Select Metadata X M L File" (Seleccionar archivo X M L de metadatos) seleccionada.](./media/samlssojira-tutorial/metadata.png)

    a. Haga clic en el botón **Select Metadata XML File** (Seleccionar archivo XML de metadatos) y elija el archivo **XML de metadatos de federación** que ha descargado antes.

    b. Haga clic en el botón **Import** (Importar).
     
    c. Espere un momento hasta que la importación se realice correctamente.  
     
    d. Haga clic en el botón **Next** (Siguiente).
    
12. En la página **User ID attribute and transformation** (Transformación y atributo del id. de usuario), haga clic en el botón **Next** (Siguiente).

    ![Captura de pantalla que muestra la página "User I D attribute and transformation" (Atributo de id. de usuario y transformación) con el botón "Next" (Siguiente) seleccionado.](./media/samlssojira-tutorial/transformation.png)
    
13. En la página **User creation and update** (Creación y actualización de usuarios), haga clic en **Save & Next** (Guardar y siguiente) para guardar la configuración.
    
    ![Captura de pantalla que muestra la página "User creation and update" (Creación y actualización de usuarios) con el botón "Save & Next" (Guardar y siguiente) seleccionado.](./media/samlssojira-tutorial/update.png)
    
14. En la página **Test your settings** (Probar su configuración), haga clic en **Skip test & configure manually** (Omitir la prueba y configurar manualmente) para omitir la prueba de usuario por ahora. Esto se realizará en la sección siguiente y requiere la configuración de algunos valores en Azure Portal.
    
    ![Captura de pantalla que muestra la página "Test your settings" (Probar su configuración) con el botón "Skip test & configure manually" (Omitir prueba y configurar manualmente) seleccionado.](./media/samlssojira-tutorial/test.png)
    
15. Haga clic en **OK** (Aceptar) para omitir la advertencia.
    
    ![Captura de pantalla que muestra el cuadro de diálogo de advertencia con el botón "O K" (Aceptar) seleccionado.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Creación de un usuario de prueba de SAML SSO for Jira by resolution GmbH

Para permitir que los usuarios de Azure AD inicien sesión en SAML SSO for Jira by resolution GmbH, deben estar aprovisionados en SAML SSO for Jira by resolution GmbH. En el caso de este tutorial, deberá realizar el aprovisionamiento de forma manual. Sin embargo, hay también otros modelos de aprovisionamiento disponibles para el complemento SAML SSO by resolution, por ejemplo **simplemente In Time** aprovisionamiento. Consulte la documentación en [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Si tiene alguna pregunta sobre ella, póngase en contacto con el soporte técnico para [su resolución](https://www.resolution.de/go/support).

**Para aprovisionar una cuenta de usuario manualmente, realice estos pasos:**

1. Inicie sesión como administrador en una instancia de Jira.

2. Mantenga el puntero sobre el icono de engranaje y seleccione **Administración de usuarios**.

   ![Captura de pantalla que muestra el icono de engranaje seleccionado y "User management" (Administración de usuarios) seleccionada en el menú desplegable.](./media/samlssojira-tutorial/user-1.png)

3. Se le redirigirá a la página de acceso de administrador para especificar la **contraseña** y haga clic en el botón **Confirm** (Confirmar).

    ![Captura de pantalla que muestra la página "Administrator Access" (Acceso de administrador) con el cuadro de texto "Password" (Contraseña) resaltado.](./media/samlssojira-tutorial/user-2.png) 

4. En la sección de la pestaña **User management** (Administración de usuarios), haga clic en **Create user** (Crear usuario).

    ![Captura de pantalla que muestra la sección "User management" (Administración de usuarios) con el botón "Create user" (Crear usuario) seleccionado.](./media/samlssojira-tutorial/user-3-new.png) 

5. En la página del cuadro de diálogo **"Create New User"** (Crear nuevo usuario), realice los pasos siguientes. El usuario se crea exactamente igual que en Azure AD:

    ![Agregar empleado](./media/samlssojira-tutorial/user-4-new.png) 

    a. En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo electrónico del usuario: <b>BrittaSimon@contoso.com</b>.

    b. En el cuadro de texto **Nombre completo**, escriba el nombre completo del usuario: **Britta Simon**.

    c. En el cuadro de texto **Username** (Nombre de usuario), escriba la dirección de correo electrónico del usuario: <b>BrittaSimon@contoso.com</b>. 

    d. En el cuadro de texto **Password** (Contraseña), escriba la contraseña del usuario.

    e. Haga clic en **Create user** (Crear usuario) para finalizar la creación del usuario.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de SAML SSO for Jira by resolution GmbH, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de SAML SSO for Jira by resolution GmbH e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de SAML SSO for Jira by resolution para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de SAML SSO for Jira by resolution GmbH en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, iniciará sesión automáticamente en la instancia de SAML SSO for Jira by resolution para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="enable-sso-redirection-for-jira"></a>Habilitación de la redirección de inicio de sesión único para Jira

Como se ha indicado en la sección anterior, actualmente hay dos formas de desencadenar el inicio de sesión único. Ya sea mediante **Azure Portal** o mediante **un vínculo especial a la instancia de Jira**. El complemento SAML SSO by resolution GmbH también permite desencadenar un inicio de sesión único simplemente **mediante el acceso a cualquier dirección URL que apunte a la instancia de Jira**.

En esencia, todos los usuarios que tienen acceso a Jira se redirigirán al inicio de sesión único después de activar una opción en el complemento.

Para activar el redireccionamiento de inicio de sesión único, realice lo siguiente en **la instancia de Jira**:

1. Acceda a la página de configuración del complemento SAML SSO de Jira.
1. Haga clic en **Redirección** en el panel izquierdo.

   ![Captura de pantalla parcial de la página Jira SAML SingleSignOn Plugin Configuration (Configuración del complemento de inicio de sesión único de SAML de Jira) que resalta el vínculo Redirection (Redireccionamiento) en el panel de navegación izquierdo.](./media/samlssojira-tutorial/configure-1.png)

1. Marque **Enable SSO Redirect** (Habilitar la redirección de inicio de sesión único).

   ![Captura de pantalla parcial de la página Jira SAML SingleSignOn Plugin Configuration (Configuración del complemento de inicio de sesión único de SAML de Jira) que resalta la casilla "Enable SSO Redirect" (Habilitar la redirección de inicio de sesión único) seleccionada.](./media/samlssojira-tutorial/configure-2.png) 

1. Presione el botón **Save Settings** (Guardar configuración) en la esquina superior derecha.

Después de activar la opción, todavía puede llegar a la solicitud de nombre de usuario y contraseña si la opción **Enable nosso** (Habilitar nosso) está activada cuando se navega a `https://<server-base-url>/login.jsp?nosso`. Como siempre, sustituya **\<server-base-url>** por la dirección URL base.

## <a name="next-steps"></a>Pasos siguientes

Una vez que se ha configurado SAML SSO for Jira by resolution GmbH, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).