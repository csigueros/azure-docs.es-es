---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Workplace by Facebook | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workplace by Facebook.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: ceb5200f716a00d0c8599ab596b29c727dff486e
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Workplace by Facebook

En este tutorial, obtendrá información sobre cómo integrar Workplace by Facebook con Azure Active Directory (Azure AD). Cuando integra Workplace by Facebook con Azure Active AD, puede:

* Controlar en Azure AD quién tiene acceso a Workplace by Facebook.
* Permitir que los usuarios inicien sesión automáticamente en Workplace by Facebook con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).


## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único (SSO) de Workplace by Facebook.

> [!NOTE]
> Facebook tiene dos productos: Workplace Standard (gratis) and Workplace Premium (de pago). Todo inquilino de Workplace Premium puede configurar la integración de SCIM y SSO sin otras implicaciones de coste ni requerimiento de licencias. SSO y SCIM no están disponibles en las instancias de Workplace Standard.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Workplace by Facebook admite el inicio de sesión único iniciado por **SP**.
* Workplace by Facebook admite el **aprovisionamiento Just-In-Time**.
* Workplace by Facebook admite el **[aprovisionamiento automático de usuarios](workplacebyfacebook-provisioning-tutorial.md)** .
* Ahora se puede configurar la aplicación Workplace by Facebook con Azure AD para habilitar el inicio de sesión único. En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Incorporación de Workplace by Facebook desde la galería

Para configurar la integración de Workplace by Facebook en Azure AD, deberá agregar Workplace by Facebook desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Workplace by Facebook** en el cuadro de búsqueda.
1. Seleccione **Workplace by Facebook** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Configuración y prueba del inicio de sesión único (SSO) de Azure AD para Workplace by Facebook

Configure y pruebe el inicio de sesión único de Azure AD con Workplace by Facebook mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Workplace by Facebook.

