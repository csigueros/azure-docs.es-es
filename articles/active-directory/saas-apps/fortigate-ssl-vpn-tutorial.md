---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con FortiGate SSL VPN | Microsoft Docs'
description: Obtenga información sobre los pasos necesarios para integrar FortiGate SSL VPN con Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: jeedes
ms.openlocfilehash: e9663ee95b8ae9b109016e409855ef171c368793
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690869"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con FortiGate SSL VPN

En este tutorial, obtendrá información sobre cómo integrar FortiGate SSL VPN con Azure Active Directory (Azure AD). Al integrar FortiGate SSL VPN con Azure AD, puede hacer lo siguiente:

* Usar Azure AD para determinar quién puede acceder a FortiGate SSL VPN.
* Permitir que los usuarios inicien sesión automáticamente en FortiGate SSL VPN con sus cuentas de Azure AD.
* Administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en FortiGate SSL VPN.

## <a name="tutorial-description"></a>Descripción del tutorial

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

FortiGate SSL VPN admite el inicio de sesión único iniciado por SP.


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>Incorporación de FortiGate SSL VPN desde la galería

Para configurar la integración de FortiGate SSL VPN en Azure AD, es preciso agregar FortiGate SSL VPN desde la galería a la lista de aplicaciones SaaS administradas:

1. Inicie sesión en Azure Portal con una cuenta profesional o educativa o con una cuenta Microsoft personal.
1. En el panel izquierdo, seleccione **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **FortiGate SSL VPN** en el cuadro de búsqueda.
1. Seleccione **FortiGate SSL VPN** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Configuración y prueba del inicio de sesión único de Azure AD para FortiGate SSL VPN

Configure y pruebe el inicio de sesión único de Azure AD con FortiGate SSL VPN mediante un usuario de prueba llamado B.Simon. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de FortiGate SSL VPN.

