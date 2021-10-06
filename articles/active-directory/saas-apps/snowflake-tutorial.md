---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Snowflake'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Snowflake.
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
ms.openlocfilehash: 32e150a9a3db521fb097426f1c5ec5fb38fa6064
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677994"
---
# <a name="tutorial-azure-ad-sso-integration-with-snowflake"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Snowflake

En este tutorial, aprenderá a integrar Snowflake con Azure Active Directory (Azure AD). Al integrar Snowflake con Azure AD, se puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Snowflake.
* Permitir que los usuarios inicien sesión automáticamente en Snowflake con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Snowflake, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único de Snowflake.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial configurará y probará el inicio de sesión único de Azure AD en un entorno de prueba.

* Snowflake admite el inicio de sesión único iniciado por **SP e IDP**.
* Snowflake admite el [aprovisionamiento y desaprovisionamiento de usuarios de forma automática](snowflake-provisioning-tutorial.md) (recomendado).

## <a name="add-snowflake-from-the-gallery"></a>Adición de Snowflake desde la galería

Para configurar la integración de Snowflake en Azure AD, será preciso que agregue Snowflake desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Snowflake** en el cuadro de búsqueda.
1. Seleccione **Snowflake** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Configuración y prueba del inicio de sesión único de Azure AD para Snowflake

Configure y pruebe el inicio de sesión único de Azure AD con Snowflake mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Snowflake.

Para configurar y probar el inicio de sesión único de Azure AD con Snowflake, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Snowflake](#configure-snowflake-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación del usuario de prueba de Snowflake](#create-snowflake-test-user)** : para tener un homólogo de B. Simon en Snowflake vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Snowflake**, busque la sección **Administrar** y seleccione **inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por **IDP**:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por SP:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    b. En el cuadro de texto **URL de cierre de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`.

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de Snowflake](https://support.snowflake.net/s/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up Snowflake** (Configurar Snowflake), copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B. Simon acceda a Snowflake mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Snowflake**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-snowflake-sso"></a>Configuración del inicio de sesión único de Snowflake

1. En otra ventana del explorador web, inicie sesión en Snowflake como administrador de seguridad.

1. Cambie de rol (**Switch Role**) en **ACCOUNTADMIN**, haciendo clic en el **perfil** en la parte superior derecha de la página.

    > [!NOTE]
    > Esto es independiente del contexto que haya seleccionado en la esquina superior derecha bajo el nombre de usuario.
    
    ![El administrador de Snowflake](./media/snowflake-tutorial/account.png)

1. Abra el **certificado Base 64 descargado** en el Bloc de notas. Copie el valor entre "---BEGIN CERTIFICATE---" y "---END CERTIFICATE---" y pegue esto en las comillas junto a **certificado** a continuación. En el cuadro de texto **ssoUrl** (Dirección URL de cierre de sesión), pegue el valor de la **URL de inicio de sesión** que ha copiado de Azure Portal. Seleccione **Todas las consultas** y haga clic en **Ejecutar**.

   ![sql de Snowflake](./media/snowflake-tutorial/certificate.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```

### <a name="create-snowflake-test-user"></a>Creación del usuario de prueba de Snowflake

Para permitir que los usuarios de Azure AD inicien sesión en Snowflake, tienen que aprovisionarse en Snowflake. En Snowflake, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en Snowflake como administrador de seguridad.

2. Cambie de rol (**Switch Role**) en **ACCOUNTADMIN**, haciendo clic en el **perfil** en la parte superior derecha de la página.  

    ![El administrador de Snowflake](./media/snowflake-tutorial/account.png)

3. Para crear el usuario, ejecute la siguiente consulta SQL y asegúrese de que "Login name" (Nombre de inicio de sesión) esté establecido en el nombre de usuario de Azure AD en la hoja de cálculo, como se muestra a continuación.

    ![adminsql de Snowflake](./media/snowflake-tutorial/user.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Snowflake, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya directamente a la URL de inicio de sesión de Snowflake y comience el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Snowflake para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Proware en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Snowflake para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Snowflake, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).