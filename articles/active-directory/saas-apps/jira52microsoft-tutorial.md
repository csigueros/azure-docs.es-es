---
title: 'Tutorial: Integración del inicio de sesión único de Azure Active Directory con JIRA SAML SSO by Microsoft (versión 5.2)'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y JIRA SAML SSO by Microsoft (versión 5.2).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 5254b7bcf851685572adbdc3d18c1fc5560a34d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633243"
---
# <a name="tutorial-azure-ad-sso-integration-with-jira-saml-sso-by-microsoft-v52"></a>Tutorial: Integración del inicio de sesión único de Azure Active Directory con JIRA SAML SSO by Microsoft (versión 5.2)

En este tutorial, aprenderá a integrar JIRA SAML SSO by Microsoft (versión 5.2) con Azure Active Directory (Azure AD). Al integrar JIRA SAML SSO by Microsoft (v5.2) con Azure AD, se puede realizar lo siguiente:

* Controlar en Azure AD quién tiene acceso a JIRA SAML SSO by Microsoft (v5.2).
* Permitir que los usuarios inicien sesión automáticamente en JIRA SAML SSO by Microsoft (v5.2) con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="description"></a>Descripción

Use la cuenta de Microsoft Azure Active Directory con el servidor JIRA de Atlassian para habilitar el inicio de sesión único. De esta forma, todos los usuarios de la organización pueden usar las credenciales de Azure AD para iniciar sesión en la aplicación JIRA. Este complemento usa SAML 2.0 para la federación.

## <a name="prerequisites"></a>Prerequisites

Para configurar la integración de Azure AD con JIRA SAML SSO by Microsoft (versión 5.2), se necesitan los elementos siguientes:

- Una suscripción de Azure AD.
- JIRA Core y Software 5.2 se deben instalar y configurar en una versión de 64 bits de Windows.
- El servidor JIRA es compatible con HTTPS.
- Tenga en cuenta que las versiones admitidas para el complemento JIRA se mencionan en la sección siguiente.
- El servidor JIRA es accesible en Internet, especialmente a la página de inicio de sesión de Azure AD para la autenticación y debe poder recibir el token de Azure AD.
- Las credenciales de administrador se configuran en JIRA.
- WebSudo está deshabilitado en JIRA.
- Usuario de prueba creado en la aplicación de servidor JIRA.

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción de JIRA. Pruebe primero la integración en el entorno de desarrollo o de ensayo de la aplicación y, después, use el entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).

## <a name="supported-versions-of-jira"></a>Versiones compatibles de JIRA

* Jira Core y Software: 5.2.
* JIRA también admite desde 6.0 hasta 7.12. Para más información, haga clic en [JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md).

> [!NOTE]
> Tenga en cuenta que nuestro complemento de JIRA también funciona con Ubuntu 16.04.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* JIRA SAML SSO by Microsoft (versión 5.2) admite el inicio de sesión único iniciado por **SP**.

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Agregar JIRA SAML SSO by Microsoft (versión 5.2) desde la galería

Para configurar la integración de JIRA SAML SSO by Microsoft (versión 5.2) en Azure AD, deberá agregar JIRA SAML SSO by Microsoft (versión 5.2) desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **JIRA SAML SSO by Microsoft (v5.2)** en el cuadro de búsqueda.
1. Seleccione **JIRA SAML SSO by Microsoft (v5.2)** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Configuración y prueba del inicio de sesión único de Azure AD para JIRA SAML SSO by Microsoft (v5.2)

En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con JIRA SAML SSO by Microsoft (versión 5.2) con un usuario de prueba llamado **Britta Simon**. Para que el inicio de sesión único funcione, debe establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de JIRA SAML SSO by Microsoft (v5.2).

