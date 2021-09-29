---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Resource Central – SAML SSO for Meeting Room Booking System | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Resource Central – SAML SSO for Meeting Room Booking System.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: 0db8c8d14dbd3e09ebb798c41ea2d7b526acabc9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753203"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central--saml-sso-for-meeting-room-booking-system"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Resource Central – SAML SSO for Meeting Room Booking System

En este tutorial, aprenderá a integrar Resource Central – SAML SSO for Meeting Room Booking System con Azure Active Directory (Azure AD). Al integrar Resource Central – SAML SSO for Meeting Room Booking System con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Resource Central – SAML SSO for Meeting Room Booking System.
* Permitir que los usuarios inicien sesión automáticamente en Resource Central – SAML SSO for Meeting Room Booking System con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Resource Central – SAML SSO for Meeting Room Booking System.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Resource Central – SAML SSO for Meeting Room Booking System admite el SSO iniciado por **SP**.

* Resource Central – SAML SSO for Meeting Room Booking System admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-resource-central--saml-sso-for-meeting-room-booking-system-from-the-gallery"></a>Adición de Resource Central – SAML SSO for Meeting Room Booking System desde la galería

Para configurar la integración de Resource Central – SAML SSO for Meeting Room Booking System en Azure AD, tendrá que agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Resource Central – SAML SSO for Meeting Room Booking System**.
1. Seleccione **Resource Central – SAML SSO for Meeting Room Booking System** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-resource-central--saml-sso-for-meeting-room-booking-system"></a>Configuración y prueba del inicio de sesión único de Azure AD para Resource Central – SAML SSO for Meeting Room Booking System

Configure y pruebe el inicio de sesión único de Azure AD con Resource Central – SAML SSO for Meeting Room Booking System con un usuario de prueba denominado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Resource Central – SAML SSO for Meeting Room Booking System.

Para configurar y probar el inicio de sesión único de Azure AD con Resource Central – SAML SSO for Meeting Room Booking System, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
    1. **[Creación de un usuario de prueba en Resource Central SAML SSO for Meeting Room Booking System](#create-resource-central-saml-sso-for-meeting-room-booking-system-test-user)** : para tener un homólogo de B.Simon en Resource Central – SAML SSO for Meeting Room Booking System que esté vinculado a la representación del usuario en Azure AD.
1. **[Configuración del inicio de sesión único de Resource Central SAML SSO for Meeting Room Booking System](#configure-resource-central-saml-sso-for-meeting-room-booking-system-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Resource Central – SAML SSO for Meeting Room Booking System**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En **Configuración básica de SAML**, especifique los valores de los siguientes campos:

   1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<DOMAIN_NAME>/ResourceCentral`

   1. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón:`https://<DOMAIN_NAME>/ResourceCentral`

   1. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Estos valores no son literales. Actualícelos con los valores de la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Resource Central – SAML SSO for Meeting Room Booking System](mailto:st@aod.vn) para obtener estos valores.  También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En **Configurar Resource Central – SAML SSO for Meeting Room Booking System**, copie las direcciones URL que necesite.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, se crea un usuario llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba `username@companydomain.extension`. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a Resource Central – SAML SSO for Meeting Room Booking System mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Resource Central – SAML SSO for Meeting Room Booking System**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el panel **Usuarios y grupos**, seleccione **B.Simon** en la lista de **usuarios** y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol **Acceso predeterminado**.
1. En el panel **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="create-resource-central-saml-sso-for-meeting-room-booking-system-test-user"></a>Creación del usuario de prueba de Resource Central – SAML SSO for Meeting Room Booking System

En esta sección, se creará un usuario llamado **B.Simon** en **Resource Central – SAML SSO for Meeting Room Booking System**.

1. En Resource Central – SAML SSO for Meeting Room Booking System, seleccione **Security** > **Persons** > **New** (Seguridad > Personas > Nueva).
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Captura de pantalla que muestra el panel Personas en Resource Central, con el botón Nuevo resaltado.":::

1. En **Datos de la persona**, en **Nombre para mostrar**, escriba el usuario **B.Simon**. En **Dirección SMTP**, escriba el nombre de usuario de Azure AD del usuario. Por ejemplo, `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Captura de pantalla que muestra el panel Datos de la persona en Resource Central.":::

## <a name="configure-resource-central-saml-sso-for-meeting-room-booking-system-sso"></a>Configuración del inicio de sesión único de Resource Central SAML SSO for Meeting Room Booking System

En esta sección, configurará el inicio de sesión único en **Administrador del sistema de Resource Central**.

1. En el administrador del sistema de Resource Central – SAML SSO for Meeting Room Booking System, seleccione **External Authentication** (Autenticación externa).
1.  En **Enable Configuration** (Habilitar configuración), seleccione **Yes** (Sí).

    ![Captura de pantalla en la que se muestra la opción Enable Configuration (Habilitar configuración) seleccionada en el panel External Authentication (Autenticación externa) de Resource Central – SAML SSO for Meeting Room Booking System.](./media/resource-central/enable.png)

1. En **Authentication Protocol** (Protocolo de autenticación), seleccione **SAML2**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Captura de pantalla que muestra SAML2 seleccionado para el protocolo de autenticación en Resource Central":::.

1. En la sección **Configuración de SAML2**, especifique los valores de los campos siguientes:

    1. En **Identificador (id. de entidad)** , **Dirección URL de inicio de sesión**, **URL de cierre de sesión** e **Identificador de Azure AD**, escriba las direcciones URL pertinentes:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Captura de pantalla del panel de configuración de SAML2 en Resource Central.":::

        Copie las direcciones URL del panel **Configurar Resource Central – SAML SSO for Meeting Room Booking System**:

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Captura de pantalla de la configuración de Resource Central en Resource Central":::

   1. En **Dirección URL de retorno**, escriba `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler`.
  
1. En **Certificado**, cargue el certificado y escriba la contraseña.

   ![Captura de pantalla de la sección de certificado de Resource Central – SAML SSO for Meeting Room Booking System.](./media/resource-central/cert.png)
   
1. Seleccione **Guardar**.

1. Vuelva a **Azure Portal**. En **Certificado de firma de SAML**, cargue el certificado y escriba la contraseña.

   ![Captura de pantalla del panel de importación de certificado en Azure Portal.](./media/resource-central/cert2.png).

1. Seleccione **Agregar**.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD. Para probar el inicio de sesión único, tiene tres opciones:

* En Azure Portal, seleccione **Probar esta aplicación**. El vínculo redirige a la dirección URL de inicio de sesión de Resource Central – SAML SSO for Meeting Room Booking System, donde puede iniciar sesión.

* Vaya directamente a la dirección URL de inicio de sesión de Resource Central – SAML SSO for Meeting Room Booking System e inicie sesión.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Captura de pantalla de la página web de prueba de inicio de sesión único de Resource Central":::

* Use el portal Aplicaciones de Microsoft. En el portal Mis aplicaciones, seleccione el icono **Resource Central – SAML SSO for Meeting Room Booking System** para redirigir a la dirección URL de inicio de sesión de Resource Central – SAML SSO for Meeting Room Booking System. Para más información, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Resource Central – SAML SSO for Meeting Room Booking System para el inicio de sesión único con Azure AD, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).