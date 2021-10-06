---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Palo Alto Networks - Admin UI | Microsoft Docs'
description: 'Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Palo Alto Networks: interfaz de usuario de administración.'
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
ms.openlocfilehash: 9699f985d0e18153c224977b39900b67aaf205c5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746705"
---
# <a name="tutorial-azure-ad-sso-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Palo Alto Networks - Admin UI

En este tutorial, aprenderá a integrar Palo Alto Networks - Admin UI con Azure Active Directory (Azure AD). Al integrar Palo Alto Networks - Admin UI con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Palo Alto Networks - Admin UI.
* Permitir que los usuarios inicien sesión automáticamente en Palo Alto Networks - Admin UI con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Palo Alto Networks - Admin UI.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Palo Alto Networks - Admin UI admite el inicio de sesión único iniciado por **SP**.
* Palo Alto Networks - Admin UI admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Agregación de Palo Alto Networks: interfaz de usuario de administración

Para configurar la integración de Palo Alto Networks: interfaz de usuario de administración en Azure AD, es preciso agregar Palo Alto Networks: interfaz de usuario de administración desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Palo Alto Networks: Admin UI** en el cuadro de búsqueda.
1. Seleccione **Palo Alto Networks: Admin UI** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---admin-ui"></a>Configuración y prueba del inicio de sesión único de Azure AD en Palo Alto Networks - Admin UI

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Palo Alto Networks: Admin UI con un usuario de prueba llamado **B.Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Palo Alto Networks - Admin UI.