Para configurar y probar el inicio de sesión único de Azure AD con JIRA SAML SSO by Microsoft (v5.2), siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de JIRA SAML SSO by Microsoft (v5.2)](#configure-jira-saml-sso-by-microsoft-v52-sso)** , para configurar los valores del inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de JIRA SAML SSO by Microsoft (versión 5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)** : para tener un homólogo de Britta Simon en JIRA SAML SSO by Microsoft (versión 5.2) que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

1. En Azure Portal, en la página de integración de aplicaciones de **JIRA SAML SSO by Microsoft (v5.2)** , busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<domain:port>/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<domain:port>/plugins/servlet/saml/auth`

    c. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. En caso de que sea una dirección URL con nombre, el puerto es opcional. Estos valores se reciben durante la configuración del complemento de Jira, que se explica más adelante en el tutorial.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

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

En esta sección, va a permitir que B.Simon use el inicio de sesión único de Azure concediéndole acceso a JIRA SAML SSO by Microsoft (v5.2).

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **JIRA SAML SSO by Microsoft (versión 5.2)** .
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>Configuración del inicio de sesión único de JIRA SAML SSO by Microsoft (v5.2)

1. En otra ventana del explorador web, inicie sesión en la instancia de JIRA como administrador.

2. Mantenga el mouse encima del icono de engranaje y haga clic en **Complementos**.

    ![Captura de pantalla que muestra la opción Complementos seleccionada en el menú Configuración.](./media/jira52microsoft-tutorial/menu.png)

3. En la sección de la pestaña Complementos, haga clic en **Administrar complementos**.

    ![Captura de pantalla que muestra la opción Administrar complementos seleccionada en la pestaña Complementos.](./media/jira52microsoft-tutorial/dashboard.png)

4. Descargue el complemento del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=56521). Cargue manualmente el complemento proporcionado por Microsoft mediante el menú **Cargar complemento**. La descarga del complemento está contemplada en el [Acuerdo de servicio de Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Captura de pantalla que muestra la opción Administrar complementos con el vínculo Upload add-on destacado.](./media/jira52microsoft-tutorial/service.png)

5. Una vez instalado el complemento, aparece en la sección de complementos **Instalados por el usuario**. Haga clic en **Configurar** para configurar el nuevo complemento.

    ![Captura de pantalla que muestra la sección Inicio de sesión único de SAML de Azure A D para Jira con la opción Configurar seleccionada.](./media/jira52microsoft-tutorial/configure-plugin.png)

6. Siga estos pasos en la página de configuración:

    ![Captura de pantalla que muestra la página de configuración del conector de inicio de sesión único de Jira para Microsoft](./media/jira52microsoft-tutorial/configuration.png)

    > [!TIP]
    > Asegúrese de que hay un solo certificado asignado a la aplicación, de forma que no se produzca ningún error en la resolución de los metadatos. Si hay varios certificados, después de resolver los metadatos, el administrador recibe un error.

    a. En el cuadro de texto **URL de metadatos**, pegue la **dirección URL de metadatos de federación de aplicación** que copió en Azure Portal y haga clic en el botón **Resolver**. Se lee la dirección URL de metadatos de IdP y se rellena toda la información de campos.

    b. Copie los valores **Identificador, Dirección URL de respuesta y Dirección URL de inicio de sesión**, y péguelos en los cuadros de texto **Identificador, Dirección URL de respuesta y Dirección URL de inicio de sesión** respectivamente en la sección **Configuración básica de SAML** de Azure Portal.

    c. En **Nombre del botón de inicio de sesión** escriba el nombre del botón que la organización quiere que los usuarios vean en la pantalla de inicio de sesión.

    d. En **SAML User ID Locations** (Ubicaciones de Id. de usuario de SAML) seleccione **User ID is in the NameIdentifier element of the Subject statement** (El Id. de usuario está en el elemento NameIdentifier de la instrucción Subject) o **User ID is in an Attribute element** (El Id. de usuario está en un elemento Attribute).  Este identificador debe ser el identificador de usuario de JIRA. Si el identificador de usuario no coincide, el sistema no permitirá que los usuarios inicien sesión.

    > [!Note]
    > La ubicación del Id. de usuario de SAML predeterminada es el identificador de nombre. Puede cambiarlo a una opción de atributo y escribir el nombre de atributo adecuado.

    e. Si selecciona la opción **User ID is in an Attribute element** (El Id. de usuario está en un elemento Attribute), escriba el nombre del atributo cuando se espera el id. de usuario en el cuadro de texto **Nombre del atributo**. 

    f. Si se usa el dominio federado (por ejemplo, ADFS, etc.) con Azure AD, haga clic en la opción **Habilitar detección de dominio principal** y configure el **nombre de dominio**.

    g. En **Nombre de dominio**, escriba el nombre del dominio en el caso de inicios de sesión basados en ADFS.

    h. Active **Enable Single Sign out** (Habilitar cierre de sesión único) si quiere que se cierre la sesión de Azure AD cuando un usuario cierre la sesión de JIRA. 

    i. Haga clic en el botón **Save** (Guardar) para guardar la configuración.

    > [!NOTE]
    > Para más información sobre la instalación y la solución de problemas, visite [MS JIRA SSO Connector Admin Guide](./ms-confluence-jira-plugin-adminguide.md) (Guía de administración del conector SSO de MS JIRA), que contiene una sección de [preguntas frecuentes](./ms-confluence-jira-plugin-adminguide.md) para ayudarle.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Creación de un usuario de prueba de JIRA SAML SSO by Microsoft (versión 5.2)

Para permitir que los usuarios de Azure AD inicien sesión en el servidor local de JIRA, se deben aprovisionar en el servidor local de JIRA.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el servidor local de JIRA.

2. Mantenga el mouse encima del icono de engranaje y haga clic en **Administración de usuarios**.

    ![Captura de pantalla que muestra la opción Administración de usuarios seleccionada en el menú Configuración.](./media/jira52microsoft-tutorial/user.png)

3. Se le redirigirá a la página de acceso de administrador para especificar la **contraseña** y haga clic en el botón **Confirmar**.

    ![Captura de pantalla que muestra la página Administrator Access (Acceso como administrador) donde puede especificar las credenciales.](./media/jira52microsoft-tutorial/access.png)

4. En la sección de la pestaña **Administración de usuarios**, haga clic en **Crear usuario**.

    ![Captura de pantalla que muestra la pestaña Administración de usuarios en la que puede crear un usuario.](./media/jira52microsoft-tutorial/create-user.png) 

5. En la página del cuadro de diálogo **"Create New User"** (Crear nuevo usuario), realice los pasos siguientes:

    ![Captura de pantalla que muestra el cuadro de diálogo Create new user (Crear nuevo usuario) donde puede especificar la información que se indica en este paso.](./media/jira52microsoft-tutorial/new-user.png)

    a. En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    b. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario, por ejemplo, Britta Simon.

    c. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.

    e. Haga clic en **Crear usuario**.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de JIRA SAML SSO by Microsoft (v5.2), desde donde puede poner en marcha el flujo de inicio de sesión. 

* Acceda directamente a la URL de inicio de sesión de JIRA SAML SSO by Microsoft (v5.2) y ponga en marcha el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Si hace clic en el icono de JIRA SAML SSO by Microsoft (v5.2) en Mis aplicaciones, se le redirigirá a la dirección URL de inicio de sesión único de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado JIRA SAML SSO by Microsoft (v5.2), puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).