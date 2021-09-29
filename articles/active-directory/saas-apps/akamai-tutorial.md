---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Akamai | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 38b0ea6b65c3fcb01e8a49cd3a416533bcab5688
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803368"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Akamai

En este tutorial, obtendrá información sobre cómo integrar Akamai con Azure Active Directory (Azure AD). Al integrar Akamai con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Akamai.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Akamai con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

La integración de Azure Active Directory y Akamai Enterprise Application Access permite un acceso sin problemas a las aplicaciones heredadas hospedadas en la nube o en el entorno local. La solución integrada aprovecha las ventajas de todas las funcionalidades modernas de Azure Active Directory como el [acceso condicional de Azure AD](../conditional-access/overview.md), [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) y [Azure AD Identity Governance](../governance/identity-governance-overview.md) para el acceso a las aplicaciones heredadas sin necesidad de instalar agentes ni modificaciones en la aplicación.

En la imagen siguiente se describe en qué lugar encaja Akamai EAA en el escenario de Acceso híbrido seguro, que es más amplio.

![Akamai EAA encaja en el escenario de Acceso híbrido seguro más amplio](./media/header-akamai-tutorial/introduction-1.png)

### <a name="key-authentication-scenarios"></a>Escenarios de autenticación de claves

Además de la compatibilidad con la integración nativa de Azure Active Directory con protocolos de autenticación modernos, como Open ID Connect, SAML y WS-Fed, Akamai EAA amplía el acceso seguro para las aplicaciones de autenticación heredadas tanto en el acceso interno como en el externo con Azure AD, lo que habilita escenarios modernos (por ejemplo, el acceso sin contraseña) en estas aplicaciones. Esto incluye:

* Aplicaciones de autenticación basadas en encabezados
* Escritorio remoto
* SSH (Secure Shell)
* Aplicaciones de autenticación Kerberos
* VNC (Virtual Network Computing)
* Aplicaciones sin autenticación integrada o autenticación anónima
* Aplicaciones de autenticación NTLM (protección con dos preguntas para el usuario)
* Aplicaciones basadas en formularios (protección con dos preguntas para el usuario)

### <a name="integration-scenarios"></a>Escenarios de integración

La asociación entre Microsoft y Akamai EAA permite la flexibilidad necesaria para satisfacer los requisitos empresariales, ya que admite varios escenarios de integración en función de las necesidades de las empresas. Se pueden usar para proporcionar cobertura desde el primer día en todas las aplicaciones y clasificar y configurar gradualmente las clasificaciones de directivas adecuadas.

#### <a name="integration-scenario-1"></a>Escenario de integración 1

Akamai EAA se configura como una sola aplicación en Azure AD. El administrador puede configurar la directiva de acceso condicional en la aplicación y, una vez que se cumplan las condiciones, los usuarios puedan obtener acceso al portal de Akamai EAA.

**Ventajas**:

* IDP solo tiene que configurarse una vez.

**Inconvenientes**:

* los usuarios acaban teniendo dos portales de aplicaciones.

* Cobertura de la directiva de acceso condicional común única para todas las aplicaciones.

