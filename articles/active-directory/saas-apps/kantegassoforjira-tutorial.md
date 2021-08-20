---
title: 'Tutorial: Integración de Azure Active Directory con Kantega SSO for JIRA | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Kantega SSO for JIRA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: 2bb28f9cc8a5e08c335c655f581453255463175b
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572679"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Tutorial: Integración de Azure Active Directory con Kantega SSO for JIRA

En este tutorial aprenderá a integrar Kantega SSO for JIRA con Azure Active Directory (Azure AD). Al integrar Kantega SSO for JIRA con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Kantega SSO for JIRA.
* Permitir que los usuarios inicien sesión automáticamente en Kantega SSO for JIRA con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Kantega SSO for JIRA, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).

* Suscripción habilitada para el inicio de sesión único de Kantega SSO for JIRA.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Kantega SSO for JIRA admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="add-kantega-sso-for-jira-from-the-gallery"></a>Adición de Kantega SSO for JIRA desde la galería

Para configurar la integración de Kantega SSO for JIRA en Azure AD, tiene que agregar Kantega SSO for JIRA desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Kantega SSO for JIRA** en el cuadro de búsqueda.
1. Seleccione **Kantega SSO for JIRA** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-jira"></a>Configuración y prueba del inicio de sesión único de Azure AD para Kantega SSO for JIRA

Configure y pruebe el inicio de sesión único de Azure AD con Kantega SSO for JIRA mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Kantega SSO for JIRA.