Para configurar y probar el inicio de sesión único de Azure AD con Palo Alto Networks - Admin UI, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Palo Alto Networks: Admin UI](#configure-palo-alto-networks---admin-ui-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Palo Alto Networks: Admin UI](#create-palo-alto-networks---admin-ui-test-user)** : para tener un homólogo de B.Simon en Palo Alto Networks: Admin UI que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Palo Alto Networks - Admin UI**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    b. En el cuadro de texto **Dirección URL de respuesta**, escriba la dirección URL del Servicio de consumidor de aserciones (ACS) con el siguiente formato: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`.

    c. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<Customer Firewall FQDN>/php/login.php`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con [el equipo de soporte técnico del cliente de Palo Alto Networks: interfaz de usuario de administración](https://support.paloaltonetworks.com/support) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.
    >
    > El puerto 443 es necesario en los campos **Identificador** y **Dirección URL de respuesta**, ya que estos valores están codificados de forma rígida en el firewall de Palo Alto. Si se quita el número de puerto, se producirá un error durante el inicio de sesión.

    > El puerto 443 es necesario en los campos **Identificador** y **Dirección URL de respuesta**, ya que estos valores están codificados de forma rígida en el firewall de Palo Alto. Si se quita el número de puerto, se producirá un error durante el inicio de sesión.

1. La aplicación Palo Alto Networks - Admin UI espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

   > [!NOTE]
   > Como los valores de atributo son solo ejemplos, asigne los valores adecuados para *username* y *adminrole*. Hay otro atributo opcional, *accessdomain*, que se usa para restringir el acceso de administrador a sistemas virtuales específicos en el firewall.

1. Además de lo anterior, la aplicación Palo Alto Networks - Admin UI espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre |  Atributo de origen|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > El valor de **Nombre**, que se muestra más arriba como _adminrole_, debe ser el mismo valor que para el _atributo de rol de administrador_ configurado en el paso 12 de la sección **[Configuración del inicio de sesión único de Palo Alto Networks - Admin UI](#configure-palo-alto-networks---admin-ui-sso)** . El valor de **Atributo de origen**, que se muestra más arriba como _customadmin_, debe ser el mismo valor que para el _nombre de perfil de rol administrador_ configurado en el paso 9 de la sección **[Configuración del inicio de sesión único de Palo Alto Networks - Admin UI](#configure-palo-alto-networks---admin-ui-sso)** .  

    > [!NOTE]
    > Para más información sobre los atributos, consulte los siguientes artículos:
    > * [Perfil de rol administrativo de Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Dominio de acceso de dispositivo de Admin UI (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar Palo Alto Networks - Admin UI**, copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon acceda a Palo Alto Networks: Admin UI mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Palo Alto: interfaz de usuario de administrador**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-palo-alto-networks---admin-ui-sso"></a>Configuración del inicio de sesión único de Palo Alto Networks: Admin UI

1. Abra la interfaz de usuario de administración del firewall de Palo Alto Networks como administrador en una nueva ventana.

2. Seleccione la pestaña **Device** (Dispositivo).

    ![Captura de pantalla que muestra la pestaña Device (Dispositivo).](./media/paloaltoadmin-tutorial/device.png)

3. En el panel izquierdo, seleccione **SAML Identity Provider** (Proveedor de identidades de SAML) y, luego, seleccione **Import** (Importar) para importar el archivo de metadatos.

    ![Captura de pantalla que muestra el botón Import (Importar) para importar el archivo de metadatos.](./media/paloaltoadmin-tutorial/admin.png)

4. En la ventana **SAML Identify Provider Server Profile Import** (Importación del perfil de servidor del proveedor de identidades de SAML), haga lo siguiente:

    ![Captura de pantalla que muestra la ventana "SAML Identify Provider Server Profile Import" (Importación del perfil de servidor del proveedor de identidades de SAML).](./media/paloaltoadmin-tutorial/profile.png)

    a. En el cuadro **Profile Name** (Nombre del perfil), proporcione un nombre (por ejemplo, **AzureAD Admin UI**).

    b. En **Identity Provider Metadata** (Metadatos del proveedor de identidades), seleccione **Browse** (Examinar) y elija el archivo metadata.xml que descargó anteriormente de Azure Portal.

    c. Desactive la casilla **Validate Identity Provider Certificate** (Validar certificado de proveedor de identidades).

    d. Seleccione **Aceptar**.

    e. Para confirmar las configuraciones en el firewall, seleccione **Commit** (Confirmar).

5. En el panel izquierdo, seleccione **SAML Identity Provider** (Proveedor de identidades de SAML) y elija el perfil del proveedor de identidades de SAML (por ejemplo, **AzureAD Admin UI**) que creó en el paso anterior.

    ![Captura de pantalla que muestra el perfil del proveedor de identidades SAML.](./media/paloaltoadmin-tutorial/azure.png)

6. En la ventana **SAML Identity Provider Server Profile** (Perfil de servidor del proveedor de identidades de SAML), realice lo siguiente:

    ![Captura de pantalla que muestra la ventana "SAML Identity Provider Server Profile" (Perfil de servidor del proveedor de identidades de SAML).](./media/paloaltoadmin-tutorial/server.png)
  
    a. En el cuadro **Identity Provider SLO URL** (Dirección URL de SLO del proveedor de identidades), sustituya la dirección URL de SLO importada anteriormente y agregue la siguiente dirección URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
  
    b. Seleccione **Aceptar**.

7. En la interfaz de usuario del firewall de Palo Alto Networks, seleccione **Device** (Dispositivo) y elija **Admin Roles** (Roles de administrador).

8. Seleccione el botón **Agregar**.

9. En la ventana **Admin Role Profile** (Perfil de rol de administrador), en el cuadro **Name** (Name), proporcione un nombre para el rol de administrador (por ejemplo, **fwadmin**). El nombre del rol de administrador debe coincidir con el nombre de atributo del rol de administrador de SAML enviado por el proveedor de identidades. El nombre de rol de administrador y el valor que se crearon en la sección **Atributos de usuario** de Azure Portal.

    ![Configure el rol de administrador de Palo Alto Networks.](./media/paloaltoadmin-tutorial/role.png)
  
10. En la interfaz de usuario de administrador del firewall, seleccione **Device** (Dispositivo) y elija **Authentication Profile** (Perfil de autenticación).

11. Seleccione el botón **Agregar**.

12. En la ventana **Authentication Profile** (Perfil de autenticación), haga lo siguiente: 

    ![Captura de pantalla que muestra la ventana "Authentication Profile" (Perfil de autenticación).](./media/paloaltoadmin-tutorial/authentication.png)

    a. En el cuadro **Name** (Nombre), proporcione un nombre (por ejemplo, **AzureSAML_Admin_AuthProfile**).

    b. En la lista desplegable **Type** (Tipo), seleccione **SAML**. 

    c. En la lista desplegable **IdP Server Profile** (Perfil del servidor de IdP), seleccione el perfil adecuado de servidor del proveedor de identidades de SAML (por ejemplo, **AzureAD Admin UI**).

    d. Active la casilla **Enable Single Logout** (Habilitar el cierre de sesión único).

    e. En el cuadro **Admin Role Attribute** (Atributo de rol de administrador), escriba el nombre del atributo (por ejemplo, **adminrole**).

    f. Seleccione la pestaña **Advanced** (Opciones avanzadas) y, en **Allow List** (Lista de permitidos), seleccione **Add** (Agregar).

    ![Captura de pantalla que muestra el botón "Add" (Agregar) de la pestaña Advanced (Opciones avanzadas).](./media/paloaltoadmin-tutorial/allowlist.png)

    g. Active la casilla **All** (Todos) o seleccione los usuarios y grupos que se pueden autenticar con este perfil.  
    Cuando un usuario se autentica, el firewall compara el nombre de usuario o grupo asociado con las entradas de esta lista. Si no agrega entradas, ningún usuario puede autenticarse.

    h. Seleccione **Aceptar**.

13. Para permitir que los administradores usen SSO de SAML mediante Azure, seleccione **Device** (Dispositivo)  > **Setup** (Configuración). En el panel **Setup** (Configuración), seleccione la pestaña **Management** (Administración) y, en **Authentication Settings** (Configuración de autenticación), seleccione el botón de engranaje **Settings** (Configuración).

    ![Captura de pantalla que muestra el botón Settings (Configuración).](./media/paloaltoadmin-tutorial/setup.png)

14. Seleccione el perfil de autenticación de SAML que creó en la ventana Perfil de autenticación (por ejemplo, **AzureSAML_Admin_AuthProfile**).

    ![Captura de pantalla que muestra el campo Authentication Profile (Perfil de autenticación).](./media/paloaltoadmin-tutorial/settings.png)

15. Seleccione **Aceptar**.

16. Para confirmar la configuración, seleccione **Commit** (Confirmar).

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Creación de un usuario de prueba de Palo Alto Networks - Admin UI

Palo Alto Networks - Admin UI admite el aprovisionamiento de usuarios Just-In-Time. Si un usuario aún no existe, se crea automáticamente en el sistema después de una autenticación correcta. No es necesaria ninguna acción por su parte para crear el usuario.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Palo Alto Networks - Admin UI, desde donde puede comenzar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Palo Alto Networks - Admin UI y comience el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Palo Alto Networks - Admin UI en Aplicaciones, se debería iniciar sesión automáticamente en la instancia de Palo Alto Networks - Admin UI para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Palo Alto Networks - Admin UI, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).