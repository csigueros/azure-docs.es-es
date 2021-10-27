---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con VECOS Releezme Locker management system'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y VECOS Releezme Locker management system.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2021
ms.author: jeedes
ms.openlocfilehash: caca1503e0ea4c272708bce34ecfd8993b4d6113
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007631"
---
# <a name="tutorial-azure-ad-sso-integration-with-vecos-releezme-locker-management-system"></a>Tutorial: Integración del inicio de sesión único de Azure AD con VECOS Releezme Locker management system

En este tutorial, aprenderá a integrar VECOS Releezme Locker management system con Azure Active Directory (Azure AD). Al integrar VECOS Releezme Locker management system con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a VECOS Releezme Locker management system.
* Permitir que los usuarios inicien sesión automáticamente en VECOS Releezme Locker management system con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en VECOS Releezme Locker management system.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* VECOS Releezme Locker management system admite el inicio de sesión único iniciado por **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-vecos-releezme-locker-management-system-from-the-gallery"></a>Adición de VECOS Releezme Locker management system desde la galería

Para configurar la integración de VECOS Releezme Locker management system en Azure AD, debe agregar esta aplicación desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **VECOS Releezme Locker management system** en el cuadro de búsqueda.
1. Seleccione **VECOS Releezme Locker management system** en el panel de resultados y, luego, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-vecos-releezme-locker-management-system"></a>Configuración y prueba del inicio de sesión único de Azure AD para VECOS Releezme Locker management system

Configure y pruebe el inicio de sesión único de Azure AD con VECOS Releezme Locker management system mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de VECOS Releezme Locker management system.

Para configurar y probar el inicio de sesión único de Azure AD con VECOS Releezme Locker management system, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de VECOS Releezme Locker management system](#configure-vecos-releezme-locker-management-system-sso)** : para configurar los valores del inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en VECOS Releezme Locker management system](#create-vecos-releezme-locker-management-system-test-user)** : para tener un homólogo de B.Simon en VECOS Releezme Locker management system vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **VECOS Releezme Locker management system**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos: 

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba la dirección URL `https://au.releezme.net/`.

    b. En el cuadro de texto **URL de respuesta**, escriba la siguiente dirección URL: `https://au.releezme.net/Saml2/Acs`
    
    c. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL:  
    `https://sso-na.releezme.net`

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

En esta sección, concederá a B.Simon acceso a VECOS Releezme Locker management system mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **VECOS Releezme Locker management system**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-vecos-releezme-locker-management-system-sso"></a>Configuración del inicio de sesión único de VECOS Releezme Locker management system

Para configurar el inicio de sesión único en **VECOS Releezme Locker management system**, es preciso enviar la **dirección URL de metadatos de federación de la aplicación**  al [equipo de soporte técnico de VECOS Releezme Locker management system](mailto:servicedesk@vecos.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-vecos-releezme-locker-management-system-test-user"></a>Creación de un usuario de prueba de VECOS Releezme Locker management system

En esta sección, creará un usuario llamado Britta Simon en VECOS Releezme Locker management system. Trabaje con el [equipo de soporte técnico de VECOS Releezme Locker management system](mailto:servicedesk@vecos.com) para agregar los usuarios a la plataforma de esta aplicación. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de VECOS Releezme Locker management system, donde puede comenzar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de VECOS Releezme Locker management system y comience desde allí el flujo de inicio de sesión.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de VECOS Releezme Locker management system en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de esta aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Cuando haya configurado VECOS Releezme Locker management system, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).