Para configurar y probar el inicio de sesión único de Azure AD con FortiGate SSL VPN, siga estos pasos generales:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** para probar el inicio de sesión único de Azure AD.
    1. **[Concesión de acceso al usuario de prueba](#grant-access-to-the-test-user)** , para que ese usuario pueda usar el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de FortiGate SSL VPN](#configure-fortigate-ssl-vpn-sso)** en la aplicación.
    1. **Creación de un usuario de prueba de FortiGate SSL VPN** como homólogo de la representación de usuario de Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** para comprobar que la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal:

1. En Azure Portal, en la página de integración de la aplicación **FortiGate SSL VPN**, en la sección **Administrar**, seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, seleccione el botón de lápiz para **Configuración básica de SAML** para editar la configuración:

   ![Captura de pantalla que muestra el botón de lápiz para editar la configuración básica de SAML.](common/edit-urls.png)

1. En la página **Configuración del inicio de sesión único con SAML**, escriba los valores siguientes:

    a. En el cuadro **URL de inicio de sesión**, escriba una dirección URL que siga el patrón `https://<FQDN>/remote/saml/login`.

    b. En el cuadro **Identificador**, escriba una dirección URL que siga el patrón `https://<FQDN>/remote/saml/metadata`.

    c. En el cuadro **Dirección URL de respuesta**, escriba una dirección URL que siga el patrón `https://<FQDN>/remote/saml/login`.

    d. En el cuadro **URL de cierre de sesión**, escriba una dirección URL que siga el patrón `https://<FQDN>/remote/saml/logout`.

    > [!NOTE]
    > Estos valores son tan solo patrones. Debe usar los valores reales para **Dirección URL de inicio de sesión**, **Identificador**, **Dirección URL de respuesta** y **Dirección URL de inicio de sesión**. Póngase en contacto con el [equipo de soporte técnico de Fortinet](https://support.fortinet.com) para obtener instrucciones. También puede consultar los patrones de ejemplo de la documentación de Fortinet y la sección **Configuración básica de SAML** en Azure Portal.

1. La aplicación FortiGate SSL VPN espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración. La siguiente captura de muestra la lista de atributos predeterminados.

    ![Captura de pantalla que muestra los atributos predeterminados.](common/default-attributes.png)

1. En la tabla siguiente se muestran las dos notificaciones adicionales que requiere FortiGate SSL VPN. Los nombres de estas notificaciones deben coincidir con los nombres que se usan en la sección **Configuración de la línea de comandos de FortiGate** de este tutorial. 

   | Nombre |  Atributo de origen|
   | ------------ | --------- |
   | username | user.userprincipalname |
   | group | user.groups |
   
   Para crear estas notificaciones adicionales:

   a. Seleccione **Editar** junto a **Atributos y notificaciones de usuario**.

   b. Seleccione **Agregar nueva notificación**.

   c. En **Nombre**, escriba **username**.

   d. En **Atributo de origen**, seleccione **user.userprincipalname**.

   e. Seleccione **Guardar**.

   f. Seleccione **Agregar una notificación de grupo**.

   g. Seleccione **Todos los grupos**.

   h. Seleccione la casilla **Personalizar nombre de la notificación del grupo**.

   i. En **Nombre**, escriba **group**.
   
   j. Seleccione **Guardar**.   

1. En la página **Configurar inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, seleccione el vínculo **Descargar** junto a **Certificado (Base64)** para descargar y guardar el certificado en el equipo:

    ![Captura de pantalla que muestra el vínculo de descarga del certificado.](common/certificatebase64.png)

1. En la sección **Configurar FortiGate SSL VPN**, copie las direcciones URL adecuadas según sus necesidades:

    ![Captura de pantalla que muestra las direcciones URL de configuración.](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**. Seleccione **Usuarios** y, a continuación, seleccione **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades de **usuario**, realice estos pasos:
   1. En el cuadro **Nombre**, escriba **B.Simon**.  
   1. En el cuadro **Nombre de usuario**, escriba \<username>@\<companydomain>.\<extension>. Por ejemplo, `B.Simon@contoso.com`.
   1. Seleccione **Mostrar contraseña** y, a continuación, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Seleccione **Crear**.

#### <a name="grant-access-to-the-test-user"></a>Concesión de acceso al usuario de prueba

En esta sección va a permitir que B.Simon acceda a FortiGate SSL VPN mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione **Aplicaciones empresariales** y, a continuación, seleccione **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **FortiGate SSL VPN**.
1. En la sección **Administrar** de la página de información general de la aplicación, seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista **Usuarios** y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. Si espera algún valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione el rol adecuado para el usuario en la lista. Haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

#### <a name="create-a-security-group-for-the-test-user"></a>Creación de un grupo de seguridad para el usuario de prueba

En esta sección, creará un grupo de seguridad en Azure Active Directory para el usuario de prueba. FortiGate usará este grupo de seguridad para conceder al usuario acceso a la red a través de la VPN.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**. A continuación, seleccione **Grupos**.
1. Seleccione **Nuevo grupo** en la parte superior de la pantalla.
1. En las propiedades del **Nuevo grupo**, siga estos pasos:
   1. En la lista **Tipo de grupo**, seleccione **Seguridad**.
   1. En el cuadro **Nombre del grupo**, escriba **FortiGateAccess**.
   1. En el cuadro **Descripción del grupo**, escriba **Group for granting FortiGate VPN access** (Grupo para conceder acceso a FortiGate VPN).
   1. En la configuración **Los roles de Azure AD se pueden asignar a un grupo (versión preliminar)** , seleccione **No**.
   1. En el cuadro **Tipo de pertenencia**, seleccione **Asignado**.
   1. En **Miembros**, seleccione **-no hay miembros seleccionados**.
   1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista **Usuarios** y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
   1. Seleccione **Crear**.
1. Una vez que vuelva a la sección **Grupos** de Azure Active Directory, busque el grupo **FortiGateAccess** y anote el valor de **Id. de objeto**. Lo necesitará más adelante.

### <a name="configure-fortigate-ssl-vpn-sso"></a>Configuración del inicio de sesión único de FortiGate SSL VPN

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Carga del certificado SAML de Base64 en el dispositivo FortiGate

Después de completar la configuración de SAML de la aplicación FortiGate en el inquilino, descargó el certificado SAML codificado en Base64. Debe cargar este certificado en el dispositivo FortiGate:

1. Inicie sesión en el portal de administración de la aplicación FortiGate.
1. En el panel izquierdo, seleccione **Sistema**.
1. En **Sistema**, seleccione **Certificados**.
1. Seleccione **Importar** > **Certificado remoto**.
1. Busque el certificado descargado de la implementación de la aplicación FortiGate en el inquilino de Azure, selecciónelo y haga clic en **Aceptar**.

Una vez cargado el certificado, tome note del nombre en **Sistema** > **Certificados** > **Certificado remoto**. De manera predeterminada, se llamará REMOTE_Cert_ *N*, donde *N* es un valor entero.

#### <a name="complete-fortigate-command-line-configuration"></a>Configuración de la línea de comandos de FortiGate

Los pasos siguientes requieren que configure la dirección URL de cierre de sesión de Azure. Esta dirección URL contiene un signo de interrogación (?). Debe seguir pasos específicos para enviar correctamente este signo. Estos pasos no se pueden completar desde la consola de la CLI de FortiGate. En su lugar, establezca una sesión de SSH en el dispositivo FortiGate mediante una herramienta como PuTTY. Si el dispositivo FortiGate es una máquina virtual de Azure, puede seguir estos pasos desde la consola serie en relación con las máquinas virtuales de Azure.

Para completar estos pasos, necesitará los valores que anotó anteriormente:

- El identificador de entidad
- URL de respuesta
- URL de cierre de sesión
- Dirección URL de inicio de sesión de Azure
- Identificador de Azure AD
- Dirección URL de cierre de sesión de Azure
- Nombre del certificado SAML de Base64 (REMOTE_Cert_ *N*)

1. Establezca una sesión de SSH en el dispositivo FortiGate e inicie sesión con una cuenta de administrador de FortiGate.
1. Ejecute estos comandos:

   ```console
    config user saml
    edit azure
    set cert <FortiGate VPN Server Certificate Name>
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > La dirección URL de cierre de sesión de Azure contiene un carácter `?`. Debe especificar una secuencia especial de teclas para pasar correctamente la dirección URL a la consola serie de FortiGate. La dirección URL suele ser `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
   >
   > Para especificar la dirección URL de cierre de sesión de Azure en la consola serie, escriba `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`.
   > 
   > A continuación, presione CTRL+V y pegue el resto de la dirección URL para completar la línea: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

#### <a name="configure-fortigate-for-group-matching"></a>Configuración de FortiGate para la coincidencia de grupos

En esta sección, configurará FortiGate para reconocer el identificador de objeto del grupo de seguridad que incluye al usuario de prueba. Esto permitirá a FortiGate aplicar decisiones de acceso basadas en la pertenencia al grupo.

Para completar estos pasos, necesitará el identificador de objeto del grupo de seguridad FortiGateAccess que creó anteriormente en este tutorial.

1. Establezca una sesión de SSH en el dispositivo FortiGate e inicie sesión con una cuenta de administrador de FortiGate.
1. Ejecute estos comandos:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>Creación de una directiva de firewall y de portales de VPN de FortiGate

En esta sección, configurará una directiva de firewall y de portales de VPN de FortiGate para conceder acceso al grupo de seguridad FortiGateAccess que creó anteriormente en este tutorial.

Trabaje con el [equipo de soporte técnico de FortiGate](mailto:tac_amer@fortinet.com) para agregar los portales de VPN y la directiva de firewall a la plataforma de VPN de FortiGate. Debe completar este paso antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de la VPN de FortiGate, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de la VPN de FortiGate e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de VPN de FortiGate de Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de la VPN de FortiGate. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Pasos siguientes

Una vez que configure la VPN de FortiGate, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
