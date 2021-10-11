---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Hiretual-SSO'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Hiretual-SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2021
ms.author: jeedes
ms.openlocfilehash: c459db2d895042d9edfa4bada8b730c96b95ed55
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403220"
---
# <a name="tutorial-azure-ad-sso-integration-with-hiretual-sso"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Hiretual-SSO

En este tutorial aprenderá a integrar Hiretual-SSO con Azure Active Directory (Azure AD). Al integrar Hiretual-SSO con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Hiretual-SSO.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Hiretual-SSO con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Hiretual-SSO.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Hiretual-SSO admite el inicio de sesión único iniciado por **SP e IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-hiretual-sso-from-the-gallery"></a>Incorporación de Hiretual-SSO desde la galería

Para configurar la integración de Hiretual-SSO en Azure AD, tiene que agregar Hiretual-SSO desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Hiretual-SSO** en el cuadro de búsqueda.
1. Seleccione **Hiretual-SSO** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-hiretual-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD para Hiretual-SSO

Configure y pruebe el inicio de sesión único de Azure AD con Hiretual-SSO mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Hiretual-SSO.

Para configurar y probar el inicio de sesión único de Azure AD con Hiretual-SSO, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Hiretual-SSO](#configure-hiretual-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Hiretual-SSO](#create-hiretual-sso-test-user)** : para tener un homólogo de B.Simon en Hiretual-SSO que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Hiretual-SSO**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice el siguiente paso:

    a. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://api.hiretual.com/v1/users/saml/login/<teamId>`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.hiretual.com/`

    > [!NOTE]
    > Este valor no es real. Actualice este valor con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de Hiretual-SSO](mailto:support@hiretual.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Hiretual-SSO espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Hiretual-SSO espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre | Atributo de origen |
    | ---------- | --------- |
    | firstName | user.givenname |
    | title | user.jobtitle |
    | lastName | user.surname |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

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

En esta sección, va a permitir que B.Simon acceda a Hiretual-SSO mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Hiretual-SSO**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-hiretual-sso"></a>Configuración de Hiretual-SSO

1. Inicie sesión en su sitio de la empresa de Hiretual-SSO como administrador.

1. Vaya a **Security & Compliance** > **Inicio de sesión único**.

1. En la página **Autenticación SAML2.0**, realice los siguientes pasos:

    ![Captura de pantalla que muestra la configuración del inicio de sesión único.](./media/hiretual-tutorial/configuration.png "SSO Configuration")

    1. En el cuadro de texto **Dirección URL de inicio de sesión único de SAML2.0**, pegue el valor de **Dirección URL de acceso de usuario** que copió de Azure Portal.

    1. Copie el valor de **Id. de entidad** del archivo de metadatos y péguelo en el cuadro de texto **Emisor del proveedor de identidades**.

    1. Copie el **certificado X509** del archivo de metadatos y pegue el contenido en el cuadro de texto **Certificado**.

    1. Rellene los atributos necesarios manualmente según sus necesidades y haga clic en **Guardar**.

    1. Habilite el botón **Estado de conexión del inicio de sesión único**.

    1. Pruebe primero la integración de inicio de sesión único y, a continuación, habilite el botón **Inicio de sesión único iniciado por SP de administrador**. 

    > [!NOTE]
    > Si la configuración del inicio de sesión único tiene errores o tiene problemas para iniciar sesión en la extensión o aplicación web de Hiretual-SSO después de conectar el inicio de sesión único iniciado por SP de administrador, póngase en contacto con el [equipo de soporte técnico de Hiretual-SSO](mailto:support@hiretual.com).
    
### <a name="create-hiretual-sso-test-user"></a>Creación de un usuario de prueba de Hiretual-SSO

En esta sección, va a crear un usuario llamado Britta Simon en Hiretual-SSO. Trabaje con el [equipo de soporte técnico de Hiretual-SSO](mailto:support@hiretual.com) para agregar los usuarios a la plataforma de Hiretual-SSO. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Hiretual-SSO, donde puede poner en marcha el flujo de inicio de sesión.  

* Acceda directamente a la dirección URL de inicio de sesión de Hiretual-SSO y ponga en marcha el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal y debería iniciar sesión automáticamente en la instancia de Hiretual-SSO para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Hiretual-SSO en Mis aplicaciones, si está configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión y, si está configurado en modo IDP, debería iniciar sesión automáticamente en la instancia de Hiretual-SSO para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado el inicio de sesión único de Hiretual-SSO, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).