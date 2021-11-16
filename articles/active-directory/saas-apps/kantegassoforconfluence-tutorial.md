---
title: 'Tutorial: Integración del inicio de sesión único de Azure Active Directory con Kantega SSO para Confluence'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Kantega SSO para Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/13/2021
ms.author: jeedes
ms.openlocfilehash: 024687e693b82ed3f8053fcdceefe876a5cad9e1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313819"
---
# <a name="tutorial-azure-ad-sso-integration-with-kantega-sso-for-confluence"></a>Tutorial: Integración del inicio de sesión único de Azure Active Directory con Kantega SSO para Confluence

En este tutorial aprenderá a integrar Kantega SSO para Confluence con Azure Active Directory (Azure AD). Al integrar Kantega SSO para Confluence con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Kantega SSO para Confluence.
* Permitir que los usuarios inicien sesión automáticamente en Kantega SSO para Confluence con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Prerrequisitos

Para configurar la integración de Azure AD con Kantega SSO para Confluence, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción de Kantega SSO para Confluence con el inicio de sesión único habilitado.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Kantega SSO para Confluence admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="add-kantega-sso-for-confluence-from-the-gallery"></a>Incorporación de Kantega SSO para Confluence desde la galería

Para configurar la integración de Kantega SSO para Confluence en Azure AD, tiene que agregar Kantega SSO para Confluence desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Kantega SSO para Confluence** en el cuadro de búsqueda.
1. Seleccione **Kantega SSO para Confluence** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-confluence"></a>Configuración y prueba del inicio de sesión único de Azure AD para Kantega SSO para Confluence

Configure y pruebe el inicio de sesión único de Azure AD con Kantega SSO para Confluence mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Kantega SSO para Confluence.

