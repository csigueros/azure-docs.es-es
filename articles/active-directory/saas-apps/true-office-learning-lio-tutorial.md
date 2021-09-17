---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con True Office Learning - LIO | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y True Office Learning - LIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: jeedes
ms.openlocfilehash: 58b3e6067ccc9b4e0b88e497008c904e79dc8bbc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786008"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-true-office-learning---lio"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con True Office Learning - LIO

En este tutorial, obtendrá información sobre cómo integrar True Office Learning - LIO con Azure Active Directory (Azure AD). Al integrar True Office Learning - LIO con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a True Office Learning - LIO.
* Permitir que los usuarios inicien sesión automáticamente en True Office Learning - LIO con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en True Office Learning - LIO.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* True Office Learning - LIO admite el inicio de sesión único iniciado por **SP**.

## <a name="add-true-office-learning---lio-from-the-gallery"></a>Incorporación de True Office Learning - LIO desde la galería

Para configurar la integración de True Office Learning - LIO en Azure AD, tendrá que agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **True Office Learning - LIO** en el cuadro de búsqueda.
1. Seleccione **True Office Learning - LIO** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-true-office-learning---lio"></a>Configuración y prueba del inicio de sesión único de Azure AD para True Office Learning - LIO

Configure y pruebe el inicio de sesión único de Azure AD con True Office Learning - LIO mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de True Office Learning - LIO.

Para configurar y probar el inicio de sesión único de Azure AD con True Office Learning - LIO, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en True Office Learning - LIO](#configure-true-office-learning---lio-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en True Office Learning - LIO](#create-true-office-learning---lio-test-user)** : para tener un homólogo de B.Simon en True Office Learning - LIO que esté vinculado a la representación de dicho usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **True Office Learning - LIO**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    1. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://learn-sso.trueoffice.com/simplesaml/module.php/saml/sp/metadata.php/<CUSTOMER_NAME>-sp`

    1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://learn-sso.trueoffice.com/<CUSTOMER_NAME>`
    
        > [!NOTE]
        > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico de clientes de True Office Learning - LIO](mailto:service@trueoffice.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar True Office Learning - LIO**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, permitirá que B.Simon acceda a True Office Learning - LIO mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **True Office Learning - LIO**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-true-office-learning---lio-sso"></a>Configuración del inicio de sesión único de True Office Learning - LIO

Póngase en contacto con el [equipo de soporte técnico de True Office Learning - LIO](mailto:service@trueoffice.com) con preguntas de configuración o para solicitar una copia de los metadatos. En la solicitud, indique la siguiente información:
* Nombre de la empresa.
* CompanyID (si se conoce).
* Configuración existente o nueva.

### <a name="create-true-office-learning---lio-test-user"></a>Creación de un usuario de prueba de True Office Learning - LIO

En esta sección, creará un usuario llamado Britta Simon en True Office Learning - LIO. Trabaje con el [equipo de soporte técnico de True Office Learning - LIO](mailto:service@trueoffice.com) para agregar los usuarios a la plataforma de True Office Learning - LIO. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante las opciones siguientes:

* En Azure Portal, seleccione **Probar esta aplicación**. Esta acción le redirigirá a la dirección URL de inicio de sesión de True Office Learning - LIO, donde puede comenzar el flujo de inicio de sesión. 
* Vaya directamente a la dirección URL inicio de sesión de True Office Learning - LIO e inicie el flujo de inicio de sesión desde ese sitio.
* Puede usar Mis aplicaciones de Microsoft. Al seleccionar el icono True Office Learning - LIO en Mis aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de True Office Learning - LIO. Para obtener más información sobre Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado True Office Learning - LIO, puede aplicar el control de sesión, que protege la filtración e infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