Para configurar y probar el inicio de sesión único de Azure AD con Workplace by Facebook, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de Workplace by Facebook](#configure-workplace-by-facebook-sso)**: para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Workplace by Facebook](#create-workplace-by-facebook-test-user)**: para tener un homólogo de B. Simon en Workplace by Facebook que esté vinculado a la representación del usuario en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Workplace by Facebook**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión** (que se encuentra en WorkPlace como Dirección URL del destinatario), escriba una dirección URL con el siguiente patrón: `https://.workplace.com/work/saml.php`

    b. En el cuadro de texto **Identificador (Id. de entidad)** (que se encuentra en WorkPlace como Dirección URL de la audiencia), escriba una dirección URL con el siguiente patrón: `https://www.workplace.com/company/`

    c. En el cuadro de texto **URL de respuesta** (que se encuentra en WorkPlace como Servicio de consumidor de aserciones), escriba una dirección URL con el siguiente patrón: `https://.workplace.com/work/saml.php`

    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Consulte la página de autenticación del panel de la empresa Workplace para obtener los valores correctos de la comunidad Workplace; esta operación se explica más adelante en el tutorial.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up Workplace by Facebook** (Configurar Workplace by Facebook), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, habilitará a B. Simon para usar el inicio de sesión único de Azure al concederle acceso a Workplace by Facebook.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Workplace by Facebook**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-workplace-by-facebook-sso"></a>Configuración del inicio de sesión único de Workplace by Facebook

1. Para automatizar la configuración en Workplace by Facebook, debe instalar la **extensión del explorador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

1. Después de agregar la extensión al explorador, haga clic en **Set up Workplace by Facebook** (Configurar Workplace by Facebook) para ir a la aplicación del mismo nombre. Desde ahí, proporcione las credenciales de administrador para iniciar sesión en la aplicación. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 5.

    ![Configuración](common/setup-sso.png)

1. Si quiere configurar Workplace by Facebook manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Workplace by Facebook como administrador y haga lo siguiente:

    > [!NOTE]
    > Como parte del proceso de autenticación SAML, es posible que Workplace use cadenas de consulta de hasta 2,5 kilobytes de tamaño para pasar parámetros a Azure AD.

1. Vaya a la pestaña **Panel de administración** > **Seguridad** > **Autenticación**.

    ![Panel de administración](./media/workplacebyfacebook-tutorial/security.png)

    a. Active la opción **Single-sign on (SSO)** (Inicio de sesión único [SSO]).

    b. Seleccione **SSO** valor como predeterminado para los nuevos usuarios.
    
    c. Haga clic en **+ Add new SSO Provider** (+ Agregar nuevo proveedor de SSO).
    > [!NOTE]
    > Asegúrese de activar también la casilla Password login (Inicio de sesión con contraseña). Los administradores pueden necesitar esta opción para el inicio de sesión mientras realizan la sustitución del certificado para evitar quedarse bloqueados.

1. En la ventana emergente **Configuración del inicio de sesión único (SSO)** , siga estos pasos:

    ![Pestaña Autenticación](./media/workplacebyfacebook-tutorial/single-sign-on-setup.png)

    a. En **Name of the SSO Provider** (Nombre del proveedor de SSO), escriba el nombre de la instancia de inicio de sesión único, como Azureadsso.

    b. En el cuadro de texto **Dirección URL de SAML**, pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c. En el cuadro de texto **SAML Issuer URL** (Dirección URL del emisor de SAML), pegue el valor de **Identificador de Azure AD** que ha copiado de Azure Portal.

    d. Abra el **Certificado (Base64)** que ha descargado de Azure Portal en el Bloc de notas, copie el contenido en el Portapapeles y péguelo en el cuadro de texto **Certificado SAML**.

    e. Copie el valor de **Audience URL** (URL de público) de su instancia y péguela en el cuadro de texto **Identifier (Entity ID)** [Identificador (Id. de entidad)] de la sección **Configuración de SAML básica** de Azure Portal.

    f. Copie el valor de **Recipient URL** (URL de destinatario) y péguelo en el cuadro de texto **URL de inicio de sesión** de la sección **Configuración básica de SAML** en Azure Portal.

    g. Copie el valor de **ACS (Assertion Consumer Service) URL** (Dirección URL de ACS [servicio de consumidor de aserciones]) de la instancia y péguelo en el cuadro de texto **URL de respuesta** de la sección **Configuración básica de SAML** de Azure Portal.

    h. Desplácese hasta la parte inferior de la sección y haga clic en el botón **Probar SSO**. Como resultado aparece una ventana emergente en la que se muestra la página de inicio de sesión de Azure AD. Escriba las credenciales de la forma habitual para autenticarse.

    **Solución de problemas:** Asegúrese de que la dirección de correo electrónico que se devuelve desde Azure AD es la misma que la cuenta de Workplace con la que ha iniciado sesión.

    i. Una vez que la prueba se ha realizado correctamente, desplácese hasta la parte inferior de la página y haga clic en el botón **Guardar**.

    j. Ahora se presentará a todos los usuarios de Workplace la página de inicio de sesión de Azure AD para la autenticación.

1. **Redirigir el cierre de sesión de SAML (opcional)**  -

    Puede optar por configurar una dirección URL de cierre de sesión de SAML que puede usarse para apuntar a la página de cierre de sesión de Azure AD. Si esta opción está habilitada y configurada, ya no se dirige al usuario a la página de cierre de sesión de Workplace, sino que se le redirige a la dirección URL agregada en la opción Redirigir el cierre de sesión de SAML.

### <a name="configuring-reauthentication-frequency"></a>Configuración de la frecuencia de reautenticación

Puede configurar Workplace para que solicite una comprobación SAML cada día, cada tres días, cada semana, cada dos semanas, cada mes o nunca.

> [!NOTE]
> El valor mínimo para la comprobación SAML en aplicaciones móviles se establece en una semana.

También puede forzar el restablecimiento de SAML para todos los usuarios mediante el botón: Require SAML authentication for all users now (Requerir autenticación de SAML para todos los usuarios ahora).

### <a name="create-workplace-by-facebook-test-user"></a>Creación de un usuario de prueba de Workplace by Facebook

En esta sección se crea un usuario llamado B. Simon en Workplace by Facebook. Workplace by Facebook admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

El usuario no tiene que hacer nada en esta sección. Si no existe un usuario en Workplace by Facebook, se crea uno cuando intenta obtener acceso a Workplace by Facebook.

>[!Note]
>Si necesita crear un usuario de forma manual, póngase en contacto con el [equipo de soporte técnico al cliente de Workplace by Facebook](https://www.workplace.com/help/work/).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Workplace by Facebook, desde donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Workplace by Facebook e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Workplace by Facebook en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Workplace by Facebook. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Prueba del inicio de sesión único para Workplace by Facebook (móvil)

1. Abra la aplicación de Workplace by Facebook Mobile. En la página de inicio de sesión, haga clic en **LOG IN** (iniciar sesión).

    ![Inicio de sesión](./media/workplacebyfacebook-tutorial/test05.png)

2. Escriba el correo electrónico de su empresa y haga clic en **CONTINUE** (CONTINUAR).

    ![El correo electrónico](./media/workplacebyfacebook-tutorial/test02.png)

3. Haga clic en **JUST ONCE** (SOLO UNA VEZ).

    ![Una vez](./media/workplacebyfacebook-tutorial/test04.png)

4. Haga clic en **Permitir**.

    ![Permiso](./media/workplacebyfacebook-tutorial/test03.png)

5. Finalmente, después de iniciar sesión correctamente, aparecerá la página principal de la aplicación.    

    ![Página principal](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Workplace by Facebook, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).