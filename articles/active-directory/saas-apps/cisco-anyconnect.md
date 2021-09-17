---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Cisco AnyConnect | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Cisco AnyConnect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2021
ms.author: jeedes
ms.openlocfilehash: 5e655177f37ecafea407899c4512d803ba2295ad
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252807"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Cisco AnyConnect

En este tutorial, aprenderá a integrar Cisco AnyConnect con Azure Active Directory (Azure AD). Al integrar Cisco AnyConnect con Azure AD, puede hacer lo siguiente:

* Puede controlar en Azure AD quién tiene acceso a Cisco AnyConnect.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Cisco AnyConnect con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Cisco AnyConnect.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Cisco AnyConnect admite el inicio de sesión único iniciado por **IDP**.

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>Incorporación de Cisco AnyConnect desde la galería

Para configurar la integración de Cisco AnyConnect en Azure AD, será preciso que añada Cisco AnyConnect desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Cisco AnyConnect** en el cuadro de búsqueda.
1. Seleccione **Cisco AnyConnect** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>Configuración y prueba del SSO de Azure AD para Cisco AnyConnect

Configure y pruebe el inicio de sesión único de Azure AD con Cisco AnyConnect mediante un usuario de prueba llamado **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Cisco AnyConnect.

Para configurar y probar el inicio de sesión único de Azure AD con Cisco AnyConnect, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Cisco AnyConnect](#configure-cisco-anyconnect-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación del usuario de prueba de Cisco AnyConnect](#create-cisco-anyconnect-test-user)** , para tener un homólogo de B.Simon en Cisco AnyConnect vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Cisco AnyConnect**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configurar el inicio de sesión único con SAML**, escriba los valores de los siguientes campos (distinguen mayúsculas de minúsculas):

   1. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente:  
      `https://<SUBDOMAIN>.YourCiscoServer.com/saml/sp/metadata/<Tunnel_Group_Name>`

   1. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón:  
      `https://<YOUR_CISCO_ANYCONNECT_FQDN>/+CSCOE+/saml/sp/acs?tgname=<Tunnel_Group_Name>`

    > [!NOTE]
    > Para aclarar estos valores, póngase en contacto con el soporte técnico de Cisco TAC. Actualice estos valores con los valores reales de identificador y URL de respuesta proporcionados por Cisco TAC. Póngase en contacto con el [equipo de soporte técnico de clientes de Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargar el archivo del certificado y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Cisco AnyConnect**, copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

> [!NOTE]
> Si desea incorporar varios vales TGT del servidor, debe agregar varias instancias de la aplicación Cisco AnyConnect desde la galería. También puede optar por cargar su propio certificado en Azure AD para todas estas instancias de aplicación. De este modo puede tener el mismo certificado para las aplicaciones y a la vez configurar un identificador y una dirección URL de respuesta diferentes para cada aplicación.

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

En esta sección, va a permitir que B. Simon acceda a Cisco AnyConnect mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Cisco AnyConnect**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-cisco-anyconnect-sso"></a>Configuración del inicio de sesión único de Cisco AnyConnect

1. En primer lugar, lo hará en la CLI. Después, podrá volver a realizar un tutorial de ASDM en otro momento.

1. Conéctese a su dispositivo VPN, va a usar una ASA que ejecuta el entrenamiento de código de la versión 9.8 y los clientes VPN tendrán la versión 4.6 o posterior.

1. En primer lugar, creará un Trustpoint e importará el certificado SAML.

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. Los siguientes comandos aprovisionarán el IdP de SAML.

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. Ahora puede aplicar la autenticación SAML a una configuración de túnel VPN.

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > Hay un trabajo con la configuración del IdP de SAML. Si realiza cambios en la configuración de IdP, deberá quitar la configuración del proveedor de identidades de SAML del grupo de túnel y volver a aplicarla para que los cambios surtan efecto.

### <a name="create-cisco-anyconnect-test-user"></a>Creación de un usuario de prueba de Cisco AnyConnect

En esta sección, creará un usuario llamado Britta Simon en Cisco AnyConnect. Trabaje con el [equipo de soporte técnico de Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) para añadir los usuarios a la plataforma de Cisco AnyConnect. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Cisco AnyConnect para la que configuró el inicio de sesión único.
* Puede usar el Panel de acceso de Microsoft. Al hacer clic en el icono de Cisco AnyConnect en el Panel de acceso, debería iniciar sesión automáticamente en la versión de Cisco AnyConnect para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Cisco AnyConnect, puede aplicar el control de sesión, que protege a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
