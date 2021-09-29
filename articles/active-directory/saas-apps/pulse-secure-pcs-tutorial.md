---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Pulse Secure PCS | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Pulse Secure PCS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: 743557b3171a0bee6615a875ff647107e81481d1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749574"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Pulse Secure PCS

En este tutorial aprenderá a integrar Pulse Secure PCS con Azure Active Directory (Azure AD). Al integrar Pulse Secure PCS con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Pulse Secure PCS.
* Permitir que los usuarios inicien sesión automáticamente en Pulse Secure PCS con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Pulse Secure PCS.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Pulse Secure PCS admite el inicio de sesión único iniciado por **SP**.

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Incorporación de Pulse Secure PCS desde la galería

Para configurar la integración de Pulse Secure PCS en Azure AD, es preciso agregar Pulse Secure PCS desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Pulse Secure PCS** en el cuadro de búsqueda.
1. Seleccione **Pulse Secure PCS** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Configuración y prueba del SSO de Azure AD para Pulse Secure PCS

Configure y pruebe el inicio de sesión único de Azure AD con Pulse Secure PCS mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Pulse Secure PCS.

Para configurar y probar el inicio de sesión único de Azure AD con Pulse Secure PCS, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Pulse Secure PCS](#configure-pulse-secure-pcs-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Pulse Secure PCS](#create-pulse-secure-pcs-test-user)** , para tener un homólogo de B.Simon en Pulse Secure PCS que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Pulse Secure PCS**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, la dirección URL de respuesta y el identificador reales. Póngase en contacto con el [equipo de soporte técnico de Pulse Secure PCS](mailto:support@pulsesecure.net) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Pulse Secure PCS**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Pulse Secure PCS mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Pulse Secure PCS**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-pulse-secure-pcs-sso"></a>Configuración del inicio de sesión único en Pulse Secure PC

En esta sección se describen las configuraciones de SAML necesarias para configurar PCS como SP de SAML. No se incluyen las demás configuraciones básicas, como la creación de dominios y roles.

**Las configuraciones de Pulse Connect Secure incluyen:**

* Configuración de Azure AD como proveedor de metadatos de SAML
* Configuración del servidor de autenticación SAML
* Asignación de dominios y roles correspondientes

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Configuración de Azure AD como proveedor de metadatos de SAML

En la página siguiente, realice estos pasos:

![Configuración de Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Inicio de sesión en la consola de administración de Pulse Connect Secure
1. Vaya a **System -> Configuration -> SAML** (Sistema > Configuración > SAML).
1. Haga clic en **New Metadata Provider** (Nuevo proveedor de metadatos).
1. Proporcione un nombre válido en el cuadro de texto **Name** (Nombre).
1. Cargue el archivo XML de metadatos descargado de Azure Portal en **Upload Metadata File** (Cargar el archivo de metadatos).
1. Seleccione **Accept Unsigned Metadata** (Aceptar metadatos sin firmar).
1. En Roles, seleccione **Identity Provider** (Proveedor de identidades).
1. Haga clic en **Save Changes** (Guardar cambios).

#### <a name="steps-to-create-a-saml-auth-server"></a>Pasos para crear un servidor de autenticación SAML:

1. Vaya a **Authentication-> Auth Servers** (Autenticación > Servidores de autenticación).
1. Seleccione **New: SAML Server** (Nuevo: Servidor SAML) y haga clic en **New Server** (Nuevo servidor).

    ![Servidor de autenticación de Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. En la página de configuración, realice los pasos siguientes:

    ![Configuración del servidor de autenticación de Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Indique el nombre del servidor en el cuadro de texto **Server Name** (Nombre del servidor).

    b. Seleccione **SAML Version 2.0** (SAML versión 2.0) y **Metadata** (Metadatos) en **Configuration Mode** (Modo de configuración).

    c. Copie el valor de **Connect Secure Entity Id** (Id. de entidad de Connect Secure) y péguelo en el cuadro **Identificador (Id. de entidad)** del cuadro de diálogo **Configuración básica de SAML** de Azure Portal.

    d. Seleccione el valor de identificador de entidad de Azure AD en la lista desplegable **Identity Provider Entity Id** (Id. de entidad del proveedor de identidades).

    e. Seleccione el valor de dirección URL de inicio de sesión de Azure AD en la lista desplegable **Identity Provider Single Sign On URL** (Dirección URL de inicio de sesión del proveedor de identidades).

    f. Puede activar el valor **Single Logout** (Cierre de sesión único). Si se selecciona esta opción, se solicita una nueva autenticación después del cierre de sesión. Si esta opción no está seleccionada y no ha cerrado el explorador, puede volver a conectarse sin autenticación.

    g. En **Requested Authn Context Class** (Clase de contexto de autenticación solicitada) seleccione **Password** y en **Comparison Method** (Método de comparación) seleccione **exact** (exacto).

    h. En **Metadata Validity** (Validez de los metadatos), establezca la validez de los metadatos expresada en número de días.
    
    i. Haga clic en **Guardar cambios**.

### <a name="create-pulse-secure-pcs-test-user"></a>Creación de un usuario de prueba en Pulse Secure PCS

En esta sección creará un usuario llamado Britta Simon en Pulse Secure PCS. Trabaje con el [equipo de soporte técnico de Pulse Secure PCS](mailto:support@pulsesecure.net) para agregar los usuarios a la plataforma de Pulse Secure PCS. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

1. Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Pulse Secure PCS, donde puede iniciar el flujo de inicio de sesión. 

2. Vaya directamente a la dirección URL de inicio de sesión de Pulse Secure PCS e inicie el flujo de inicio de sesión desde allí.

3. Puede usar el Panel de acceso de Microsoft. Al hacer clic en el icono de Pulse Secure PCS en el Panel de acceso, se le redirigirá a la dirección URL de inicio de sesión de Pulse Secure PCS. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Pulse Secure PCS, puede aplicar el control de sesión, que protege a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).