![Escenario de integración 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>Escenario de integración 2

La aplicación Akamai EAA se configura de forma individual en el portal de Azure AD. El administrador puede configurar una directiva de acceso condicional individual en las aplicaciones y, una vez que se cumplan las condiciones, se puede redirigir a los usuarios directamente a la aplicación específica.

**Ventajas**:

* puede definir directivas de CA individuales.

* Todas las aplicaciones se representan en el panel de myApps.microsoft.com y 0365 Waffle.


**Inconvenientes**:

* Debe configurar varios IDP.

![Escenario de integración 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Akamai.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

- Akamai admite SSO iniciado por IDP.

#### <a name="important"></a>Importante

Toda la configuración que se muestra a continuación es la misma para el **escenario de integración 1** y el **escenario 2**. En el **escenario de integración 2**, ha configurado un IDP individual en Akamai EAA. La propiedad URL debe modificarse para que apunte a la dirección URL de la aplicación.

![Captura de pantalla de la pestaña General de AZURESSO-SP en Akamai Enterprise Application Access. Se resalta el campo dirección URL de configuración de autenticación.](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>Adición de Akamai desde la galería

Para configurar la integración de Akamai en Azure AD, será preciso agregar Akamai desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Akamai** en el cuadro de búsqueda.
1. Seleccione **Akamai** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Configuración y prueba del inicio de sesión único de Azure AD para Akamai

Configure y pruebe el inicio de sesión único de Azure AD con Akamai mediante una usuaria de prueba llamada **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Akamai.

Para configurar y probar el inicio de sesión único de Azure AD con Akamai, realice los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Akamai](#configure-akamai-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    * **[Configuración de IDP](#setting-up-idp)**
    * **[Autenticación basada en encabezados](#header-based-authentication)**
    * **[Escritorio remoto](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Autenticación Kerberos](#kerberos-authentication)**
    * **[Creación de un usuario de prueba de Akamai](#create-akamai-test-user)** , para tener un homólogo de B.Simon en Akamai que esté vinculado a la representación de ella en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Akamai**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de cliente de Akamai](https://www.akamai.com/us/en/contact-us/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Akamai** (Configurar Akamai), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Akamai mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Akamai**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-akamai-sso"></a>Configuración del inicio de sesión único de Akamai

### <a name="setting-up-idp"></a>Configuración de IDP

**Configuración de IDP de AKAMAI EAA**

1. Inicie sesión en la consola **Enterprise Application Access de Akamai**.
1. En la **consola EAA de Akamai**, seleccione **Identity** (Identidad)  > **Identity Providers** (Proveedores de identidades) y haga clic en **Add Identity Provider** (Agregar proveedor de identidades).

    ![Captura de pantalla de la ventana Proveedores de identidades de la consola de Akamai EAA. Selección de proveedores de identidades y selección de Agregar proveedor de identidades.](./media/header-akamai-tutorial/configure-1.png)

1. En **Create New Identity Provider** (Crear proveedor de identidades), realice los pasos siguientes:

    ![Captura de pantalla del cuadro de diálogo Crear nuevo proveedor de identidades en la consola de Akamai EAA.](./media/header-akamai-tutorial/configure-2.png)

    a. Especifique el valor de **Unique Name** (Nombre único).

    b. Elija **Third Party SAML** (SAML de terceros) y haga clic en **Create Identity Provider and Configure** (Crear proveedor de identidades y configurar).

### <a name="general-settings"></a>Configuración general

1. **Identity Intercept** (Interceptación de identidad): especifique el nombre de la URL base del SP. Se usará para la configuración de Azure AD.

    > [!NOTE]
    > Puede elegir tener su propio dominio personalizado (requerirá una entrada DNS y un certificado). En este ejemplo, vamos a usar el dominio de Akamai.

1. **Akamai Cloud Zone** (Zona de nube de Akamai): seleccione la zona de nube adecuada.
1. **Certificate Validation** (Validación de certificado): consulte la documentación de Akamai (opcional).

    ![Captura de pantalla de la pestaña General de la consola de Akamai EAA que muestra la configuración de la interceptación de identidad, la zona en la nube de Akamai y la validación de certificado.](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>Configuración de autenticación

1. URL: especifique la misma dirección URL que la que indicó en la interceptación de identidad (aquí es donde se redirigirá a los usuarios después de la autenticación).
2. Logout URL (Dirección URL de cierre de sesión): actualice la dirección URL de cierre de sesión.
3. Sign SAML Request (Firmar solicitud SAML): el valor está desactivado de manera predeterminada.
4. Para el archivo de metadatos de IDP, agregue la aplicación en la consola de Azure AD.

    ![Captura de pantalla de la configuración de autenticación de la consola de Akamai EAA que muestra la configuración de dirección URL, dirección URL de cierre de sesión, firmar solicitud de SAML y archivo de metadatos de IDP.](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>Configuración de la sesión

Deje la configuración predeterminada.

![Captura de pantalla del cuadro de diálogo Configuración de la sesión de la consola de Akamai EAA.](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>Directorios

Omita la configuración del directorio.

![Captura de pantalla de la pestaña Directorios de la consola de Akamai EAA.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>IU de personalización

Puede agregar personalización a IDP.

![Captura de pantalla de la pestaña de personalización de la consola de Akamai EAA que muestra la configuración para personalizar la interfaz de usuario, la configuración de idioma y los temas.](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>Configuración avanzada

Omita la configuración avanzada o consulte la documentación de Akamai para obtener más detalles.

![Captura de pantalla de la pestaña Configuración avanzada de la consola de Akamai EAA que muestra la configuración del cliente de EAA, las opciones avanzadas y el puente de OIDC a SAML.](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>Implementación

1. Haga clic en Deploy Identity Provider (Implementar proveedor de identidades).

    ![Captura de pantalla de la pestaña de implementación de la consola de Akamai EAA que muestra el botón Implementar proveedor de identidades.](./media/header-akamai-tutorial/deployment.png)

2. Compruebe que la implementación se ha realizado correctamente.

### <a name="header-based-authentication"></a>Autenticación basada en encabezados

Autenticación basada en encabezados de Akamai

1. Elija **Custom HTTP** (HTTP personalizado) en el Asistente para agregar aplicaciones.

    ![Captura de pantalla del Asistente para agregar aplicaciones de la consola de Akamai EAA que muestra la opción HTTP personalizado en la sección aplicaciones de acceso.](./media/header-akamai-tutorial/configure-5.png)

2. Escriba **Application Name** (Nombre de aplicación) y **Description** (Descripción).

    ![Captura de pantalla del cuadro de diálogo de HTTP personalizado de la aplicación que muestra la configuración del nombre y la descripción de la aplicación.](./media/header-akamai-tutorial/configure-6.png)

    ![Captura de pantalla de la pestaña General de la consola de Akamai EAA que muestra la configuración general de MYHEADERAPP.](./media/header-akamai-tutorial/configure-7.png)

    ![Captura de pantalla de la consola de Akamai EAA que muestra la configuración del certificado y la ubicación.](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>Authentication

1. Seleccione **Authentication** (Autenticación).

    ![Captura de pantalla de la consola de Akamai EAA con la pestaña Autenticación seleccionada.](./media/header-akamai-tutorial/configure-9.png)

2. Asigne el **proveedor de identidades**.

    ![Captura de pantalla de la pestaña de autenticación de la consola de Akamai EAA para MYHEADERAPP que muestra el proveedor de identidades establecido en Azure AD SSO.](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>Servicios

Haga clic en Save and Go to Authentication (Guardar e ir a Autenticación).

![Captura de pantalla de la pestaña Servicios de la consola de Akamai EAA para MYHEADERAPP que muestra el botón Guardar e ir a configuración avanzada en la esquina inferior derecha.](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>Configuración avanzada

1. En los **Customer HTTP Headers** (Encabezados de HTTP del cliente), especifique **CustomerHeader** y **SAML Attribute** (Atributo de SAML).

    ![Captura de pantalla de la pestaña Configuración avanzada de la consola de Akamai EAA que muestra el campo dirección URL de inicio de sesión único resaltado en Autenticación.](./media/header-akamai-tutorial/configure-12.png)

1. Haga clic en el botón **Save and go to Deployment** (Guardar e ir a Implementación).

    ![Captura de pantalla de la pestaña Configuración avanzada de la consola de Akamai EAA que muestra el botón Guardar e ir a la implementación en la esquina inferior derecha.](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>Implementación de la aplicación

1. Haga clic en el botón **Deploy Application** (Implementar aplicación).

    ![Captura de pantalla de la pestaña de implementación de la consola de Akamai EAA que muestra el botón Implementar aplicación.](./media/header-akamai-tutorial/configure-14.png)

1. Compruebe que la aplicación se ha implementado correctamente.

    ![Captura de pantalla de la pestaña de implementación de la consola de Akamai EAA que muestra el mensaje de estado de la aplicación: "Aplicación implementada correctamente".](./media/header-akamai-tutorial/configure-15.png)

1. Experiencia del usuario final.

    ![Captura de pantalla de la pantalla de inicio de myapps.microsoft.com con una imagen de fondo y un cuadro de diálogo de inicio de sesión.](./media/header-akamai-tutorial/end-user-1.png)

    ![Captura de pantalla que muestra parte de una ventana de aplicaciones con iconos para el complemento, HRWEB, Akamai: aplicaciones corporativas, gastos, grupos y revisiones de acceso. ](./media/header-akamai-tutorial/end-user-2.png)

1. Acceso condicional.

    ![Captura de pantalla del mensaje: Aprobar la solicitud de inicio de sesión. Hemos enviado una notificación a su dispositivo móvil. Responda para continuar.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![Captura de pantalla de una pantalla de aplicaciones que muestra un icono para MyHeaderApp.](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>Escritorio remoto

1. Elija **RDP** en el Asistente para agregar aplicaciones.

    ![Captura de pantalla del Asistente para agregar aplicaciones de la consola de Akamai EAA que muestra RDP en la lista de aplicaciones en la sección aplicaciones de acceso.](./media/header-akamai-tutorial/configure-16.png)

1. Escriba **Application Name** (Nombre de aplicación) y **Description** (Descripción).

    ![Captura de pantalla del cuadro de diálogo de la aplicación RDP que muestra la configuración del nombre y la descripción de la aplicación.](./media/header-akamai-tutorial/configure-17.png)

    ![Captura de pantalla de la pestaña General de la consola de Akamai EAA que muestra la configuración de identidad de la aplicación para SECRETRDPAPP.](./media/header-akamai-tutorial/configure-18.png)

1. Especifique el conector que funcionará con esta aplicación.

    ![Captura de pantalla de la consola de Akamai EAA que muestra la configuración del certificado y la ubicación. El conector asociado se establece en USWST-CON1.](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>Authentication

Haga clic en **Save and go to Services** (Guardar e ir a Servicios).

![Captura de pantalla de la pestaña de autenticación de la consola de Akamai EAA para SECRETRDPAPP que muestra el botón Guardar e ir a servicios en la esquina inferior derecha.](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>Servicios

Haga clic en **Save and go to Advanced Settings** (Guardar e ir a Configuración avanzada).

![Captura de pantalla de la pestaña Servicios de la consola de Akamai EAA para SECRETRDPAPP que muestra el botón Guardar e ir a configuración avanzada en la esquina inferior derecha.](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>Configuración avanzada

1. Haga clic en **Save and go to Deployment** (Guardar e ir a Implementación).

    ![Captura de pantalla de la pestaña Configuración avanzada de la consola de Akamai EAA para SECRETRDPAPP que muestra la configuración de escritorio remoto.](./media/header-akamai-tutorial/configure-22.png)

    ![Captura de pantalla de la pestaña Configuración avanzada de la consola de Akamai EAA para SECRETRDPAPP que muestra la configuración de autenticación y comprobación de estado.](./media/header-akamai-tutorial/configure-23.png)

    ![Captura de pantalla de la configuración de los encabezados HTTP personalizados de la consola de Akamai EAA para SECRETRDPAPP con el botón Guardar e ir a implementación en la esquina inferior derecha.](./media/header-akamai-tutorial/configure-24.png)

1. Experiencia del usuario final

    ![Captura de pantalla de una ventana de myapps.microsoft.com con una imagen de fondo y un cuadro de diálogo de inicio de sesión.](./media/header-akamai-tutorial/end-user-3.png)

    ![Captura de pantalla de la ventana de aplicaciones myapps.microsoft.com con iconos para el complemento, HRWEB, Akamai: aplicaciones corporativas, gastos, grupos y revisiones de acceso.](./media/header-akamai-tutorial/end-user-2.png)

1. Acceso condicional

    ![Captura de pantalla del mensaje de acceso condicional: Aprobar la solicitud de inicio de sesión. Hemos enviado una notificación a su dispositivo móvil. Responda para continuar.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Captura de pantalla de una pantalla de aplicaciones que muestra iconos para MyHeaderApp y SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Captura de pantalla de la pantalla de Windows Server 2012 R2 que muestra iconos de usuario genéricos. Los iconos del administrador, user0 y user1 muestran que han iniciado sesión.](./media/header-akamai-tutorial/conditional-access-6.png)

1. También puede escribir directamente la dirección URL de la aplicación RDP.

#### <a name="ssh"></a>SSH

1. Vaya a Add Applications (Agregar aplicaciones) y elija **SSH**.

    ![Captura de pantalla del Asistente para agregar aplicaciones de la consola de Akamai EAA que muestra SSH en la lista de aplicaciones en la sección aplicaciones de acceso.](./media/header-akamai-tutorial/configure-25.png)

1. Escriba **Application Name** (Nombre de aplicación) y **Description** (Descripción).

    ![Captura de pantalla del cuadro de diálogo de la aplicación SSH que muestra la configuración del nombre y la descripción de la aplicación.](./media/header-akamai-tutorial/configure-26.png)

1. Configure Application Identity (Identidad de aplicación).

    ![Captura de pantalla de la pestaña General de la consola de Akamai EAA que muestra la configuración de identidad de la aplicación para SSH-SECURE.](./media/header-akamai-tutorial/configure-27.png)

    a. Especifique un nombre y una descripción.

    b. Especifique la dirección IP y el nombre de dominio completo del servidor de aplicaciones y el puerto para SSH.

    c. Especifique el nombre de usuario y la frase de contraseña de SSH *Consulte la consola de acceso de aplicaciones empresariales de Akamai.

    d. Especifique el nombre de host externo.

    e. Especifique la ubicación del conector y elija el conector.

#### <a name="authentication"></a>Authentication

Haga clic en **Save and go to Services** (Guardar e ir a Servicios).

![Captura de pantalla de la pestaña de autenticación de la consola de Akamai EAA para SSH-SECURE que muestra el botón Guardar e ir a servicios en la esquina inferior derecha.](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>Servicios

Haga clic en **Save and go to Advanced Settings** (Guardar e ir a Configuración avanzada).

![Captura de pantalla de la pestaña Servicios de la consola de Akamai EAA para SSH-SECURE que muestra el botón Guardar e ir a configuración avanzada en la esquina inferior derecha.](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>Configuración avanzada

Haga clic en Save and go to Deployment (Guardar e ir a implementación).

![Captura de pantalla de la pestaña Configuración avanzada de la consola de Akamai EAA para SSH-SECURE que muestra la configuración de autenticación y comprobación de estado.](./media/header-akamai-tutorial/configure-30.png)

![Captura de pantalla de la configuración de los encabezados HTTP personalizados de la consola de Akamai EAA para SSH-SECURE con el botón Guardar e ir a implementación en la esquina inferior derecha.](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>Implementación

1. Haga clic en **Deploy Application** (Implementar aplicación).

    ![Captura de pantalla de la pestaña de implementación de la consola de Akamai EAA para SSH-SECURE que muestra el botón Implementar aplicación.](./media/header-akamai-tutorial/configure-32.png)

1. Experiencia del usuario final

    ![Captura de pantalla del cuadro de diálogo de inicio de sesión de una ventana de myapps.microsoft.com.](./media/header-akamai-tutorial/end-user-3.png)

    ![Captura de pantalla de la ventana de aplicaciones myapps.microsoft.com con iconos para el complemento, HRWEB, Akamai: aplicaciones corporativas, gastos, grupos y revisiones de acceso.](./media/header-akamai-tutorial/end-user-4.png)

1. Acceso condicional

    ![Captura de pantalla que muestra el mensaje: Aprobar la solicitud de inicio de sesión. Hemos enviado una notificación a su dispositivo móvil. Responda para continuar.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Captura de pantalla de una pantalla de aplicaciones que muestra iconos para MyHeaderApp, SSH Secure y SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-7.png)

    ![Captura de pantalla de una ventana de comandos para ssh-secure-go.akamai-access.com que muestra un mensaje de solicitud de contraseña.](./media/header-akamai-tutorial/conditional-access-8.png)

    ![Captura de pantalla de una ventana de comandos para ssh-secure-go.akamai-access.com que muestra información sobre la aplicación y muestra un mensaje para los comandos.](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Autenticación Kerberos

En el ejemplo siguiente, publicaremos un servidor web interno, <code>http://frp-app1.superdemo.live</code>, y habilitaremos el inicio de sesión único mediante KCD.

#### <a name="general-tab"></a>Pestaña General

![Captura de pantalla de la pestaña General para MYKERBOROSAPP de la consola de Akamai EAA.](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>Pestaña Autenticación

Asigne el proveedor de identidades.

![Captura de pantalla de la pestaña de autenticación de la consola de Akamai EAA para MYKERBOROSAPP que muestra el proveedor de identidades establecido en Azure AD SSO.](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>Pestaña Servicios

![Captura de pantalla de la pestaña Servicios para MYKERBOROSAPP de la consola de Akamai EAA.](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>Configuración avanzada

![Captura de pantalla de la pestaña Configuración avanzada de la consola de Akamai EAA para MYKERBOROSAPP que muestra la configuración de aplicaciones relacionadas y la autenticación.](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> El SPN del servidor web tiene el formato SPN@Domain como, por ejemplo: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` para esta demostración. Deje los valores predeterminados en el resto de la configuración.

#### <a name="deployment-tab"></a>Pestaña Implementación

![Captura de pantalla de la pestaña de implementación de la consola de Akamai EAA para MYKERBOROSAPP que muestra el botón Implementar aplicación.](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>Incorporación de directorio

1. Seleccione **AD** en la lista desplegable.

    ![Captura de pantalla de la ventana Directorios de la consola de Akamai EAA que muestra un cuadro de diálogo Crear nuevo directorio con AD seleccionado en el menú desplegable del tipo de directorio.](./media/header-akamai-tutorial/configure-33.png)

1. Proporcione los datos necesarios.

    ![Captura de pantalla de la ventana de SUPERDEMOLIVE de la consola de Akamai EAA con la configuración de nombre de directorio, servicio de directorio, conector y asignación de atributos.](./media/header-akamai-tutorial/configure-34.png)

1. Compruebe la creación del directorio.

    ![Captura de pantalla de la ventana Directorios de la consola de Akamai EAA que muestra que se ha agregado el directorio superdemo.live.](./media/header-akamai-tutorial/directory-domain.png)

1. Agregue los grupos o unidades organizativas que requerirán acceso.

    ![Captura de pantalla de la configuración del directorio superdemo.live. Se resalta el icono seleccionado para agregar grupos o unidades organizativas.](./media/header-akamai-tutorial/add-group.png)

1. En la parte inferior, el grupo se denomina EAAGroup y tiene 1 miembro.

    ![Captura de pantalla de la ventana de grupos del directorio SUPERDEMOLIVE de la consola de Akamai EAA. El grupo EAAGroup con 1 usuario aparece en la lista Grupos.](./media/header-akamai-tutorial/eaagroup.png)

1. Agregue el directorio al proveedor de identidades; para ello, haga clic en **Identity** (Identidad)  > **Identity Providers** (Proveedores de identidades), haga clic en la pestaña **Directories** (Directorios) y haga clic en **Assign directory** (Assign directory).

    ![Captura de pantalla de la pestaña Directorios de la consola de Akamai EAA para Azure AD SSO, donde se muestra superdemo.live en la lista de directorios asignados actualmente.](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Tutorial de configuración de la delegación de KCD para EAA

#### <a name="step-1-create-an-account"></a>Paso 1: Creación de una cuenta 

1. En el ejemplo, usaremos una cuenta llamada **EAADelegation**. Para ello, puede usar el complemento **Usuarios y equipos de Active Directory**.

    ![Captura de pantalla de la pestaña Directorios de la consola de Akamai EAA para Azure AD SSO. El directorio superdemo.live aparece en la lista de directorios asignados actualmente.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > El nombre de usuario tiene que estar en un formato específico basado en el **nombre de interceptación de identidad**. En la figura 1, vemos que es **corpapps.login.go.akamai-access.com**

1. El nombre de inicio de sesión de usuario será `HTTP/corpapps.login.go.akamai-access.com`

    ![Captura de pantalla que muestra las propiedades de EAADelegation con el nombre establecido en "EAADelegation" y el nombre de inicio de sesión del usuario establecido en HTTP/corpapps.login.go.akamai-access.com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Paso 2: Configuración del SPN para esta cuenta

1. Según este ejemplo, el SPN será como el que se indica a continuación.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Captura de pantalla de un símbolo del sistema de administrador que muestra los resultados del comando setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Paso 3: Configuración de la delegación

1. En la cuenta EAADelegation, haga clic en la pestaña Delegation (Delegación).

    ![Captura de pantalla de un símbolo del sistema del administrador que muestra el comando para configurar el SPN.](./media/header-akamai-tutorial/spn.png)

    * Especifique el uso de cualquier protocolo de autenticación.
    * Haga clic en Agregar y agregue la cuenta del grupo de aplicaciones para el sitio web de Kerberos. Se debe resolver automáticamente para corregir el SPN, si se ha configurado correctamente.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Paso 4: Creación de un archivo keytab para AKAMAI EAA

1. Esta es la sintaxis genérica.

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. Explicación del ejemplo

    | Fragmento de código | Explicación |
    | - | - |
    | Ktpass /out EAADemo.keytab | // Nombre del archivo keytab de salida |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // Cuenta de delegación de EAA |
    | /pass RANDOMPASS | // Contraseña de la cuenta de delegación de EAA |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // Consulte la documentación de Akamai EAA |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Captura de pantalla de un símbolo del sistema del administrador que muestra los resultados del comando para crear un archivo Keytab para AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Paso 5: Importación de keytab en la consola de AKAMAI EAA

1. Haga clic en **System** (Sistema)  > **Keytabs**.

    ![Captura de pantalla de la consola de Akamai EAA que muestra la selección de Keytabs en el menú del sistema.](./media/header-akamai-tutorial/keytabs.png)

1. En el tipo de keytab, elija **Delegación Kerberos**.

    ![Captura de la pantalla EAAKEYTAB de la consola de Akamai EAA que muestra la configuración de Keytab. En el tipo de keytab está establecido en Delegación Kerberos.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Asegúrese de que el archivo keytab se muestre como Deployed (implementado) y Verified (Comprobado).

    ![Captura de la pantalla KEYTABS de la consola de Akamai EAA que enumera el archivo Keytab de EAA como "Keytab implementado y comprobado".](./media/header-akamai-tutorial/keytabs-2.png)

1. Experiencia del usuario

    ![Captura de pantalla del cuadro de diálogo de inicio de sesión en myapps.microsoft.com. ](./media/header-akamai-tutorial/end-user-3.png)

    ![Captura de pantalla de la ventana de aplicaciones de myapps.microsoft.com que muestra los iconos de la aplicación.](./media/header-akamai-tutorial/end-user-4.png)

1. Acceso condicional

    ![Captura de pantalla que muestra un mensaje de solicitud de aprobación de inicio de sesión. El mensaje.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Captura de la pantalla Aplicaciones que muestra iconos para MyHeaderApp, SSH Secure, SecretRDPApp y myKerberosApp.](./media/header-akamai-tutorial/conditional-access-10.png)

    ![Captura de pantalla de la pantalla de presentación de myKerberosApp. Se muestra el mensaje "Bienvenido superdemo\user1" sobre una imagen de fondo.](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Creación de un usuario de prueba de Akamai

En esta sección, creará un usuario llamado B.Simon en Akamai. Colabore con el [equipo de soporte técnico al cliente de Akamai](https://www.akamai.com/us/en/contact-us/) para agregar los usuarios a la plataforma de Akamai. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal y debería iniciar sesión automáticamente en la instancia de Akamai para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Akamai en Aplicaciones, iniciará sesión automáticamente en la versión de Akamai para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Akamai, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).