Para configurar y probar el inicio de sesión único de Azure AD con Kantega SSO for JIRA, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Kantega SSO for JIRA](#configure-kantega-sso-for-jira-sso)** : para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Kantega SSO for JIRA](#create-kantega-sso-for-jira-test-user)** : para tener un homólogo de B.Simon en Kantega SSO for JIRA que esté vinculado a la representación de Azure AD del usuario.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Kantega SSO for JIRA**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Estos valores se reciben durante la configuración del complemento de Jira, que se explica más adelante en el tutorial.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

7. En la sección **Set up Kantega SSO for JIRA** (Configurar Kantega SSO for JIRA), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Kantega SSO for JIRA mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Kantega SSO for JIRA**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-kantega-sso-for-jira-sso"></a>Configuración del inicio de sesión único en Kantega SSO for JIRA

1. En otra ventana del explorador web, inicie sesión en el servidor local de JIRA como administrador.

1. Mantenga el mouse encima del icono de engranaje y haga clic en **Complementos**.

    ![Captura de pantalla que muestra el icono de engranaje seleccionado y la opción "Complementos" seleccionada en el menú desplegable.](./media/kantegassoforjira-tutorial/settings.png)

1. En la sección de la pestaña Complementos, haga clic en **Find new add-ons** (Buscar nuevos complementos). Busque **Kantega SSO for JIRA (SAML & Kerberos)** y haga clic en el botón **Instalar** para instalar el nuevo complemento SAML.

    ![Captura de pantalla que muestra la sección "Find new Add-ons" (Buscar nuevos complementos) con "Kantego S S O for JIRA (S A M L & Kerberos)" en el cuadro de búsqueda y el botón "Instalar" seleccionado.](./media/kantegassoforjira-tutorial/install-tab.png)

1. Se inicia la instalación del complemento.

    ![Captura de pantalla que muestra el cuadro de diálogo "Instalando" del complemento.](./media/kantegassoforjira-tutorial/installation.png)

1. Una vez completada la instalación. Haga clic en **Cerrar**.

    ![Captura de pantalla que muestra el cuadro de diálogo "Installed and ready to go!" (Instalado y listo para usarse) con la acción "Cerrar" seleccionada.](./media/kantegassoforjira-tutorial/close-tab.png)

1.  Haga clic en **Administrar**.

    ![Captura de pantalla que muestra la página de aplicación "Kantega S S O" con el botón "Administrar" seleccionado.](./media/kantegassoforjira-tutorial/manage-tab.png)
    
1. El nuevo complemento aparece en **INTEGRACIONES**. Haga clic en **Configurar** para configurar el nuevo complemento.

    ![Captura de pantalla que muestra "INTEGRACIONES" en el menú de navegación de la izquierda resaltado y el botón "Configurar" seleccionado en la sección "Administrar complementos".](./media/kantegassoforjira-tutorial/integration.png)

1. En la sección **SAML**. Seleccione **Azure Active Directory (Azure AD)** en la lista desplegable **Agregar proveedor de identidades**.

    ![Captura de pantalla que muestra el menú desplegable "Agregar proveedor de identidades" con la opción "Azure Active Directory (Azure A D)" seleccionada.](./media/kantegassoforjira-tutorial/identity-provider.png)

1. Seleccione el nivel de suscripción **Básica**.

    ![Captura de pantalla que muestra la sección "Preparación de Azure A D" con la opción "Básica" seleccionada.](./media/kantegassoforjira-tutorial/basic-tab.png)

1. En la sección **Agregar propiedades**, siga estos pasos: 

    ![Captura de pantalla que muestra la sección "Propiedades de la aplicación" con el cuadro de texto "U R L de I D. de aplicación" y el botón Copiar resaltados, y el botón "Siguiente" seleccionado.](./media/kantegassoforjira-tutorial/properties.png)

    1. Copie el valor de **URI de id. de aplicación** y úselo en los campos **Identificador, URL de respuesta y URL de inicio de sesión** de la sección **Configuración básica de SAML**  de Azure Portal.

    1. Haga clic en **Next**.

1. En la sección **Importar metadatos**, siga estos pasos: 

    ![Captura de pantalla que muestra la sección "Importación de metadatos" con la opción "Archivo de metadatos en mi equipo" seleccionada.](./media/kantegassoforjira-tutorial/metadata.png)

    1. Seleccione **Archivo de metadatos en el equipo** y cargue el archivo de metadatos que descargó desde Azure Portal.

    1. Haga clic en **Next**.

1. En la sección **Name and SSO location** (Nombre y ubicación de SSO), siga estos pasos:

    ![Captura de pantalla que muestra la opción "Name and S S O location" (Nombre y ubicación de S S O) y el botón "Siguiente" seleccionado.](./media/kantegassoforjira-tutorial/location.png)

    1. Agregue el nombre del proveedor de identidades en el cuadro de texto **Nombre del proveedor de identidades** (por ejemplo, Azure AD).

    1. Haga clic en **Next**.

1. Compruebe el certificado de firma y haga clic en **Siguiente**.

    ![Captura de pantalla que muestra la sección "Comprobación de firmas" con el botón "Siguiente" seleccionado.](./media/kantegassoforjira-tutorial/certificate.png)

1. En la sección **Cuentas de usuario de JIRA**, siga estos pasos:

    ![Captura de pantalla que muestra la sección "Cuentas de usuario de JIRA" con la opción "Create users in JIRA's Internal Directory if needed" (Crear usuarios en el directorio interno de JIRA si es necesario) resaltada y el botón "Siguiente" seleccionado.](./media/kantegassoforjira-tutorial/accounts.png)

    1. Seleccione **Create users in JIRA's internal Directory if needed** (Crear usuarios en el directorio interno de JIRA si es necesario) y escriba el nombre adecuado del grupo de usuarios (puede ser un número múltiple de grupos separados por coma).

    1. Haga clic en **Next**.

1. Haga clic en **Finalizar**

    ![Captura de pantalla que muestra la sección "Resumen" con el botón "Finalizar" seleccionado.](./media/kantegassoforjira-tutorial/finish-tab.png)

1. En la sección **Known domains for Azure AD** (Dominios conocidos para Azure AD), siga estos pasos:

    ![Configurar inicio de sesión único](./media/kantegassoforjira-tutorial/save-tab.png)

    1. Seleccione **Known domains** (Dominios conocidos) en el panel izquierdo de la página.

    2. Escriba el nombre de dominio en el cuadro de texto **Known domains** (Dominios conocidos).

    3. Haga clic en **Save**(Guardar).

### <a name="create-kantega-sso-for-jira-test-user"></a>Creación de un usuario de prueba en Kantega SSO for JIRA

Para que los usuarios de Azure AD puedan iniciar sesión en JIRA, es preciso aprovisionarlos en JIRA. En Kantega SSO for JIRA, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el servidor local de JIRA.

1. Mantenga el mouse encima del icono de engranaje y haga clic en **Administración de usuarios**.

    ![Captura de pantalla que muestra el icono de engranaje seleccionado y la opción "Administración de usuarios" seleccionada en el menú desplegable.](./media/kantegassoforjira-tutorial/user.png) 

1. En la sección de la pestaña **Administración de usuarios**, haga clic en **Crear usuario**.

    ![Captura de pantalla que muestra la sección "Administración de usuarios" con el botón "Crear usuario" seleccionado.](./media/kantegassoforjira-tutorial/create-user.png) 

1. En la página del cuadro de diálogo **"Create New User"** (Crear nuevo usuario), realice los pasos siguientes:

    ![Agregar empleado](./media/kantegassoforjira-tutorial/new-user.png) 

    1. En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    2. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario, por ejemplo, Britta Simon.

    3. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    4. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.

    5. Haga clic en **Crear usuario**.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Kantega SSO for JIRA, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Kantega SSO for JIRA e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Kantega SSO for JIRA para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Kantega SSO for JIRA en Mis aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de Kantega SSO for JIRA para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Kantega SSO for JIRA, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
