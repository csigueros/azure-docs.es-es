---
title: 'Tutorial: Integración de inicio de sesión único (SSO) de Azure Active Directory con Software AG Cloud'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Software AG Cloud.
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: 900cb7892331db3e78314a5de6bfb432f40990c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558488"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Tutorial: Integración de inicio de sesión único (SSO) de Azure Active Directory con Software AG Cloud

En este tutorial, aprenderá a integrar Software AG Cloud con Azure Active Directory (Azure AD). Al integrar Software AG Cloud con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Software AG Cloud.
* Permitir que los usuarios inicien sesión automáticamente en Software AG Cloud con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Software AG Cloud.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Software AG Cloud admite el inicio de sesión único iniciado por **SP**.
* Software AG Cloud admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Incorporación de Software AG Cloud desde la galería

Para configurar la integración de Software AG Cloud en Azure AD, deberá agregar Software AG Cloud desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Software AG Cloud** en el cuadro de búsqueda.
1. Seleccione **Software AG Cloud** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Configuración y prueba de inicio de sesión único de Azure AD para Software AG Cloud

Configure y pruebe el inicio de sesión único de Azure AD con Software AG Cloud mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Software AG Cloud.

Para configurar y probar el inicio de sesión único de Azure AD con Software AG Cloud, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración de inicio de sesión único de Software AG Cloud](#configure-software-ag-cloud-sso)** , para configurar los valores de inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba de Software AG Cloud](#create-software-ag-cloud-test-user)** , para tener un homólogo de B.Simon en Software AG Cloud que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal en la página de integración de aplicaciones de **Software AG Cloud**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: 

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón:

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Software AG Cloud](mailto:support@softwareag.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar Software AG Cloud**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Software AG Cloud mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Software AG Cloud**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-software-ag-cloud-sso"></a>Configuración de inicio de sesión único de Software AG Cloud

1. Abra una nueva ventana del explorador web e inicie sesión en el sitio web de Software AG Cloud como administrador.

1.  Haga clic en **Administration** (Administración).

    ![Configuración de la administración de Software AG Cloud](./media/software-ag-cloud-tutorial/admin.png)

1. Vaya a **Single-sign on > Add identity provider** (Inicio de sesión único > Agregar proveedor de identidades).

    ![Configuración del proveedor de identidades de Software AG Cloud](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. En la página siguiente, realice estos pasos.

    ![Pasos de configuración de Software AG Cloud](./media/software-ag-cloud-tutorial/saml-1.png)

    a. En el cuadro **Identity provider display name** (Nombre para mostrar del proveedor de identidades), proporcione un nombre válido como `azure ad`.

    b. En el cuadro de texto **Identity provider unique identifier for use in Software AG Cloud redirect URI** (Identificador único del proveedor de identidades para su uso en el identificador URI de redirección de Software AG Cloud), escriba un nombre único para el proveedor de identidades. El campo **Software AG Cloud redirect URI** (Identificador URI de redirección de Software AG Cloud) se actualizará y rellenará con el identificador URI. Copie este identificador URI y úselo para configurar el campo **Id. de entidad** y otra información en Azure Portal según los patrones definidos.

    c. Importe el archivo **Federation Metadata XML** (XML de metadatos de federation) en **Identity provider configuration** (Configuración del proveedor de identidades) y haga clic en **Next** (Siguiente).

    d. Vaya a la página **Configuración** y complete los campos según sea necesario.

### <a name="create-software-ag-cloud-test-user"></a>Creación de un usuario de prueba de Software AG Cloud

En esta sección creará un usuario llamado Britta Simon en Software AG Cloud. Software AG Cloud admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si no existe un usuario en Software AG Cloud, se crea uno nuevo después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

Suponiendo que Microsoft Azure esté configurado como un proveedor en Software AG Cloud, vaya a `www.softwareag.cloud`, haga clic en el botón de inicio de sesión y escriba el nombre del entorno. En la siguiente pantalla, haga clic en el vínculo "Log in with \<IDP NAME\>" (Iniciar sesión con <nombre_del_IdP>) y escriba las credenciales. Una vez autenticado, se iniciará sesión y se le dirigirá a la página principal de Software AG Cloud.

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Software AG Cloud, puede aplicar el control de sesión que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).