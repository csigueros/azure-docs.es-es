---
title: 'Tutorial: Integración de Azure Active Directory con SAML SSO for Confluence by resolution GmbH | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAML SSO for Confluence by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 8835f32b9a4960587eeb8a15545b3a2062661230
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124765293"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: Integración de Azure Active Directory con SAML SSO for Confluence by resolution GmbH

En este tutorial, aprenderá a integrar SAML SSO for Confluence by resolution GmbH con Azure Active Directory (Azure AD). Si integra SAML SSO for Confluence by resolution GmbH con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a SAML SSO for Confluence by resolution GmbH.
* Permitir que los usuarios inicien sesión automáticamente en SAML SSO for Confluence by resolution GmbH con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción que permita el inicio de sesión único en SAML SSO for Confluence by resolution GmbH.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SAML SSO for Confluence by resolution GmbH admite el inicio de sesión único iniciado por **SP e IDP**

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Incorporación de SAML SSO for Confluence by resolution GmbH desde la galería

Para configurar la integración de SAML SSO for Confluence by resolution GmbH en Azure AD, debe agregar SAML SSO for Confluence by resolution GmbH desde la galería a la lista de aplicaciones administradas de SaaS.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SAML SSO for Confluence by resolution GmbH** en el cuadro de búsqueda.
1. Seleccione **SAML SSO for Confluence by resolution GmbH** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Configuración y comprobación del inicio de sesión único de Azure AD con SAML SSO for Confluence by resolution GmbH

Configure y pruebe el inicio de sesión único de Azure AD con SAML SSO for Confluence by resolution GmbH utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso vincular un usuario de Azure AD y el usuario correspondiente de SAML SSO for Confluence by resolution GmbH.

