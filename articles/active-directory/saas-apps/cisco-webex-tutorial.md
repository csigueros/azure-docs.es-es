---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Cisco Webex Meetings | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Cisco Webex Meetings.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ef6a785fc1d825b0b490ec7af7d3c8128aac588b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181666"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Cisco Webex Meetings

En este tutorial, aprenderá a integrar Cisco Webex Meetings con Azure Active Directory (Azure AD). Al integrar Cisco Webex Meetings con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Cisco Webex Meetings.
* Permitir que los usuarios inicien sesión automáticamente en Cisco Webex Meetings con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).


## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Cisco Webex Meetings.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Cisco Webex Meetings admite el inicio de sesión único iniciado por **SP e IDP**.
* Cisco Webex Meetings admite el [aprovisionamiento y desaprovisionamiento **automático** de usuarios](cisco-webex-provisioning-tutorial.md) (recomendado).
* Cisco Webex Meetings admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Adición de Cisco Webex Meetings desde la galería

Para configurar la integración de Cisco Webex Meetings en Azure AD, será preciso que agregue Cisco Webex Meetings desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Cisco Webex Meetings** en el cuadro de búsqueda.
1. Seleccione **Cisco Webex Meetings** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Configuración y prueba del inicio de sesión único de Azure AD para Cisco Webex Meetings

Configure y pruebe el inicio de sesión único de Azure AD con Cisco Webex Meetings utilizando un usuario de prueba llamado **B. Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Cisco Webex Meetings.

Para configurar y probar el inicio de sesión único de Azure AD con Cisco Webex Meetings, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
   1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
   1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
   
1. **[Configuración del inicio de sesión único de Cisco Webex Meetings](#configure-cisco-webex-meetings-sso)**, para configurar los valores de inicio de sesión único en la aplicación.
   * **[Creación del usuario de prueba de Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)**, para tener un homólogo de B. Simon en Cisco Webex Meetings vinculado a la representación del usuario en Azure AD.
    
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Cisco Webex Meetings**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, puede configurar la aplicación en modo iniciado por **IDP** mediante la carga del archivo de **metadatos del proveedor de servicios** de la siguiente manera:
   1. Haga clic en **Cargar el archivo de metadatos**.
   1. Haga clic en el **logotipo de la carpeta** para seleccionar el archivo de metadatos y luego en **Cargar**.
   1. Cuando el archivo de metadatos del proveedor de servicios se haya cargado correctamente, los valores **Identificador** y **URL de respuesta** se rellenarán automáticamente en la sección **Configuración básica de SAML**.
   
      > [!Note]
      > Obtendrá el archivo de metadatos del proveedor de servicios en la sección **Configuración del inicio de sesión único de Cisco Webex Meetings**, que se explica más adelante en este tutorial. 

1. Si desea configurar la aplicación en modo iniciado por **SP**, realice los siguientes pasos:  
   1. En la sección **Configuración básica de SAML**, haga clic en el icono de edición o con forma de lápiz.

      ![Edición de la configuración básica de SAML](common/edit-urls.png)

   1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL con el siguiente patrón: `https://<customername>.my.webex.com`

1. La aplicación Cisco Webex Meetings espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados. Haga clic en el icono **Editar** para abrir el cuadro de diálogo Atributos de usuario.

   ![imagen](common/edit-attribute.png)

1. Además de lo anterior, la aplicación Cisco Webex Meetings espera que se usen algunos atributos más en la respuesta de SAML. En la sección Notificaciones del usuario del cuadro de diálogo Atributos de usuario, haga lo siguiente para agregar el atributo Token SAML como se muestra en la tabla siguiente: 

   | Nombre | Atributo de origen|
   | ---------------|  --------- |
   |   firstname    | user.givenname |
   |   lastname    | user.surname |
   |   email       | user.mail |
   |   uid    | user.mail |

   1. Haga clic en **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.
   1. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
   1. Deje **Espacio de nombres** en blanco.
   1. Seleccione **Atributo** como origen.
   1. En la lista **Atributo de origen**, seleccione el valor de atributo que se muestra para esa fila en la lista desplegable.
   1. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

   ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Cisco Webex Meetings** (Configurar Cisco Webex Meetings), copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B. Simon acceda a Cisco Webex Meetings mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Cisco Webex Meetings**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-cisco-webex-meetings-sso"></a>Configuración del inicio de sesión único de Cisco Webex Meetings

1. Inicie sesión en Cisco Webex Meetings con sus credenciales de administrador.
1. Vaya a **Common Site Setting** (Configuración del sitio común) y a **SSO Configuration** (Configuración de inicio de sesión único).

   ![Captura de pantalla que muestra la página de administración de Cisco WebEx con la configuración de sitio común y la configuración de S S O seleccionada.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. En la página **Webex Administration** (Administración de Webwex), siga estos pasos:

   ![Captura de pantalla que muestra la página de administración de Webex con la información que se describe en este paso.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. Seleccione **SAML 2.0** como **Federation Protocol** (Protocolo de Federación).
   1. Haga clic en el enlace **Import SAML Metadata** (Importar metadatos de SAML) para cargar el archivo de metadatos que descargó de Azure Portal.
   1. Seleccione **SSO Profile** (Perfil de SSO) como **IDP initiated** (Iniciado por IdP) y haga clic en el botón **Export** (Exportar) para descargar el archivo de metadatos del proveedor de servicios y cárguelo en la sección **Configuración básica de SAML** de Azure Portal.
   1. En el cuadro de texto **AuthContextClassRef**, escriba uno de los siguientes valores:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Para habilitar MFA mediante Azure AD, escriba los dos valores de la siguiente manera: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. Seleccione **Auto Account Creation** (Creación de cuenta automática).
   
      > [!NOTE]
      > Para habilitar el aprovisionamiento de usuarios **Just-In-Time**, debe seleccionar **Auto Account Creation** (Creación de cuenta automática). Además, es necesario pasar atributos de token de SAML en la respuesta SAML.

   1. Haga clic en **Save**(Guardar).

      > [!NOTE]
      > Esta configuración es solo para los clientes que usan el UserID de Webex en formato de correo electrónico.
      > 
      > Para más información sobre cómo configurar Cisco Webex meetings, consulte la página [Documentación de WebEx](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog).

### <a name="create-cisco-webex-meetings-test-user"></a>Creación del usuario de prueba de Cisco Webex Meetings

El objetivo de esta sección es crear un usuario llamado B. Simon en Cisco Webex Meetings. Cisco Webex Meetings admite el aprovisionamiento **Just-In-Time**, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Cisco Webex Meetings, se crea uno al intentar acceder.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Cisco Webex Meetings, donde podrá comenzar el flujo de inicio de sesión.  

* Acceda directamente a la dirección URL de inicio de sesión de Cisco Webex Meetings y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Cisco Webex Meetings para la que configuró el inicio de sesión único.

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Cisco Webex Meetings en Aplicaciones, si está configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión y, si está configurado en modo IDP, debería iniciar sesión automáticamente en la instancia de Cisco Webex Meetings para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Cisco Webex Meetings, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).