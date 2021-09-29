---
title: 'Tutorial: Integración de inicio de sesión único (SSO) de Azure Active Directory con Burp Suite Enterprise Edition | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Burp Suite Enterprise Edition.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c73c988eb6d950a77ccd626968fe48cc9c9f40f2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124773367"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-burp-suite-enterprise-edition"></a>Tutorial: Integración de inicio de sesión único (SSO) de Azure Active Directory con Burp Suite Enterprise Edition

En este tutorial aprenderá a integrar Burp Suite Enterprise Edition con Azure Active Directory (Azure AD). Al integrar Burp Suite Enterprise Edition con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Burp Suite Enterprise Edition.
* Permitir que los usuarios inicien sesión automáticamente en Burp Suite Enterprise Edition con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Burp Suite Enterprise Edition.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.


* Burp Suite Enterprise Edition es compatible con el inicio de sesión único (SSO) de **IDP**.

* Burp Suite Enterprise Edition admite el aprovisionamiento de usuarios **Just-In-Time**.


## <a name="adding-burp-suite-enterprise-edition-from-the-gallery"></a>Incorporación de Burp Suite Enterprise Edition desde la galería

Para configurar la integración de Burp Suite Enterprise Edition en Azure AD, tiene que agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Burp Suite Enterprise Edition** en el cuadro de búsqueda.
1. Seleccione **Burp Suite Enterprise Edition** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-burp-suite-enterprise-edition"></a>Configuración y prueba del inicio de sesión único de Azure AD para Burp Suite Enterprise Edition

Configure y pruebe el inicio de sesión único de Azure AD con Burp Suite Enterprise Edition mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Burp Suite Enterprise Edition.

Para configurar y probar el inicio de sesión único de Azure AD con Burp Suite Enterprise Edition, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Burp Suite Enterprise Edition](#configure-burp-suite-enterprise-edition-sso)** , para configurar los valores del inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba de Burp Suite Enterprise Edition](#create-burp-suite-enterprise-edition-test-user)** , para tener un homólogo de B.Simon en Burp Suite Enterprise Edition que esté vinculado a la representación de usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de  **Burp Suite Enterprise Edition**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configurar el inicio de sesión único con SAML**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<BURPSUITEDOMAIN:PORT>/saml`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<BURPSUITEDOMAIN:PORT>/api-internal/saml/acs`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Burp Suite Enterprise Edition](mailto:support@portswigger.net) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Burp Suite Enterprise Edition espera las aserciones de SAML en un formato específico. Para ello, es necesario agregar asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/edit-attribute.png)

1. Además de lo anterior, la aplicación Burp Suite Enterprise Edition espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.


    | Nombre | Atributo de origen|
    | ---------------| --------------- |    
    | Grupo | user.groups |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Burp Suite Enterprise Edition**, copie las direcciones URL adecuadas a sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Burp Suite Enterprise Edition mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Burp Suite Enterprise Edition**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-burp-suite-enterprise-edition-sso"></a>Configuración del inicio de sesión único de Burp Suite Enterprise Edition

Para configurar el inicio de sesión único en **Burp Suite Enterprise Edition**, es preciso enviar el **Certificado (Base64)** descargado y las direcciones URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de Burp Suite Enterprise Edition](mailto:support@portswigger.net). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-burp-suite-enterprise-edition-test-user"></a>Creación del usuario de prueba de Burp Suite Enterprise Edition

En esta sección, se creará un usuario llamado Britta Simon en Burp Suite Enterprise Edition. Burp Suite Enterprise Edition admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe ya en Burp Suite Enterprise Edition, se creará uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Burp Suite Enterprise Edition para la que haya configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Burp Suite Enterprise Edition en Mis aplicaciones, se debería iniciar sesión automáticamente en la instancia de Burp Suite Enterprise Edition para la que haya configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Burp Suite Enterprise Edition, puede aplicar el control de sesión que protege a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).