Para configurar y probar el inicio de sesión único de Azure AD con SAML SSO for Confluence by resolution GmbH, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de SAML SSO for Confluence by resolution GmbH](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** : para configurar el inicio de sesión único en el lado de la aplicación.
    1. **[Creación del usuario de prueba de SAML SSO for Confluence by resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** : para tener un homólogo de Britta Simon en SAML SSO for Confluence by resolution GmbH vinculado a la representación del usuario de Azure AD.
6. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones **SAML SSO for Confluence by resolution GmbH**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<server-base-url>/plugins/servlet/samlsso`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de clientes de SAML SSO for Confluence by resolution GmbH](https://www.resolution.de/go/support) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

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

En esta sección, va a permitir que B.Simon utilice el inicio de sesión único de Azure concediéndole acceso a SAML SSO for Confluence by resolution GmbH.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SAML SSO for Confluence by resolution GmbH**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Configuración del inicio de sesión único en SAML SSO for Confluence by resolution GmbH

1. En otra ventana del explorador web, inicie sesión en el **Portal de administración de SAML SSO for Confluence by resolution GmbH** como administrador.

2. Mantenga el mouse encima del icono de engranaje y haga clic en **Complementos**.
    
    ![Captura de pantalla que muestra el icono de "engranaje" seleccionado, y la opción "Complementos" seleccionada en el menú desplegable.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Se le redirigirá a la página de acceso de administrador. Escriba la contraseña y haga clic en el botón **Confirmar**.

    ![Captura de pantalla que muestra la página de "acceso de administrador" con el botón "Confirmar" seleccionado.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. En la pestaña **ATLASSIAN MARKETPLACE** (MARKETPLACE DE ATLASSIAN), haga clic en **Find new add-ons** (Buscar nuevos complementos). 

    ![Captura de pantalla que muestra la pestaña "Attlassian Marketplace" (Marketplace de Attlassian), con la opción "Find new add-ons" (Buscar nuevos complementos) seleccionada.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Busque **SAML Single Sign On (SSO) for Confluence** y haga clic en el botón **Instalar** para instalar el nuevo complemento SAML.

    ![Captura de pantalla que muestra la página "Find new add-ons" (Buscar nuevos complementos) con el texto "SAML Single Sign On (SSO) for Confluence" en el cuadro de búsqueda y el botón "Install" (Instalar) seleccionado.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. Se iniciará la instalación del complemento. Haga clic en **Cerrar**.

    ![Captura de pantalla que muestra el cuadro de diálogo "Installing" (Instalando).](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Captura de pantalla que muestra el cuadro de diálogo "Installed and ready to go!" (Instalado y listo para usarse) con la acción "Cerrar" seleccionada.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Haga clic en **Administrar**.

    ![Captura de pantalla que muestra la página de aplicación "SAML Single Sign On (SSO) for Confluence" con el botón "Administrar" seleccionado.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Haga clic en **Configurar** para configurar el nuevo complemento.

    ![Captura de pantalla que muestra la página "Administrar", con el botón "Configurar" seleccionado.](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Este nuevo complemento también puede encontrarse en la pestaña **USERS & SECURITY** (USUARIOS Y SEGURIDAD).

    ![Captura de pantalla que muestra la pestaña "Users & Security" (Usuarios y seguridad), con la opción "SAML SingleSignOn" seleccionada.](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. En la página **SAML SingleSignOn Plugin Configuration** (Configuración del complemento SingleSignOn de SAML), haga clic en el botón **Add new IdP** (Agregar IdP nuevo) para configurar los valores del proveedor de identidades.

    ![Captura de pantalla que muestra la página "SAML SingleSignOn Plugin Configuration" (Configuración del complemento SingleSignOn de SAML), con el botón "Add new IdP" (Agregar nuevo IdP) seleccionado.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. En la página **Choose your SAML Identity Provider** (Elegir el proveedor de identidades SAML), siga estos pasos:

    ![Captura de pantalla que muestra la página "Choose your SAML Identity Provider" (Elegir el proveedor de identidades SAML), con los cuadros de texto "IdP Type" (Tipo de IdP), "Name" (Nombre) y "Description" (Descripción) resaltados.](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Establezca **Azure AD** como el tipo de IdP.
    
    b. Agregue el **nombre** del proveedor de identidades (p. ej., Azure AD).
    
    c. Agregue la **descripción** del proveedor de identidades (p. ej., Azure AD).
    
    d. Haga clic en **Next**.
    
12. En la página **Identity provider configuration** (Configuración del proveedor de identidades), haga clic en el botón **Siguiente**.

    ![Captura de pantalla que muestra la página "Identity provider configuration" (Configuración de proveedor de identidades), con el botón "Siguiente" seleccionado.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. En la página **Import SAML IdP Metadata** (Importar los metadatos del IdP de SAML), siga los pasos siguientes:

    ![Captura de pantalla que muestra la página "Import SAML IdP Metadata" (Importar los metadatos del IdP de SAML), con los botones "Importar", "Cargar archivo" y "Siguiente" seleccionados.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Haga clic en el botón **Cargar archivo** y seleccione el archivo XML de metadatos que descargó en el paso 5.

    b. Haga clic en el botón **Importar**.
    
    c. Espere un momento hasta que la importación se realice correctamente.
    
    d. A continuación, haga clic en el botón **Siguiente**.
    
14. En la página **User ID attribute and transformation** (Transformación y atributo del id. de usuario), haga clic en el botón **Siguiente**.

    ![Captura de pantalla que muestra la página "User ID attribute and transformation" (Transformación y atributo del id. de usuario), con el botón "Siguiente" seleccionado.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. En la página **User creation and update** (Creación y actualización de usuarios), haga clic en **Guardar y siguiente** para guardar la configuración.   
    
    ![Captura de pantalla que muestra la página "User creation and update" (Creación y actualización de usuarios) con el botón "Save & Next" (Guardar y siguiente) seleccionado.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. En la página **Test your settings** (Probar su configuración), haga clic en **Skip test & configure manually** (Omitir la prueba y configurar manualmente) para omitir la prueba de usuario por ahora. Esto se realizará en la sección siguiente y requiere la configuración de algunos valores en Azure Portal. 
    
    ![Captura de pantalla que muestra la página "Test your settings" (Probar su configuración) con el botón "Skip test & configure manually" (Omitir la prueba y configurar manualmente) seleccionado.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. En el cuadro que aparece con el texto **Skipping the test means...** (Omitir la prueba significa...), haga clic en **OK** (Aceptar).
    
    ![Configurar inicio de sesión único](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Creación del usuario de prueba de SAML SSO for Confluence by resolution GmbH

Para permitir que los usuarios de Azure AD inicien sesión en SAML SSO for Confluence by resolution GmbH, deben estar aprovisionados en SAML SSO for Confluence by resolution GmbH.  
En SAML SSO for Confluence by resolution GmbH, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la empresa de SAML SSO for Confluence by resolution GmbH como administrador.

2. Mantenga el mouse encima del icono de engranaje y haga clic en **Administración de usuarios**.

    ![Captura de pantalla que muestra el icono de "engranaje" seleccionado, y la opción "Administración de usuarios" seleccionada en el menú.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. En la sección Usuarios, haga clic en la pestaña **Agregar usuarios**. En la página del cuadro de diálogo **Agregar un usuario**, realice los siguientes pasos:

    ![Agregar empleado](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Britta Simon.

    b. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario, por ejemplo, Britta Simon.

    c. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña de Britta Simon.

    e. Haga clic en **Confirmar contraseña** y vuelva a escribir la contraseña.
    
    f. Haga clic en el botón **Agregar**.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. De este modo, accederá automáticamente a la dirección URL de inicio de sesión de SAML SSO for Confluence by resolution GmbH, donde podrá comenzar el flujo de inicio de sesión.  

* Acceda directamente a la dirección URL de inicio de sesión de SAML SSO for Confluence by resolution GmbH y comience el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* En Azure Portal, haga clic en **Probar esta aplicación**. Al hacerlo, debería iniciar sesión automáticamente en la instancia de SAML SSO for Confluence by resolution GmbH en la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Cuando en Aplicaciones haga clic en el icono de SAML SSO for Confluence by resolution GmbH, si seleccionó el modo SP en la configuración, debería acceder automáticamente a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Por el contrario, si seleccionó el modo IDP en la configuración, debería iniciar sesión automáticamente en la instancia de SAML SSO for Confluence by resolution GmbH en la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado SAML SSO for Confluence by resolution GmbH, podrá aplicar el control de sesión, que protege la información confidencial de la organización de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).