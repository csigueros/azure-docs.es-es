---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Chargebee'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Chargebee.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: jeedes
ms.openlocfilehash: c72da21933f1f03dd30c6ef8a5dc68368923145e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448187"
---
# <a name="tutorial-azure-ad-sso-integration-with-chargebee"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Chargebee

En este tutorial, obtendrá información sobre cómo integrar Chargebee con Azure Active Directory (Azure AD). Al integrar Chargebee con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Chargebee.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Chargebee con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Prerrequisitos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Chargebee.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Chargebee admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="add-chargebee-from-the-gallery"></a>Adición de Chargebee desde la galería

Para configurar la integración de Chargebee en Azure AD, deberá agregar Chargebee desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Chargebee** en el cuadro de búsqueda.
1. Seleccione **Chargebee** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-chargebee"></a>Configuración y prueba del inicio de sesión único de Azure AD para Chargebee

Configure y pruebe el inicio de sesión único de Azure AD con Chargebee mediante un usuario de prueba denominado **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Chargebee.

Para configurar y probar el inicio de sesión único de Azure AD con Chargebee, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)**, para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en Chargebee](#configure-chargebee-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Chargebee](#create-chargebee-test-user)** , para tener un homólogo de B.Simon en Chargebee que esté vinculado a la representación del usuario en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Chargebee**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<domainname>.chargebee.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.chargebee.com/saml/<domainname>/acs`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>` es el nombre del dominio que el usuario crea después de reclamar la cuenta. Si necesita cualquier otra información, póngase en contacto con el [equipo de atención al cliente de Chargebee](mailto:support@chargebee.com). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Configurar Chargebee**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Chargebee mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Chargebee**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-chargebee-sso"></a>Configuración del inicio de sesión único de Chargebee

1. Abra una ventana del explorador web e inicie sesión en el sitio de la compañía Chargebee como administrador.

4. En el lado izquierdo del menú, haga clic en **Configuración** > **Seguridad** > **Administrar**.

    ![Captura de pantalla que muestra el sitio de la compañía Chargebee con las opciones Configuración, Seguridad y Administrar seleccionadas.](./media/chargebee-tutorial/security.png)

5. En el elemento emergente **Inicio de sesión único**, siga estos pasos:

    ![Captura de pantalla que muestra el cuadro de diálogo Inicio de sesión único con SAML seleccionado y la opción para confirmar.](./media/chargebee-tutorial/settings.png)

    a. Seleccione **SAML**.

    b. En el cuadro de texto **Dirección URL de inicio de sesión**, pegue el valor de **Dirección URL de inicio de sesión** que copió en Azure Portal.

    c. Abra el certificado codificado en Base64 con el Bloc de notas, copie su contenido y péguelo en el cuadro de texto **Certificado SAML**.

    d. Haga clic en **Confirmar**.

### <a name="create-chargebee-test-user"></a>Creación de un usuario de prueba de Chargebee

Para permitir que los usuarios de Azure AD inicien sesión en Chargebee, tienen que aprovisionarse en Chargebee. En Chargebee, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. En otra ventana del explorador web, inicie sesión en Chargebee como administrador de seguridad.

2. En el lado izquierdo del menú, haga clic en **Clientes** y después vaya a **Crear un nuevo cliente**.

    ![Captura de pantalla que muestra el sitio de Chargebee con las opciones Clientes y Crear un nuevo cliente seleccionadas.](./media/chargebee-tutorial/menu.png)

3. En la página **Nuevo cliente**, rellene los campos correspondientes que se muestran a continuación y haga clic en **Crear cliente** para crear el usuario.

    ![Captura de pantalla muestra la página Nuevo cliente, donde puede especificar la información del cliente.](./media/chargebee-tutorial/customers.png)

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Chargebee, donde puede iniciar el flujo de inicio de sesión.  

* Acceda directamente a la URL de inicio de sesión de Chargebee y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Chargebee para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Chargebee en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Chargebee para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Chargebee, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).