Para configurar y probar el inicio de sesión único de Azure AD con Kantega SSO para Confluence, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Kantega SSO para Confluence](#configure-kantega-sso-for-confluence-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Kantega SSO para Confluence](#create-kantega-sso-for-confluence-test-user)** : para tener un homólogo de B.Simon en Kantega SSO para Confluence que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Kantega SSO para Confluence**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Estos valores se reciben durante la configuración del complemento de Confluence, que se explica más adelante en el tutorial.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

7. En la sección **Set up Kantega SSO for Confluence** (Configurar Kantega SSO para Confluence), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección va a permitir que B.Simon acceda a Kantega SSO para Confluence mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Kantega SSO para Confluence**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-kantega-sso-for-confluence-sso"></a>Configuración del inicio de sesión único en Kantega SSO para Confluence

1. En otra ventana del explorador web, inicie sesión en el **Portal de administración de Confluence** como administrador.

1. Mantenga el mouse encima del icono de engranaje y haga clic en **Complementos**.

    ![Captura de pantalla que muestra el menú del icono de engranaje y la opción "Add-ons" (Complementos) seleccionada.](./media/kantegassoforconfluence-tutorial/settings.png)

1. En la pestaña **ATLASSIAN MARKETPLACE** (MARKETPLACE DE ATLASSIAN), haga clic en **Find new add-ons** (Buscar nuevos complementos).

    ![Captura de pantalla que muestra la pestaña "ATTLASSIAN MARKETPLACE" (MARKETPLACE DE ATLASSIAN) con la opción "Find new add-ons" (Buscar nuevos complementos) seleccionada.](./media/kantegassoforconfluence-tutorial/admin.png)

1. Busque **Kantega SSO para Confluence (SAML Kerberos)** y haga clic en el botón **Instalar** para instalar el nuevo complemento SAML.

    ![Captura de pantalla que muestra la página "Find new add-ons" (Buscar nuevos complementos) con el texto "Kantega S S O for Confluence S A M L Kerberos" en el cuadro de búsqueda y el botón "Install" (Instalar) seleccionado.](./media/kantegassoforconfluence-tutorial/install-button.png)

1. Se inicia la instalación del complemento.

    ![Captura de pantalla que muestra la pantalla "Installing" (Instalando) del complemento.](./media/kantegassoforconfluence-tutorial/plugin.png)

1. Una vez completada la instalación. Haga clic en **Cerrar**.

    ![Captura de pantalla que muestra la pantalla "Installed and ready to go" (Instalado y listo para usarse) con la acción "Close" (Cerrar) seleccionada.](./media/kantegassoforconfluence-tutorial/installation.png)

1. Haga clic en **Administrar**.

    ![Captura de pantalla que muestra el complemento "Kantega Single Sign-on with Kerberos and S A M L" (Inicio de sesión único de Kantega con Kerberos y S A M L) con el botón "Manage" (Administrar) seleccionado.](./media/kantegassoforconfluence-tutorial/integration.png)

1. Haga clic en **Configurar** para configurar el nuevo complemento.

    ![Captura de pantalla que muestra la página "Kantega Single Sign-on with Kerberos and S A M L" (Inicio de sesión único de Kantega con Kerberos y S A M L) con el botón "Configure" (Configurar) seleccionado.](./media/kantegassoforconfluence-tutorial/configuration.png)

1. Este nuevo complemento también puede encontrarse en la pestaña **USERS & SECURITY** (USUARIOS Y SEGURIDAD).

    ![Captura de pantalla que muestra la pestaña "USERS & SECURITY" (Usuarios y seguridad) con la acción "Kantega Single Sign-on" (Inicio de sesión único de Kantega) seleccionada.](./media/kantegassoforconfluence-tutorial/security.png)

1. En la sección **SAML**. Seleccione **Azure Active Directory (Azure AD)** en la lista desplegable **Agregar proveedor de identidades**.

    ![Captura de pantalla que muestra la sección "S A M L" con las opciones "Add Identity provider" (Agregar proveedor de identidades) y "Azure Active Directory (Azure AD)" seleccionadas.](./media/kantegassoforconfluence-tutorial/azure.png)

1. Seleccione el nivel de suscripción **Básica**.

    ![Captura de pantalla que muestra la página "Preparing Azure AD" (Preparación de Azure A D) con la opción "Basic" (Básica) seleccionada.](./media/kantegassoforconfluence-tutorial/subscription.png)

1. En la sección **Agregar propiedades**, siga estos pasos:

    ![Captura de pantalla que muestra la sección "App properties" (Propiedades de la aplicación) con el campo "App I D U R L" (Dirección U R L del identificador de aplicación) y el botón "Copy" (Copiar) resaltados, y el botón "Next" (Siguiente) seleccionado.](./media/kantegassoforconfluence-tutorial/properties.png)

    a. Copie el valor de **URI de id. de aplicación** y úselo en los campos **Identificador, URL de respuesta y URL de inicio de sesión** de la sección **Configuración básica de SAML**  de Azure Portal.

    b. Haga clic en **Next**.

1. En la sección **Importar metadatos**, siga estos pasos: 

    ![Captura de pantalla que muestra la sección "Importación de metadatos" con la opción "Archivo de metadatos en mi equipo" seleccionada.](./media/kantegassoforconfluence-tutorial/metadata.png)

    a. Seleccione **Archivo de metadatos en el equipo** y cargue el archivo de metadatos que descargó desde Azure Portal.

    b. Haga clic en **Next**.

1. En la sección **Name and SSO location** (Nombre y ubicación de SSO), siga estos pasos:

    ![Captura de pantalla que muestra la opción "Name and S S O location" (Nombre y ubicación de S S O) y el botón "Siguiente" seleccionado.](./media/kantegassoforconfluence-tutorial/location.png)

    a. Agregue el nombre del proveedor de identidades en el cuadro de texto **Nombre del proveedor de identidades** (por ejemplo, Azure AD).

    b. Haga clic en **Next**.

1. Compruebe el certificado de firma y haga clic en **Siguiente**.

    ![Captura de pantalla que muestra la sección "Signature verification" (Comprobación de firmas) con el botón "Next" (Siguiente) seleccionado.](./media/kantegassoforconfluence-tutorial/certificate.png)

1. En la sección **Cuentas de usuario de Confluence**, siga estos pasos:

    ![Captura de pantalla que muestra la sección "Confluence user accounts" (Cuentas de usuario de Confluence) con la opción "Create users in Confluence's Internal Directory if needed" (Crear usuarios en el directorio interno de Confluence si es necesario) y el botón "Next" (Siguiente) seleccionados.](./media/kantegassoforconfluence-tutorial/accounts.png)

    a. Seleccione **Create users in Confluence's internal Directory if needed** (Crear usuarios en el directorio interno de Confluence si es necesario) y escriba el nombre adecuado del grupo de usuarios (puede ser un número múltiple de grupos separados por coma).

    b. Haga clic en **Next**.

1. Haga clic en **Finalizar**

    ![Captura de pantalla de la página "Summary" (Resumen) con el botón "Finish" (Finalizar) seleccionado.](./media/kantegassoforconfluence-tutorial/summary.png)

1. En la sección **Known domains for Azure AD** (Dominios conocidos para Azure AD), siga estos pasos: 

    ![Captura de pantalla que muestra la página "Known domains for Azure AD" (Dominios conocidos para Azure AD) con el cuadro de texto "Known domains" (Dominios conocidos) resaltado y el botón "Save" (Guardar) seleccionado.](./media/kantegassoforconfluence-tutorial/domain.png)

    a. Seleccione **Known domains** (Dominios conocidos) en el panel izquierdo de la página.

    b. Escriba el nombre de dominio en el cuadro de texto **Known domains** (Dominios conocidos).

    c. Haga clic en **Save**(Guardar).

### <a name="create-kantega-sso-for-confluence-test-user"></a>Creación de un usuario de prueba de Kantega SSO para Confluence

Para permitir que los usuarios de Azure AD inicien sesión en Confluence, tienen que aprovisionarse en Confluence. En el caso de Kantega SSO para Confluence, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la empresa de Kantega SSO para Confluence como administrador.

1. Mantenga el mouse encima del icono de engranaje y haga clic en **Administración de usuarios**.

    ![Captura de pantalla que muestra el icono de engranaje y la opción "User management" (Administración de usuarios) seleccionada.](./media/kantegassoforconfluence-tutorial/user-management.png)

1. En la sección Usuarios, haga clic en la pestaña **Agregar usuarios**. En la página del cuadro de diálogo **Agregar un usuario**, realice los siguientes pasos:

    ![Agregar empleado](./media/kantegassoforconfluence-tutorial/create-user.png)

    a. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    b. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario, por ejemplo, Britta Simon.

    c. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.

    e. Haga clic en **Confirmar contraseña** y vuelva a escribir la contraseña.

    f. Haga clic en el botón **Agregar**.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Kantega SSO para Confluence, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Kantega SSO para Confluence e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Kantega SSO para Confluence para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Kantega SSO para Confluence en Aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de Kantega SSO para Confluence para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Kantega SSO para Confluence, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con las aplicaciones de Microsoft Defender para la nube](/cloud-app-security/proxy-deployment-aad).
