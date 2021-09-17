---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con IntelligenceBank | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory e IntelligenceBank.
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
ms.openlocfilehash: 11acc1805e163a4c1cb9f837f0320133042648b8
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272295"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-intelligencebank"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con IntelligenceBank

En este tutorial, aprenderá a integrar IntelligenceBank con Azure Active Directory (Azure AD). Al integrar IntelligenceBank con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a IntelligenceBank.
* Permitir que los usuarios inicien sesión automáticamente en IntelligenceBank con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en IntelligenceBank.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* IntelligenceBank admite el inicio de sesión único iniciado por **SP**.

## <a name="add-intelligencebank-from-the-gallery"></a>Incorporación de IntelligenceBank desde la galería

Para configurar la integración de IntelligenceBank en Azure AD, es preciso agregar IntelligenceBank desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **IntelligenceBank** en el cuadro de búsqueda.
1. Seleccione **IntelligenceBank** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-intelligencebank"></a>Configuración y prueba del inicio de sesión único de Azure AD para IntelligenceBank

Configure y pruebe el inicio de sesión único de Azure AD con IntelligenceBank mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de IntelligenceBank.

Para configurar y probar el inicio de sesión único de Azure AD con IntelligenceBank, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en IntelligenceBank](#configure-intelligencebank-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de IntelligenceBank](#create-intelligencebank-test-user)** : para tener un homólogo de B.Simon en IntelligenceBank que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **IntelligenceBank**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con uno de los patrones siguientes:

    | **Identificador** |
    |-----|
    | `IB` |
    | `IntelligenceBank` |
    | `https://<SUBDOMAIN>.intelligencebank.com` |

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.intelligencebank.com/auth`

    c. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.intelligencebank.com`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico al cliente de IntelligenceBank](mailto:helpdesk@intelligencebank.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar IntelligenceBank**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a IntelligenceBank mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **IntelligenceBank**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-intelligencebank-sso"></a>Configuración del inicio de sesión único de IntelligenceBank

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de IntelligenceBank como administrador.

1. Haga clic en **Authenticator** (Autenticador) y haga clic en **Add New** (Agregar nuevo).

    ![Captura de pantalla que muestra la pestaña Administrator (Administrador) seleccionada y el icono Add New (Agregar nuevo).](./media/intelligencebank-tutorial/authenticator.PNG)

1. Lleve a cabo los siguiente pasos:

    ![Captura de pantalla que muestra los campos donde puede especificar la información que se describe en este paso.](./media/intelligencebank-tutorial/fields.PNG)

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre; por ejemplo, `azureadsso`.

    b. En el cuadro de texto **Description** (Descripción), escriba una descripción válida.

    c. Seleccione **SAML** en la lista desplegable como valor de **Type** (Tipo).

    d. En el cuadro de texto **Remote Url** (Dirección URL remota), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado desde Azure Portal.

    e. En el cuadro de texto **Host**, pegue el valor de **Id. de entidad** que ha copiado desde Azure Portal.

    f. Abra en el Bloc de notas el archivo del **certificado (Base 64)** que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **CertData** (Datos del certificado).

    g. En el cuadro de texto **SingleLogoutService** (Servicio de cierre de sesión único), pegue el valor de **URL de cierre de sesión** que ha copiado de Azure Portal.

    h. Haga clic en el botón **Save** (Guardar).

### <a name="create-intelligencebank-test-user"></a>Creación de un usuario de prueba de IntelligenceBank

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de IntelligenceBank como administrador.

1. Vaya a **Admin** -> **Users** (Administración>Usuarios) y seleccione el icono **Add New User** (Agregar usuario nuevo) para agregar el **usuario**.

    ![Captura de pantalla que muestra el icono Users (Usuarios) seleccionado en la pestaña Users (Usuarios).](./media/intelligencebank-tutorial/creating-user.PNG)

1. Rellene los campos necesarios según los requisitos de su organización y haga clic en **Save** (Guardar).

    ![Captura de pantalla que muestra la página Add New User (Agregar un nuevo usuario) en la que se especifica la información de usuario.](./media/intelligencebank-tutorial/user.PNG)

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de IntelligenceBank, donde puede iniciar el flujo de inicio de sesión. 

* Vaya a directamente a la dirección URL de inicio de sesión de IntelligenceBank y comience el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de IntelligenceBank en Aplicaciones, se le redirigirá a la URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado IntelligenceBank, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).